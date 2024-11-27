## 11.1 기본기가 탄탄한 개발자에게 빌드 도구가 왜 중요한가
>[!important]
>빌드 도구의 사용 이유
>1. 지루한 작업의 자동화
>2. 의존성 관리
>3. 개발자들 간의 일관성 보장

### 11.1.1 지루한 작업의 자동화
- 빌드 도구는 코드를 찾는 데 대한 기본값을 제공하며, 비표준적인 레이아웃을 갖추었다면 쉽게 구성할 수 있도록 해준다

### 11.1.2 의존성 관리
- 자바 초창기에는 라이브러리 사용을 위해 해당 JAR 파일을 어디에서 다운로드 받아 애플리케이션의 클래스패스로 넣어야 했음
	- 신뢰할 수 있는 중앙 저장소가 없어, 자주 사용하지 않는 의존성에 대한 JAR을 찾기 위해 들여야하는 시간이 많았음
- 메이븐 Central
	- 자바 의존성에 가장 일반적으로 사용되는 레지스트리
	- 빌드 도구는 프로젝트 간 아티팩트(==artifact==)를 공유해 수고를 줄여주는 몇 가지 방법 개발
		- 캐싱한 로컬 저장소를 사용하면 다른 프로젝트에서 동일한 라이브러리가 필요해도 다시 다운로드 할 필요가 없다
			![[Pasted image 20241108092415.png]]
	- 레지스트리는 전이적 의존성(==transitive dependency==)을 더 잘 관리하게 해줌
		- JAR 파일은 압축 파일일 뿐이며, JAR의 의존성을 설명하는 메타데이터가는 없다
		- 즉 JAR의 의존성은 JAR에 있는 모든 클래스의 모든 의존성을 합친 것
		- 이것은 두 가지 사항을 의미
			- 의존성 정보를 담은 외부 소스의 필요성
			- 프로젝트 확장으로 인한 점진적인 전이적 의존성 그래프의 복잡도 증가
- 충돌 발생
	- 의존성 충돌(==dependency conflict==)
		- 명시적으로 ==lib-a== 의 2.0 버전 요청했지만, 의존성인 ==lib-b== 는 오래된 버전인 1.0을 요청
	- 라이브러리 버전 불일치의 문제 범주
		- 1. 버전 간에 동작만 변경되는 안정적인 API
		- 2. 버전 간에 새로운 클래스나 메서드가 나타나는 추가 API
		- 3. 버전 간에 메서드 서명이나 인터페이스 확장 변경이 있는 변경된 API
		- 4. 버전 간에 클래스 또는 메서드가 제거된 API
		- 1,2번의 경우 빌드 도구가 어떤 버전의 의존성을 선택했는지 알 수 없을 수도 있다
		- 3번의 경우 lib-a 2.0이 lib-b의 의존하는 메서드 서명을 변경한 경우, lib-b가 해당 메서드를 호출 시 ==NoSuchMethodError== 예외 발생
		- 4번의 경우도 ==NoSuchMethodError== 예외 발생 & 클래스 제거 또는 이름 변경으로 ==NoClassDefFoundError== 발생 & 인터페이스 제거로 ==ClassCaseException== 발생 가능성이 있음
	- semantic versioning
		- 라이브러리 버전 불일치 문제 해결 방법
		- 주요 사항
			- Major 버전 증가(==1.x -> 2.x==)
				- API에 큰 변경이 있는 경우 3이나 4와 같은 경우
			- Minor 버전 증가(1.1 -> 1.2)
				- 역호환성을 유지하면서 추가한 경우 2와 같은 경우
			- 패치(patch) 버전 증가 : 버그 수정(1.1.0 -> 1.1.1)
### 11.1.3 개발자 간의 일관성 보장
- 코드 커버리지(==code coverage==) 도구
	- 어떤 코드가 테스트에 포함되고 어떤 코드가 포함되지 않았는지 감지하는 데 핵심적인 역할
- 정적 분석 도구
	- 일반적인 패턴 감지부터 사용하지 않는 변수 스니핑까지, 컴퓨터는 승인하지만 프로덕션 환경에서 문제가 될 수 있는 코드의 측면을 검증

## 11.2 메이븐
### 11.2.1 빌드 생명 주기(build lifecycle)
- 메이븐의 생명 주기
	- 일반적으로 빌드에서 기대하는 단계들을 포함하는 기본적인 생명 주기(default lifecycle)을 가지고 있다
	- 단계
		- validate
			- 프로젝트 구성이 올바르며 빌드할 수 있는지 확인
		-   compile
			- 소스 코드 컴파일
		- test
			- 단위 테스트 실행
		- package
			- JAR 파일과 같은 아티팩트 생성
		- verify
			- 통합 테스트 실행
		- install
			- 패키지를 로컬 저장소에 설치
		- deploy
			- 패키지 결과를 다른 사용자에게 제공
			- 일반적으로 CI 환경에서 실행
	- 모델
		- 목표
			- 구체적인 작업
			- 그 작업을 어떻게 실행해야 하는지에 대한 구현을 포함
- 기본 생명주기 외 생명주기
	-  클린 생명 주기(clean lifecycle)
		- 중간 빌드 결과 제거
	- 사이트 생명 주기(site lifecycle)
		- 문서 생성

### 11.2.2 명령 및 POM 소개
- 최소한의  POM.XML
```xml
<project>
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.wellgrounded</groupId>
	<artifactId>example</artifactId>
	<version>1.0-SNAPSHOT</version>
	<name>example</name>

	<properties>
		<maven.compiler.source>11</maven.compliler.source>
		<maven.compiler.target>11</maven.compiler.target>
	</properties>
</project>
```
	- GAV 좌표(group, artifact, version)
		- 특정 패키지의 특정 릴리스를 전 세계적으로 고유하게 식별
		- 의존성을 찾기 위한 주소 역할
		- groupId
			- 라이브러리를 담당하는 회사, 조직, 오픈소스 프로젝트
		- artifactId
			- 특정 라이브러리의 이름

### 11.2.3 빌드
- ==mvn compile==
	- 메이븐은 출력을 target 디렉토리를 기본으로 한다

### 11.2.4 manifest 조작하기
- manifest 작성 예시
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>2.4</version>
            <configuration>
                <archive>
                    <!-- JAR의 manifest 내용을 구성-->
                    <manifest>
                        <addClasspath>true</addClasspath>
                        <mainClass>com.wellgrounded.Main</mainClass>
                        <!-- 자동 모듈 이름을 구성-->
                        <Automatic-Module-Name>
                            com.wellgrounded
                        </Automatic-Module-Name>
                    </manifest>
                </archive>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 11.2.5 다른 언어 추가하기
- 코틀린을 메이븐으로 컴파일 할 경우 pom.xml에 kotlin-maven-plugin을 추가해야한다

### 11.2.6 테스트
- 메이븐의 테스트
	- ==test==
		- 일반적인 단위 테스트에 사용
	- ==integration-test==
		- 최종 산출물에 대한 E2E(==end-to-end==) 유효성 검사를 수행하기 위해 JAR과 같은 아티팩트를 구성한 후 실행
	- 테스트 실행 추가 방법
```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.8.1</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.8.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
	- maven-surefire-plugin은 test 단계 동알 표준 위치인 src/test/*에 있는 모든 단위 테스트를 실행
	- maven-failsafe-plugin은 integration-test단계 활용 시 사용
	- 통합테스트 실행 시 maven verify가 mven intergation-test보다 좋음
		- maven verify에는 post-integration-test가 포함되어 있다

### 11.2.7 의존성 관리
- 의존성 해결(==dependency resolution==)
	- 트리를 탐색하고 필요한 모든 라이브러리를 찾는 프로세스
	- **[[메이븐의 의존성 해결 알고리즘은 라이브러리 버전 중 루트에 가까운 버전을 선호]]**
- 의존성이 더 높은 버전을 요구하는 의존성 충돌
	- ==lib-d==가 ==lib-c==의 API 중 3.0 버전에만 추가된 API 사용하고 있을 때 ==lib-c==의 버전을 4.0으로 올릴 경우, ==lib-d== 가 의존하던 ==lib-c==의 API 중 3.0 버전에만 추가된 API가 4.0에는 없어 런타임 예외 발생할 수 있다
	- 해결 방법
		- ==mvn dependency:tree==
			- 의존성 트리 확인 방법
		- 가장 좋은 접근 방식은 의존성들 간에 동의할 수 있는 더 새로운 버전을 찾는 것
	- 만약 의존성 중 하나는 모두 동의할 수 있는 버전이지만 메이븐의 해결 알고리즘이 선택하지 않는 경우 => **트리의 일부를 제거하는 방법이 있다**
```xml
<dependencies>
    <dependency>
        <groupId>com.wellgrounded</groupId>
        <artifactId>second-test-helper</artifactId>
        <version>2.0.0</version>
        <scope>test</scope>
        <!-- assertj-core의 오래된 버전 제외 -->
        <exclusions>
            <groupId>org.assertj</groupId>
            <artifactId>assertj-core</artifactId>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>com.wellgrounded</groupId>
        <artifactId>first-test-helper</artifactId>
        <version>1.0.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
- ==maven-enforcer-plugin
	- 일치하지 않는 의존성 발견 시 빌드를 실패하도록 구성
	- 잘못된 런타임 동작에 의존해 문제가 나타나는 것을 피할 수 있다

### 11.2.8 검토
- JaCoCo 
	- 코드 커버리지 확인하는 방법 중 하나

### 11.2.9 자바 8을 넘어서
- 자바 9이상부터 사용되지 않지만 외부 라이브러리로 사용할 수 있는 라이브러리들
	- ==java.activation(JAF)==
	- ==java.corba(CORBA)==
	- ==java.transaction(JTA)==
	- ==java.xml.bind(JAXB)==
	- ==java.xml.ws(JAX-WS 및 일부 관련 기술)==
	- ==java.xml.ws.annotation(공통 애너테이션)==

### 11.2.10 메이븐에서의 다중 릴리스 JAR
- JDK 9 에서 도입된 기능 중 하나는 서로 다른 JDK를 타깃으로 서로 다른 코드를 가진 JAR를 패키징할 수 있음
- 다중 릴리스(==multirelease==)
	- JAR의 출력 형식, 코드 레이아웃도 중요

### 11.2.11 메이븐과 모듈
- 모듈형 라이브러리
	- 메이븐에게 모듈형 라이브러리에서 소스 코드를 컴파일할 위치 알려주는 방법
```xml
<build>
	<sourceDirectory>src/com.wellgrounded.modlib/java</sourceDirectory>
</build>
```
- 모듈화된 애플리케이션
	- 모듈화된 애플리케이션을 메이븐에게 알리기
```xml
module com.wellgrounded.modapp {
	requires.com.wellgrounded.modlib;
}

<dependency>
	<groupId>com.wellgrounded</groupId>
	<artifactId>modlib</artifactId>
	<version>2.0</version>
</dependency>
```


### 11.2.12 메이븐 플러그인 작성
- 메이븐 플러그인 작성
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.wellgrounded</groupId>
    <artifactId>wellgrounded-maven-plugin</artifactId>
    <!-- 플러그인 패키지를 빌드하려는 의도를 메이븐에게 알려줌-->
    <packaging>maven-plugin</packaging>
    <version>1.0-SNAPSHOT</version>
  
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>11</maven.compliler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.apache.maven</groupId>
            <artifactId>maven-plugin-api</artifactId>
            <version>3.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.maven.plugin-tools</groupId>
            <artifactId>maven-plugin-annotations</artifactId>
            <version>3.4</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```

- 코드 추가

```Java
package com.wellgrounded;

import org.apache.maven.plugins.AbstractMojo;
import org.apache.maven.plugins.MojoExecutionException;
import org.apache.maven.plugins.annotation.Mojo;

// maven에게 goal을 알려줌
@Mojo(name= "wellgrounded")
public class WellGroundedMojo extends AbstractMojo{
	public void execute() throws MojoExecutionException{
		getLog().info("Extending Maven for fun and profit.");
	}
}
```

## 11.3 그래들
- 그래들
	- 표준 디렉터리 레이아웃 지원
	- JVM 프로젝트를 위한 기본 빌드 생명 주기 제공
	- 코틀린 또는 그루비 위에 선언적 DSL 사용
### 11.3.1 그래들 설치
- wrapper
	- ==gradle.wrapper== 
		- 서로 다른 프로젝트에서 여러 gradle 버전을 사용하는 방법
		- 특정 버전의 그래들을 프로젝트의 로컬로 캡쳐
		- 그 후 ==./gradlew 또는 gradlew.bat== 명령을 통해 액세스
### 11.3.2 Tasks
- Tasks
	- 호출할 수 있는 작업의 조각
	- 다른 태스크에 의존할 수 있음
	- 스크립팅을 통해 구성
	- 그래들의 플러그인 시스템을 통해 추가 가능
	- 개념적으론 함수와 유사
	- ==./gradlew tasks== 
		- meta-task
		- 현재 프로젝트에서 사용 가능한 테스크 목록 나열
### 11.3.3 스크립트 내용
- 빌드 스크립트(buildscript)
	- 도메인 특화 언어 또는 DSL(==domain specific language==)로 구성
- 그루비 vs 코틀린
	- 그루비
		- JVM에서 동작하는 동적 언어
		- 유연하고 간결한 빌드 스크립트 작성 목표와 잘 어울림
	- 코틀린
		- 그래들 5.0 이후 추가

### 11.3.4 플러그인 사용
- 플러그인 사용 선언
```gradle
plugins{
	<!-- 일반적인 빌드 생명 주기 태스크-->
	base
}
```

### 11.3.5 빌드
- 애플리케이션 만들기
```gradle
plugins {
	<!-- 자바 앱을 컴파일하고 실행하는 방법을 아는 플러그인 -->
	application
}

application {
	mainClass.set("wgjd.Main)
}
<!-- -->
tasks.jar {
	manifest {
		<!-- 수정된 메니페스트가 있는 JAR을 패키징하는 작업 -->
		attributes("Main-Class" to application.mainClass)
	}
}
```
	- ./gradlew build로 빌드하면 jar 파일 생성
	- java -jar build/libs/wellgrounded.jar 실행ㅎ마ㅕㄴ 테스트 프로그램 실행
	- application 플러그인은 ./gradlew run을 사용해 직접 메인 클래스를 로드하고 실행할 수 있다\

### 11.3.6 작업 회피
- 증분 빌드(incremental build)
	- 불필요한 작업 반복 회피 전략
	- 컴퓨터의 동일한 위치에서 마지막으로 실행한 태스크의 출력만 재사용
	- 빌드 캐시를 통해 이전 빌드 똔느 다른 곳에서 실행한 빌드의 작업 결과물을 재사용할 수 있다
	- ==build-cache== 명령줄 플래그가 있는 속성을 통해 활성화

### 11.3.7 그래들의 의존성
- 기본 제공 함수는 mavenCentral, google에 있다
- 선언 방법
```gradle
repositories{
	mavenCentral()
}
<!-- 의존성 구성 -->
dependencies {
	implementation("org.slf4j:slf4j-api:1.7.30")
	<!-- 컴파일 중 코드를 사용할 수 없도록 설정 - maven의 <scope>의 목적 달성을 위한 키워드 -->
	runtimeOnly("org.slf4j:slf4j-api:1.7.30")

	<!-- 의존성이 프로젝트의 공개 API의 일부인 경우 표시 -->
	api("com.google.guava:guava:31.0.1-jre")
}
```
- 그래들 구성의 계층 구조
	![[Drawing 2024-11-08 14.12.04.excalidraw]]
- 일반적인 그래들 의존성 구성

| 이름                   | 목적                                   | 확장                                            |
| -------------------- | ------------------------------------ | --------------------------------------------- |
| api                  | 프로젝트의 외부 공용 API의 일부인 주요 의존성          |                                               |
| implementation       | 컴파일 및 실행 중에 사용되는 기본 의존성              |                                               |
| compileOnly          | 컴파일 중에만 필요한 의존성                      |                                               |
| compileClasspath     | 그래들이 컴파일 시 클래스패스를 조회하는 데 사용하는 구성     | ==compileOnly== ==implementation==            |
| runtimeOnly          | 런타임 중에만 필요한 의존성                      |                                               |
| runtimeClasspath     | 그래들이 런타임 시 클래스패스를 조회하는 데 사용하는 구성     | ==runtimeOnly==<br>==implementation==         |
| testImplementation   | 컴파일 및 테스트 실행 중에 사용되는 의존성             | ==implementation==                            |
| testCompileOnly      | 테스트 컴파일 중에만 필요한 의존성                  |                                               |
| testCompileClasspath | 그래들이 테스트 컴파일 시 클래스패스를 조회하는 데 사용하는 구성 | ==testCompileOnly==<br>==testImplementation== |
| testRuntimeOnly      | 런타임 중에만 필요한 의존성                      | ==runtimeOnly==                               |
| testRuntimeClasspath | 그래들이 테스트 런타임 시 클래스패스를 조회하는 데 사용하는 구성 | ==testCompileOnly==<br>==testImplementation== |
| archives             | 프로젝트의 출력 JAR 목록                      |                                               |
- 그래들의 의존성 해결
	- 전체 의존성 트리를 순회해 특정 패키지에 대한 모든 요청된 버전 확인
	- 요청된 버전의 전체 집합에서 그래들은 사용 가능한 **가장 높은 버전을 기본으로 선택**
		- 메이븐에서의 예기치 않은 동작 방지(예: 낮은 버전의 라이브러리 로드)
	- 전의적 의존성 문제 발생 시 핵심적인 명령어 
		- ==./gradlew dependencies==
		- ==./gradlew dependencyInsight==
			- 관심 있는 특정 의존성에 초점을 맞춘 명령

### 11.3.8 코틀린 추가
- 코틀린 의존성 추가 방법
```gradle
plugins {
	application
	id("org.jetbrains.kotlin.jvm") version "1.6.10"
}
```

### 11.3.9 테스트
- ==build== 테스크

### 11.3.10 정적 분석 자동화
- SpotBugs 추가
```gradle
plugins {
	application
	id("com.github.spotbugs") version "4.3.0"
}
```
	- ./gradlew check 명령 실행 시 정적 분석 
	- build/reports/spotbugs 폴더에 보고서 파일 생성

### 11.3.11 자바 8을 넘어서
- 자바 9이상부터 사용되지 않지만 외부 라이브러리로 사용할 수 있는 라이브러리들
	- ==java.activation(JAF)==
	- ==java.corba(CORBA)==
	- ==java.transaction(JTA)==
	- ==java.xml.bind(JAXB)==
	- ==java.xml.ws(JAX-WS 및 일부 관련 기술)==
	- ==java.xml.ws.annotation(공통 애너테이션)==
- 추가 방법 (==build.gradle.kts==에 추가)
```gradle
dependencies {
	implementation("com.sun.activation:jakarta.activation:1.2.2")
	implementation("org.glassfish.corba:glassfish-corba-omgapi:4.2.1)
	implementation("javax.transaction:javax.transaction-api:1.3")
	implementation("jakarta.xml.bind:jakarta.xml.bind-api:2.3.3")
	implementation("jakarta.xml.ws:jakarta.xml.ws-api:2.3.3")
	implementation("jakarta.annotation:jakarta.annotation-api:1.3.5")
}
```

### 11.3.12 모듈과 함께 그래들 사용하기
- 모듈형 라이브러리
	- 그래들은 수정한 소스의 위치를 자동으로 찾지 않아 ==build.gradle.kts==에 경로에 대한 힌트를 제공해야 한다
```gradle
sourceSets {
	main {
		java {
			setSrcDirs(listOf("src/com.wellgrounded.modlib/java"))
		}
	}
}
```
- 모듈형 애플리케이션
	- 로컬 라이브러리 테스트
```gradle
dependencies {
	implementation(files("../mod-lib/build/libs/gradle-mod-lib.jar"))
}
```

- JLink
	- 모듈식 애플리케이션의 경우, jlink는 완전히 작동하는 JVM 이미지 생성

### 11.3.13 사용자 정의
- 사용자 정의 작업
	- 사용자 정의 작업을 정의하는 것을 직접할 수 있음
```gradle
tasks.register("wellgrounded"){
	println("configuring")
	// 다른 테스크에 의존하도록 구성
	dependsOn("assemble")
	doLast{
		println("Hello from Gradle")
	}
}
```
- 사용자 정의 플러그인 만들기
	- 플러그인을 직접 빌드스크립트 내에 코드로 작성
	- 예시
```Java
// Plugin에서 상속받음
class WellgroundedPlugin: Plugin<Project> {
	override fun apply(project: Project){
		project.task("wellgrounded"){
			doLast{
				println("Hello from Gradle")
			}
		}
	}
}
apply<WellgroundedPlugin>()
```

