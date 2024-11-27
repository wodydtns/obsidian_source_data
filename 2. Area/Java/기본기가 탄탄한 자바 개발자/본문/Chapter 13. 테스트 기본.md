## 13.1 테스트하는 이유
>[!important]
> - 코드를 테스트하는 동기는 무엇인가?
> - 어떤 기술을 사용하면 그 목표를 가장 정확하고 깔끔하게 달성할 수 있는가?

- 개별 메서드의 로직이 올바른지 확인
- 코드 내 두 객체 간의 상호작용을 확인
- 라이브러리나 다른 외부 의존성이 예상대로 동작하는 지 확인
- 시스템의 일부에서 생성되거나 소비되는 데이터가 유효한지 확인
- 시스템이 외부 구성 요소(==예: 데이터베이스==)와 올바르게 작동하는지 확인
- 시스템의 최종적인 동작이 중요한 비지니스 시나리오를 충족하는지 확인
- 나중에 유지 보수하는 사람들을 위해 가정을 문서화하기(테스트는 주석이나 문서와 같은 방식으로 동기화되지 않기 때문)
- 강한 결합과 객체 책임을 노출해 시스템 설계에 영향을 미침
- 사람이 직접 실행하는 릴리스 후 체크리스트 자동화하기
- 작위적인 입력을 통해 코드에서 예상치 못한 코너 케이스 찾기

## 13.2 테스트 방법
- 테스팅 피라미드
	![[Pasted image 20241111141004.png]]

	- 단위테스트
		- 시스템의 한 측면을 집중적으로 테스트하는 것
		- 빠름
			- 테스트에 외부 의존성이 없으면 실행하는 데 오래 걸리지 않는다
		- 집중
			- 하나의 '단위' 코드에 대해서만 이야기하면 설정이 많은 대규모 테스트보다 테스트가 표현하는 것이 더 명확해지는 경우가 많다
		- 안정적인 실패
			- 외부 의존성, 특히 외부 상태에 대한 의존성을 최소화하면 단위 테스트가 결정론적으로 만들어져 예측 가능하게 된다
		- 단위테스트의 한계
			- 높은 결합도
				- 단위 테스트는 정의상 구현과 밀접한 관련이 있으므로 구현 선택에 너무 긴밀하게 결합되는 경향이 있다. 기반 구현이 변경될 때 전체 단위 테스트 세트다 무효화되는 경우가 흔하다
			- 의미 있는 상호작용 부재
				- 사실 프로그램의 실제 작업에는 종속적인 부분 간의 상호작용이 포함돼 있는데 단위테스트는 이 부분을 놓치게 된다
			- 내부 초점
				- 단일 메서드의 정확성이 사용자의 만족으로 이어지는 경우는 드물다
	- 통합 테스트
		- 시스템 내의 다양한 부분이 원활하게 연결되는지 확인하는 것에 중점을 둔다
		- 서로 다른 컴포넌트들이 제대로 협력하며 통합되는지를 검증
		- 예제
			- 데이터베이스 인스턴스가 필요하고 데이터 액세스 코드에 호출을 수행한다
			- 특별한 내부 HTTP 서버를 시작해서 요청을 테스트한다
			- 다른 서비스에 실제 호출을 수행한다(==테스트 환경 여부에 관계없이==)
		- 장점
			- 더 넓은 커버리지
				- 통합 테스트는 필연적으로 더 많은 코드와 종속 라이브러리의 코드를 작동시킨다
			- 더 많은 유효성 검사
				- 특정 유형의 오류는 실제 의존성을 사용할 때만 감지가 가능할 수도 있다
		- 단점
			- 느린 테스트
				- 메모리에서 값을 읽는 대신 실제 데이터베이스로 이동하면 느려진다. 수백개 또는 수천 개의 테스트에서 이를 곱하면 기다려야 하는 시간이 상당히 길어질 수 있다
			- 결과의 불확실성
				- 외부 의존성은 중요한 상태가 테스트 실행 간에 변경될 수 있는 가능성을 증가시킨다. 예를 들어 데이터베이스에서 남아 있는 레코드는 SQL 문에서 반환되는 내용을 변경할 수 있다
			- 잘못된 신뢰
				- 통합 테스트에서는 때론 메인 시스템의 주요 의존성과 약간 다른 의존성을 사용할 수 있다. 예를 들어 테스트 데이터베이스가 프로덕션과 다른 버전인 경우, 통합 테스트는 모든 것이 좋다는 잘못된 표시를 할 수 있다
	- E2E 테스트(End-to-End test)
		- 시스템의 전체 사용자 경험을 복제하기 위한 목적으로 진행
		- 프로그래밍 방식으로 웹브라우저나 다른 애플리케이션을 조작하거나, 테스트 환경에서 완전히 배포된 서비스 인스턴스를 실행하는 것을 의미한다
		- 장점
			- 실제 사용자 경험
				- 좋은 E2E 테스트는 사용자가 보는 것과 비슷하다. 이를 통해 사용자의 높은 수준의 기대치를 직접 검증할 수 있다
			- 실제 환경
				- 많은 E2E 테스트가 테스트, 스테이징, 프로덕션 환경에서 실행된다. 이ㅏ를 통해 코드가 편안하고 세심하게 관리되는 빌드 환경 밖에서도 작동하는지 검증할 수 있다
			- 사용 가능한 UI
				- 웹브라우저를 구동하는 방식 같은 많은 E2E 테스트 접근 방식은 다른 곳에서는 검증하기 어려울 수 있는 시스템의 측면을 확인할 수 있다
		- 단점
			- 더욱 느려진 테스트
				- 많은 상황에서 웹브라우저를 제어해서 사이트를 탐색하는 E2E 테스트는 실행하는 데 훨씬 더 오랜 시간이 걸릴 수밖에 없다
			- 불안정한 테스트
				- 전통적으로 E2E 테스트, 특히 UI 테스트의 도구들은 불안정성이 있어 다시 시도하거나 오랜 대기 시간을 필요로 하므로 불필요한 실패를 피해야했다
			- 취약한 테스트
				- E2E 테스트는 피라미드의 최상단에 위치하기 때문에 아래 레벨에서 변경하면 오류가 발생할 수 있다
			- 더 어려운 디버깅
				- E2E 테스트는 종종 테스트를 제어하는 또 다른 레이어를 도입해서 어떤 문제가 발생했는지 파악하는 것이 어려울 수 있다

## 13.3 테스트 주도 개발
- Test-Driven Development
	- 구현을 진행하는 동안에 테스트를 작성하며, 테스트가 코드의 설계에 영향을 미치는 것
	- '테스트를 먼저 작성'하는 방법은 실제로 구현을 제공하기 전에 실패하는 테스트를 작성한 다음, 필요한 대로 리팩토링하는 것
	- 테스트 주도 개발이 주는 이점
		- 더 깔끔한 코드
			- 필요한 코드만 작성할 수 있다
		- 더 좋은 디자인
			- 일부 개발자는 테스트 주도 개발을 테스트 중심 디자인이라고 부른다
		- 더 좋은 API
			- 테스트가 구현에 대한 클라이언트 역할을 함으로써 잘못된 부분을 조기에 발견할 수 있다
		- 유연성 향상
			- 테스트 주도 개발은 인터페이스에 대한 코딩을 장려한다
		- 테스트를 통한 문서화
			- 테스트 없이는 코드를 작성할 수 없으므로 테스트에 모든 사용 예제가 있다
		- 빠른 피드백
			- 버그를 프로덕션 환경이 아닌 지금 바로 파악할 수 있다

### 13.3.1 테스트 주도 개발 개요
- 테스트 주도 개발은 단위 테스트 수준에서 가장 쉽게 사용할 수 있으므로, 테스트 주도 개발에 익숙하지 않다면 단위 테스트 수준에서 시작하는 것이 좋다

### 13.3.2 단일 사용 사례로 설명하는 테스트 주도 개발 예제
- 테스트 주도 개발 예제
	- 초기 비지니스 규칙
		- 티켓의 기본 가격은 30달러다
		- 총 수익 = 판매된 티켓 수 * 가격
		- 극장 좌석 수 = 100석
	- 테스트 주도 개발의 세 가지 기본 단계 - 레드, 그린, 리팩터링
		- 레드
			- 작동하지 않는 작은 테스트(실패하는 테스트)를 작성한다
		- 그린
			- 가능한 한 빨리 테스트를 통과시킨다(통과하는 테스트)
		- 리팩터
			- 중복을 제거한다(정제된 통과하는 테스트)
- 실패하는 테스트 작성하기(레드)
```Java
import org.junit jupiter.api.BeforeEach;
import org.junit jupiter.api.Test;

import java.math.BigDecimal;

import static org.junit jupiter.api.Assertions.*;

public class TicketRevenueTest {
	private TicketRevenue venuRevenue;

	@BeforeEach
	public void setUp(){
		venuRevenue = new TicketRevenue();
	}

	// 티켓이 한장 팔렸을 경우를 가정
	@Test
	public void oneTicketSoldIsThrityInRevenue(){
		var expectedRevenue = new BigDecimal("30");
		assertEquals(expectedRevenue, venuRevenue.estimateTotalRevenue(1));
	}
}

// TickRevenue
public class TickRevenue {
	public BigDecimal estimateTotalRevenue(int i){
		return BigDecimal.ZERO;
	}
}
```
- 통과하는 테스트 작성하기(그린)
```java
import java.math.BigDecimal;

public class TicketRevenue {
	public BigDecimal estimateTotalRevenue(int numberOfTicketSold){
		BigDecimal totalRevenue = BigDecimal.ZERO;
		if (numberOfTicketSold == 1){
			// 테스트를 통과하는 구현
			totalRevenue = new BigDecimal("30")
		}
		return totalRevenue;
	}
}
```
- 테스트 리팩터링
```java
import java.math.BigDecimal;

public class TicketRevenue {

	// 명명된 개념이 아닌 숫자를 그대로 사용하지 않기
	private final static int TICKET_PRICE = 30;

	public BigDecimal estimateTotalRevenue(int numberOfTicketSold){
		BigDecimal totalRevenue = BigDecimal.ZERO;
		if (numberOfTicketSold == 1){
			// 테스트를 통과하는 구현
			totalRevenue = new BigDecimal(TICKET_PRICE * numberOfTicketSold);
		}
		return totalRevenue;
	}
}
```
- 여러 사용 사례가 있는 테스트 주도 개발 예제
```Java
import org.junit jupiter.api.BeforeEach;
import org.junit jupiter.api.Test;

import java.math.BigDecimal;

import static org.junit jupiter.api.Assertions.*;

public class TicketRevenueTest {
	private TicketRevenue venuRevenue;
	private BigDecimal expectedRevenue;

	@BeforeEach
	public void setUp(){
		venuRevenue = new TicketRevenue();
	}

	// 음수 판매 경우
	@Test
	public void failIfLessThanZeroTicketsAreSold(){
		assertThrows(IllegalArgumentException.class,
					() -> venuRevenue.estimateTotalRevenue(-1));
	}

	// 0개 판매 경우
	@Test
	public void zeroSalesEqualsZeroRevenue(){
		assertEquals(BigDecimal.ZERO, venuRevenue.estimateTotalRevenue(-1))
	}

	// 티켓이 한장 팔렸을 경우를 가정
	@Test
	public void oneTicketSoldIsThrityInRevenue(){
		var expectedRevenue = new BigDecimal("30");
		assertEquals(expectedRevenue, venuRevenue.estimateTotalRevenue(1));
	}
}
```
- 테스트를 기반으로 만든 코드
```Java
import java.math.BigDecimal;

public class TicketRevenue {
	public BigDecimal estimateTotalRevenue(int numberOfTicketsSold)
		throws IllegalArgumentException {

		if (numberOfTicketsSold < 0){
			throws new IllegalArgumentException (
				"Must be -> - 1"
			);
		}

		if (numberOfTicketsSold == 0){
			return BigDecimal.ZERO;
		}

		if (numberOfTicketsSold == 1){
			return new BigDecimal("30");
		}

		if (numberOfTicketsSold == 101){
			throws new IllegalArgumentException (
				"Must be < 101"
			);
		}

		return new BigDecimal(30 * numberOfTicketsSold);
	}
}
```

## 13.4 테스트 더블
- 테스트 더블
	- 시스템의 일부분이 완전히 준비되지 않았거나 테스트하기 어려운 상황에서 그 대안으로 사용될 수 있는 '가짜' 컴포넌트
	- 실제 객체의 행동을 모방하며, 테스트가 특정 조건과 상호작용을 쉽게 재현하고 검증할 수 있도록 한다
- 테스트 더블의 네 가지 타입

| 타입        | 설명                                                                      |
| --------- | ----------------------------------------------------------------------- |
| 더미(dummy) | 전달되지만 실제로 사용되지 않느 객체<br>주로 메서드의 매개변수 목록을 채우기 위해 사용                      |
| 스텁(stub)  | 항상 미리 정의된 동일한 응답을 반환하는 객체<br>때로는 어떤 더미 상태도 가질 수 있다                      |
| 페이크(fake) | 실제로 동작하는 구현<br>프로덕션 품질이나 설정은 아님<br>테스트를 위한 간소화된 동작 구현으로, 실제 구현을 대체      |
| 목(mock)   | 기대하는 동작과 미리 정의된 응답을 가진 객체<br>테스트 코드와 상호작용해서 기대한 동작이 제대로 수행되는지 확인하는 데 사용 |

### 13.4.1 더미 객체
- dummy object
	- 가장 사용하기 쉬운 유형
	- 매개변수 목록을 채우거나 객체가 사용되지 않을 것을 알고 있는 일부 필수 필드 요구 사항을 충족하는 데 도움이 되도록 설계
	- 대부분의 경우 빈 객체를 전달할 수도 있다

### 13.4.2 스텁 객체
- stub object
	- 매번 동일한 응답을 반환하는 객체
	- 실제 구현을 대체하려는 경우 사용

### 13.4.3 페이크 객체
- fake object
	- 거의 본래 프로덕션 코드와 동일한 작업을 수행하지만 테스트 요구 사항을 충족하기 위해 몇 가지 단축을 적용한 향상된 스텁
	- 실제 구현에서 사용할 실제 서드파티 시스템이나 의존성과 매우 유사한 무언가와 상호작용하도록 코드를 실행하고자 할 때 특히 유용

### 13.4.4 목 객체
- mock object
	- 프로그래밍이 가능한 스텁으로 생각할 수 있는 특수한 타입의 테스트 더블
	- 예시 - Mockito
```Java
import org.junit.jupiter.api.Test;
import java.math.BigDecimal;
import static org.junit.jupiter.api.Assertion.*;
import static org.mockito.Mockito.*;

public class TicketTest {

	@Test
	public void tenPercentDiscount(){
		// mock 객체 생성
		// mock 하려는 타입의 클래스 객체 전달
		Price price = mock(Price.class);

		// 기록하려는 동작 지정
		when(price.getInitialPrice())
			// 예상되는 결과 지정
			.thenReturn(new BigDecimal("10"));

		Ticket ticket = new Ticket(price, new BigDecimal("0.9"));
		// 예상된 메서드 호출했는지 확인
		assertEquals(new BigDecimal("9.0"), ticket.getDiscountPrice());

		verify(price).getInitialPrice();
	}
}
```

### 13.4.5 목 테스트의 문제점
- 목 테스트의 가장 큰 문제 중 하나는 목 테스트가 가짜이기 때문에 실제 프로덕션 시스템과 동작이 다를 수 있다는 것
- 각 테스트 세드에서 무엇을 테스트하는지 명확히 인지한다면, 우리는 유닛 테스트를 지역화된 특정 논리에 집중시킬 수 있으며, 외부 의존성과의 상호작용을 다루는 것은 다른 곳에서 통합 테스트에 활용할 수 있다
- 만약 목 테스트를 모든 곳에서 사용한다면, 실제 코드의 각 줄마다 테스트 코드에서 예상하는 삼수 호출을 정확하게 다시 나열해야 한다
	- 이렇게 하면 테스트 코드가 실제 코드의 동작을 따라가며 거의 동일한 구조로 작성되어, 코드를 변경 시 예상치 못한 다수의 테스트 코드 수정이 필요해질 수도 있다
- 테스트가 실행되기 전 복잡한 설정이 많이 필요한 경우에도 문제가 발생할 수 있다
	- 의존성 주입과 함께 목 테스트를 사용하는 경우, 클래스에 의존성을 많이 쌓아 놓는 것이 눈에 잘 띄지 않게 된다 
	- 만약 테스트 설정이 실행과 결과를 검증하는 데 필요한 코드보다 길다면, 클래스가 지나치게 복잡하고 리팩터링이 필요한 상태일 가능성이 크다

## 13.6 Junit 4에서 5로
- Junit 5의 가장 큰 변화
	- 패키징
		- 더 집중된 패키지로 세분화
		- Junit4의 Hamcrest에 대한 외부 의존성 제거
	- 주요 의존성
		- ==org.junit.jupiter.junit-jupiter-api==
			- 테스트 생성 시 필요한 애너테이션과 도우미를 제공하기 위해 테스트 코드에서 참조한다
		- ==org.junit.jupiter.junit-jupiter-engine==
			- JUnit 5 테스트를 실행하는 기본 엔진이며 실행 시 필요하고, 다른 테스트 실행기로 교체할 수 있다
	- 클래스 이름 변경
		- @Before -> @BeforeEach
		- @After -> @AfterEach
		- @BeforeClass -> @BeforeAll
		- @AfterClass -> @AfterAll
		- @Category -> @Tag
		- @Ignored -> @Disabled
		- @RunWith, @Rule, @ClassRule은 새로운 확장 모델로 대체