## 14.1 테스트컨테이너를 이용한 통합 테스트
### 14.1.1 테스트컨테이너 설치
- 테스팅에서 컨테이너를 활용하는 가장 간단한 방법은 testcontainer 라이브러리를 사용하는 것
- ==org.testcontainers.testcontainers==

### 14.1.2 레디스 예제
- 예시 코드
```Java
package com.wellgrounded;

import redis.client.jedis.Jedis;

import java.math.BigDecimal;

public class CachedPrice implements Price {
	private final Price priceLookup;
	private final Jedis cacheClient;

	private static final String priceKey = "price";

	CachedPrice(Price priceLookup, Jedis cacheClient){
		this.priceLookup = priceLookup;
		this.cacheClient = cacheClient;
	}

	@Override
	public BigDecimal getInitialPrice(){
		String cachedPrice = cacheClient.get(priceKey);
		if (cachedPrice != null){
			return new BigDecimal(cachedPrice);
		}

		BigDecimal price =
			priceLookup.getInitialPrice();
		cacheClient.set(priceKey,
						price.toPlainString());
		return price;
	}
}
```
- 테스트 코드
```Java
package com.wellgrounded;

import org.junit.jupiter.api.Test;
import org.testcontainers.containers.GenericContainer;
import org.testcontainers.junit.jupiter.*;
import org.testcontainers.utility.DockerImageName;
import redis.client.jedis.*;

import java.math.BigDecimal;

import static org.junit.jupiter.api.Assertions.assertEquals;

@Testcontainers
public class CachedPriceTest {
	private static final DockerImageName imageName =
		DockerImageName.parse("redis:6.2.3-alpine");

	@Container
	public static GenericContainer redis = new GenericContainer(imageName)
				.withExposedPorts(6379);
	@BeforeAll
	public void setUp(){
		redis.start();
	}

	@Test
	public void cached(){
		var jedis = getJedisConnection();
		jedis.set("price","20");

		CachedPrice price =
			new CachedPrice(new StubPrice(), jedis);
		BigDecimal result = price.getInitialPrice();

		assertEquals(new BigDecimal("20"), result);
	}

	@Test
	public void noCache(){
		var jedis = getJedisConnection();
		jedis.set("price","20");

		CachedPrice price =
			new CachedPrice(new StubPrice(), jedis);
		BigDecimal result = price.getInitialPrice();

		assertEquals(new BigDecimal("10"), result);
	}

	private Jedis getJedisConnection(){
		HostAndPort hostAndPort = 
			new HostAndPort(redis.getHost(),redis.getFirstMappedPort());
		return new Jedis(hostAndPort);
	}
}

```
		- @Testcontainers 애너테이션를 적용해 테스트 클래스 전체에 적용해 라이브러리에 테스트 실행 중 필요한 컨테이너를 감시하도록 알림
		- @Container로 표시된 필드는 특정 컨테이너 이미지를 요청해 6379를 사용해 시작하도록 요청
		- 
-  테스트 컨테이너 실행
	![[Pasted image 20241112092343.png]]

### 14.1.3 컨테이너 로그 수집
- 컨테이너 로그 수집 예시