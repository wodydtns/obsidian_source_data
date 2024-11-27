## 2.1 핵심 애노테이션
```Java
import static org.junit.jupiter.api.Assertion.assertEquals;
import org.junit.jupiter.api.Test;

public class CalculatorTest {

	@Test
	public void testAdd(){
		Calculator calculator = new Calculator();
		double result = calculator.add(10,50);
		assertEquals(60,result,0);
	}
}
```
- 테스트 클래스
	- 클래스, 정적 멤버 클래스, 하나 이상의 테스트 메서드를 포함하는 @Nested 애노테이션이 붙은 내부 클래스
	- 추상 클래스일 수 없고, 단일한 생성자를 가지고 있어야함
	- 테스트 클래스의 생성자는 파라미터가 아예 없거나, 런타임에 의존성 주입으로 동적으로 리졸브할 수 있는 파라미터만 사용 가능
	- 가시성을 보장하기 위한 최소 요구 사항으로 디폴트 접근 제어자를 사용할 수 있음
- 테스트 메서드
	- @Test, @RepeatedTest, @ParameterizedTest, @TestFactory, @TestTemplate 애노테이션이 붙은 메서드
	- 추상 메서드 일 수 없고, 반환 타입이 반드시 void
- 생애 주기 메서드
	- @BeforeAll, @AfterAll, @BeforeEach, @AfterEach 애노테이션이 붙은 메서드
- JUnit은 테스므 메서드의 격리성을 보장하고 테스트 코드에서 의도치 않은 부수 효과를 방지하기 위해, @Test 메서드 호출 전 테스트 클래스 인스턴스를 매번 새로 만든다
	- **이로 인해 테스트 메서드 간에 인스턴스 변수를 재사용 할 수 없다**
	- 대신 테스트 클래스에 @TestInstance(Lifecycle.PER_CLASS) 애노테이션을 추가하면 JUnit 5는 동일한 테스트 인스턴스를 가지고 클래스에 있는 모든 테스트 메서드를 실행한다
	- 테스트 클래스 인스턴스가 메서드 단위가 아닌 클래스 단위로 생성
- JUnit 5 생애 주기 메서드
```Java
class SUTest {
	private static ResourceForAllTests resourceForAllTests;
	private SUT systemUnderTest;

	@BeforeAll
	static void setUpClass(){
		resourceForAllTests =
			new ResourceForAllTests("테스트를 위한 리소스");
	}

	@AfterAll
	static void tearDownClass(){
		resourceForAllTests.close();
	}

	@BeforeEach
	void setUp(){
		systemUnderTest = new SUT("테스트 대상 시스템");
	}

	@AfterEach
	void tearDown(){
		systemUnderTest.close();
	}

	@Test
	void testRegularWork(){
		boolean canReceiveRegularWork =
			systemUnderTest.canReceiveRegularWork();
		assertTrue(canReceiveRegularWork);
	}

	@Test
	void testAdditionalWork(){
		boolean canReceiveRegularWork =
			systemUnderTest.canReceiveRegularWork();
		assertFalse(canReceiveRegularWork);
	}
}
```
- @BeforeAll
	- 메서드가 전체 테스트가 실행되기 전에 한 번 실행
	- @BeforeAll 애노테이션이 붙은 메서드는 테스트 클래스에  @TestInstance(Lifecycle.PER_CLASS) 애노테이션이 없다면 static으로 선언해야한다
- @BeforeEach
	- 각 테스트가 실행되기 전에 실행된다
- @Test
	- 서로 간에 독립적으로 실행된다
- @AfterEach
	- 각 테스트가 실행된 이후 실행된다
- @AfterAll
	- 전체 테스트가 실행된 후 한 번 실행된다
	- @TestInstance(Lifecycle.PER_CLASS) 애노테이션이 없다면 static으로 선언해야한다

### 2.1.1. @DisplayName
- @DisplayName 애노테이션 사용하기
```Java
@DisplayName("Test class showing the @DisplayName annotation") // 1
class DisplayNameTest {
	private SUT systemUnderTest = new SUT();

	@Test
	@DisplayName("Our system under test says hello.") // 2
	void testHello(){
		assertEquals("Hello", systemUnderTest.hello());
	}

	@Test
	@DisplayName("😀") // 3
	void testTalking(){
		assertEquals("How are you?", systemUnderTest.talk());
	}

	@Test
	void testBye(){
		assertEquals("Bye", systemUnderTest.bye());
	}
}

```
	- 디스플레이 네임을 따로 명시하지 않은 테스트는 메서드 이름을 표시
	- 1. 전체 테스트 클래스에 적용할 디스플레이 네임
	- 2. 일반적인 텍스트로 디스플레이 네임 작성
	- 3. 이모지로 디스플레이 네임 작성

### 2.1.2 @Disabled
- @Disabled
	- 테스트 클래스나 메서드에 사용 가능
	- @Disabled를 붙인 테스트 클래스나 메서드는 비활성화되어 실행되지 않는다
- 예시
```Java
@Disabled("기능 개발 중") 1️⃣
class DisabledClassTest {
	private SUT systemUnderTest = new SUT("테스트 대상 시스템"); 

	@Test
	@Disabled 2️⃣
	void testRegularWork(){
		boolean canReceiveRegularWork =
			systemUnderTest.canReceiveRegularWork();
		assertTrue(canReceiveRegularWork);
	}

	@Test
	@Disabled("기능 개발 중") 3️⃣
	void testAdditionalWork(){
		boolean canReceiveRegularWork =
			systemUnderTest.canReceiveRegularWork();
		assertFalse(canReceiveRegularWork);
	}

}

```
	 - 1. 테스트 클래스를 비활성화
	 - 2. 비활성화된 이유를 알 수 없음
	 - 3. 비활성화 이유를 알 수 있음 => 권장 사항

## 2.2 중첩 테스트
- 결합도 관점에서 중첩 테스트는 개발자가 테스트 그룹 간의 관계를 표현하는 데 도움이 된다. 참고로 내부 클래스는 해당 패키지 내에서만 접근이 가능하다
- 중첩 테스트
```Java
publc class NestedTestsTest { 1️⃣
	private static final String FIRST_NAME = "John"; 2️⃣
	private static final String LAST_NAME = "Smith"; 2️⃣

	@Nested 3️⃣
	class BuliderTest { 3
		private String MIDDLE_NAME = "Michael";

		@Test 4️⃣
		void customBuilder() throws ParseException { 4️⃣
			SimpleDateFormat simpleDateFormat = new SimpleDateFormat("MM-dd-yyyy");
			Date customerDate = simpleDateFormat.parse("04-21-2019");

			Customer customer = Customer.builder( 5️⃣
				Gender.MALE, FIRST_NAME, LAST_NAME 5️⃣
			)
			.withMiddleName(MIDDLE_NAME) 5️⃣
			.withBecomeCustomer(customDate) 5️⃣
			.build(); 5️⃣

			assertAll(()->{ 6️⃣
				assertEquals(Gender.MALE, customer.getGender()); 6️⃣
				assertEquals(FIRST_NANE, customer.getFirstName()); 6️⃣
				assertEquals(LAST_NAME, customer.getLastName()); 6️⃣
				assertEquals(MIDDLE_NAME, customer.getMiddleName()); 6️⃣
				assertEquals(customerDate, customer.getBecomeCustomer()); 6️⃣
			});
		}
	}

}
```
	- 1. 메인 테스트, 중첩 테스트 BuilderTest와 결합되어 있다
	- 2. 변수 선언
	- 3. 빌터 패턴 사용
	- 4. 객체 생성 검증

## 2.3 태그를 사용한 테스트
- @Tag
	- 사용하면 테스트를 발견하거나 실행할 때 필터를 적용할 수 있다
```Java
@Tag("individual") 1️⃣
public class CustomerTest {
	private String CUSTOMER_NAME = "John Smith";

	@Test
	void testCustomer(){
		Customer customer = new Customer(CUSTOMER_NAME);
		assertEquals("John Smith", customer.getName());
	}
}

@Tag("repository") 2️⃣
public class CustomerRepositoryTest {
	private String CUSTOMER_NAME = "John Smith";
	private CustomerRepository repository = new CustomerRepository();

	@Test
	void testNonExistence(){
		boolean exists = repository.contains("John Smith");
		assertFalse(exists);
	}

	@Test
	void testCustomerPersistance(){
		repository.persist(new Customer(CUSTOMER_NAME));
		assertTrue(repository.contains("John Smith"));
	}

}
```
	- mvn clean install 실행 시
		- 1번은 @Tag("individual") 태그가 지정된 테스트만 실행
		- 2번은 @Tag("repository") 태그가 지정된 테스트는 제외

## 2.4 단언문
- 자주 사용하는 JUnit 5 단언문 메서드

| 단언문 메서드                                          | 활용 목적                                                                                                                         |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| assertAll                                        | 오버로딩이 적용되어 있다. 안에 있는 executable 객체 중 어느 것도 예외를 던지지 않는다고 단언한다. 이때 executable은 org.junit.jupiter.api.function.Executable 타입의 객체 |
| assertArrayEquals                                | 오버로딩이 적용되어 있다. 예상 배열과 실제 배열이 동등하다고 단언                                                                                         |
| assertEquals                                     | 오버로딩이 적용되어 있다. 예상 값과 실제 값이 동등하다고 단언                                                                                           |
| assertX(..., String message)                     | 실패했을 경우 message를 테스트 프레임워크에 전달하는 단언문                                                                                          |
| asssertX(..., Supplier\<String> messageSupplier) | 실패했을 경우 message를 테스트 프레임워크에 전달하는 단언문. 실패 메시지는 messageSupplier에서 지연(lazy) 전달된다                                                 |
- 기존(JUnit 4)의 Hamcrest 매처와 함께 사용되던 assertThat은 지원하지 않고, Hamcrest의 MatcherAssert.asssertThatㄹ를 오버로딩한 메서드 사용이 권장
- assertAll 메서드
```Java
class AssertAllTest {
	@Test
	@DisplayName("기본적으로 테스트 대상 시스템은 검증하지 않는다")
	void testSystemNotVerified(){
		SUT systemUnderTest = new SUT("테스트 대상 시스템");

		assertAll("테스트 대상 시스템을 검증하지 않았는지 확인", 1️⃣
			() -> assertEquals("테스트 대상 시스템"), 2️⃣
				systemUnderTest.getSystemName()), 2️⃣
			() -> assertFalse(systemUnderTest.isVerified()) 3️⃣
		);
	}

	@Test
	@DisplayName("테스트 대상 시스템을 검증한다")
	void testSystemUnderVerification(){
		SUT systemUnderTest = new SUT("테스트 대상 시스템");

		systemUnderTest.verify();

		assertAll("테스트 대상 시스템을 검증하지 않았는지 확인", 4️⃣
			() -> assertEquals("테스트 대상 시스템"), 5️⃣
				systemUnderTest.getSystemName()), 5️⃣
			() -> assertTrue(systemUnderTest.isVerified()) 6️⃣ 
		);
	}

}
```
	- assertAll 메서드는 일부 단언문이 실패하더라도 모든 단언문을 항상 검증함
	- 1번 테스트에서 assertAll 메서드는 executable 객체 중 하나가 예외를 던지는 경우 표시할 메세지를 파라미터로 작성
	- 2번의 assertEquals 메서드로 검증할 executable 객체 & 3번의 assertFalse 메서드로 검증할 executable 객체를 전달
	- 두 번째 테스트에서 assertAll 메서드는 executable 객체 중 하나가 예외를 던지는 경우 표시할 메시지를 파라미터로 작성
	- 5번의 assertEquals 메서드로 검증할 executable 객체 & 6번의 assertTrue 메서드로 검증할 executable 객체를 전달
- 단언문에 Supplier\<String>를 사용한 경우
```Java
@Test
@DisplayName("테스트 대상 시스템을 검증한다")
void testSystemUnderVerification(){
	systemUnderTest.verify();
	assertTrue(systemUnderTest.isVerified(), 1️⃣
	() -> "테스트 대상 시스템을 검증했는지 확인" ); 2️⃣
}

@Test
@DisplayName("테스트 대상 시스템을 검증하지 않았다")
void testSystemNotUnderVerification(){
	assertFalse(systemUnderTest.isVerified(), 3️⃣
	() -> "테스트 대상 시스템을 검증하지 않았는지 확인" ); 4️⃣
}

@Test
@DisplayName("현재 테스트 대상 시스템은 작업이 없다")
void testNoJob(){
	assertNull(systemUnderTest.getCurrentJob(), 5️⃣
	() -> "테스트 대상 시스템은 현재 작업이 없는지 확인"); 6️⃣

}
```
	- 1. assertTrue 메서드를 사용해 조건이 참인지 검증한다
		- 1의 조건이 성공하면 2번의 람다식이 호출되지 않는다
	- 2. 실패하면 "테스트 대상 시스템을 검증했는지 확인"이 지연 전달된다
	- 3. assertFalse 메서드를 사용해 조건이 거짓인지 검증
	- 4. 실패하면 "테스트 대상 시스템을 검증하지 않았는지 확인"이 지연 전달된다
	- 5. 객체가 존재하는지는 assertNull 메서드를 가지고 검증
	- 6. 실패하면 "테스트 대상 시스템은 현재 작업이 없는지 확인"이 지연 전달된다

- assertTimeout 메서드 사용하기
```Java
class AssertTimeoutTest {
	private SUT systemUnderTest = new SUT("테스트 대상 시스템");

	@Test
	@DisplayName("작업이 마칠 때까지 기다리는 assertTimeout 메서드")
	void testTimeout() throws InterruptedException {
		systemUnderTest.addJob(new Job("Job 1"));
		assertTimeout(ofMillis(500), () -> systemUnderTest.run(200)); 1️⃣
	}

	@Test
	@DisplayName("시간이 지나면 작업을 중지시키는 assertTimeoutPreemptively 메서드")
	void testTimeoutPreemptively() throws InterruptedException{
		systemUnderTest.addJob(new Job("Job 1"));
		assertTimeoutPreemptively(ofMillis(500), 2️⃣
								() -> systemUnderTest.run(200)); 2️⃣
	}
}
```
	- assertTimeout 메서드는 executable 객체가 작업을 마칠 때까지 기다린다
		- 만약 테스트가 주어진 시간을 초과하면 execution exceeded timeout of 500 ms by 193ms 같은 메시지로 테스트가 얼마나 늦어졌는지 알려준다
	- assertTimeoutPreemptively 메서드는 시간이 지나면 executable 객체를 중지시킨다
		- 만약 테스트가 실패한다면 execution timed out after 500 ms와 같이 지정한 시간 안에 테스트가 완료되지 못했다고 알려준다
-  assertThrows 메서드 사용하기
```Java
class AssertThrowsTest {
	private SUT systemUnderTest = new SUT("테스트 대상 시스템");

	@Test
	@DisplayName("예외가 발생하는지 검증한다")
	void testExpectedException() {
		assertThrows(NoJobException.class, systemUnderTest::run); 1️⃣
	}

	@Test
	@DisplayName("예외가 발생하고 예외에 대한 참조가 유지되는지 검증한다")
	void testCatchException(){
		Throwable throwable = assertThrows(NoJobException.class,
										() -> systemUnderTest.run(1000)); 2️⃣
		assertEquals("테스트 대상 시스템은 현재 작업이 없는지 확인",
						throwable.getMessage()); 3️⃣
	}
}
```
	- 1. systemUnderTest에서 run 메서드 호출 시 NoJobException이 발생하는지 검증
	- 2. systemUnderTest.run(1000) 문장이 NoJobException을 던지는 지 검증한다. throwable에 예외에 대한 참조가 유지되었는지도 검증한다
	- 3. throwable이 가지고 있는 에러 메시지가 "테스트 대상 시스템은 현재 작업이 없는지 확인"인지 검증한다

## 2.5 가정문
- 가정문은 테스트를 수행하는 데 필수인 전제 조건이 충족되었는지를 검증하는 문장이다
- JUnit 5는 자바 8 람다식과 함께 사용할 수 있는 가정문 메서드를 지원
	- org.junit.jupiter.api.Assumptions 패키지에 속하는 정적 메서드
	- message 파라미터는 맨 마지막에 있음
- 가정문을 활용한 사례
```Java
class AssumptionsTest {
	private static String EXPECTED_JAVA_VERSION = "1.8";
	private TestEnviroment environment = new TestsEnvironment(
		new JavaSpecification(
			System.getProperty("java.vm.specification.version")),
		new OperationSystem(
			System.getProperty("os.name"),
			System.getProperty("os.arch"))
		);

	private SUT systemUnderTest = new SUT();

	@BeforeEach 1️⃣
	void setUp(){ 1️⃣
		assumeTrue(enviroment.isWindows());1️⃣
	}

	@Test
	void testNoJobToRun(){
		assuminigThat(
			() -> environment.getJavaVersion().equals(EXPECTED_JAVA_VERSION), 2️⃣
			() -> assertFalse(systemUnderTest.hasJobToRun()) 3️⃣
		);
	}

	@Test
	void testJobToRun(){
		assertTrue(environment.isAmd64Architectrue()); 4️⃣
		systemUnderTest.run(new Job()); 5️⃣
		assertTrue(systemUnderTest.hasJobToRun()); 6️⃣
	}
}
```
	- 1. 현재 OS 환경이 윈도우라는 가정이 만족하지 않으면 테스트 실행이 되지 않는다
	- 2. 현재 자바 버전이 1.8인지 검증
	- 3. 자바 버전이 1.8일 때만 시스템에서 현재 실행 중인 작업이 없음을 검증
	- 4. 현재 아키텍처가 사전에 가정한 환경인지 검증
	- 5. 아키텍처가 AMD64인 경우에만 시스템에서 새로운 작업 수행
	- 6. 시스템에 실행할 작업이 있는지 검증

## 2.6 JUnit 5의 의존성 주입
- 현재 JUnit 5에는 세 개의 리졸버가 기본으로 내장되어 있다

### 2.6.1 TestInfoParameterResolver
- TestInfoParameterResolver
	- 테스트 클래스 생성자나 테스트 메서드에서 TestInfo 객체를 파라미터로 사용할 수 있다
	- TestInfo는 현재 실행 중인 테스트나 컨테이너에 관한 정보를 제공하기 위해 사용
		- @Test, @BeforeEach, @AfterEach, @BeforeAll, @AfterAll 애노테이션이 달린 메서드에서 TestInfo 객체를 파라미터로 사용할 수 있다
	- TestInfo는 디스플레이 네임, 테스트 클래스, 테스트 메서드, 관련 태그에 관한 정보 등 현재 테스트에 대한 정보를 가져온다
- TestInfo를 파라미터로 활용한 사례
```Java
class TestInfoTest {
	TestInfoTest(TestInfo testInfo){
		assertEquals("TestInfoTest", testInfo.getDisplayName()); 1️⃣
	}

	@BeforeEach
	void setUp(TestInfo testInfo){
		String displayName = testInfo.getDisplayName();
		assertTrue(displayName.equals("display name of the method") 2️⃣
			|| displayName.equals("testGEtNameOfTheMethod(TestInfo)") 2️⃣
		);
	}

	@Test
	void testGetNameOfTheMethod(TestInfo testInfo){
		assertEquals("testGetNameOfTheMethod(TestIfno)", 
					testInfo.getDisplayName()); 3️⃣
	}

	@Test
	@DisplayName("display name of the method")
	void testGetNameOfTheMethodWithDisplayNameAnnotation(TestInfo testInfo){
		assertEquals("display name of the method", testInfo.getDisplayName()); 4️⃣
	}

}
```
	- 1. 테스트 클래스 생성자는 디스플레이 네임이 TestInfoTest 인지 검증
	- 2. 디스플레이 네임은 메서드의 이름이거나 @DisplayName 애노테이션을 사용해 사용자 정의한 이름
	- 3. 테스트 메서드에서도 TestInfo 타입의 파라미터를 사용했다. 각 테스트 메서드는 디스플레이 네임이 예상한 이름인지 검증한다. 첫 번째 테스트는 디스플레이 네임이 메서드 이름이고(3), 두 번째 테스트는 디스플레이 네임이 @DisplayName 애노테이션에 사용자 정의한 설명글(4)이다
	- 기본으로 내장되어 있는 TestInfoParameterResolver는 TestInfo 객체를 파라미터로 리졸브하는데, TestInfo 객체는 현재의 컨테이너 또는 테스트에 대응한다. 곧 생성자나 메서드에서 테스트에 관한 정보를 제공하는 데 사용한다

### 2.6.2 TestReporterParameterResolver
- TestReporterParameterResolver를 사용하면 테스트 클래스 생성자나 테스트 메서드에서 TestReporter 객체를 파라미터로 사용할 수 있다
- TestReporter
	- 함수형 인터페이스이므로 람다식이나 메서드 참조로 사용할 수 있다
	- 한 개의 추상 메서드 publishEntry 와 publishEntry 메서드를 오버로딩한 디폴트 메서드 여러 개를 가진다
	- TestReporter 타입의 파라미터는 @BeforeEach, @AfterEach, @Test 애노테이션이 달린 테스트 메서드에 주입할 수 있다
	- TestReporter 객체는 현재 실행되는 테스트에 추가적인 정보를 제공할 때 사용한다
- TestReporter를 파라미터로 활용한 사례
```Java
class TestReporterTest {

	@Test
	void testReportSingleValue(TestReporter testReporter){
		testReporter.publishEntry("Single value"); 1️⃣
	}

	@Test
	void testReportKeyValuePair(TestReporter testReporter){
		testReporter.publishEntry("Key", "Value"); 2️⃣
	}

	@Test
	void testReportMultipleKeyValuePairs(TestReporter testReporter){
		Map<String, String> values = new HashMap<>(); 3️⃣
		values.put("user", "John"); 4️⃣
		values.put("password", "secret"); 4️⃣
		testReporter.publishEntry(values); 5️⃣
	}
}
```
	- 1. 1번 메서드는 단일한 문자열 값을 출력하는 데 사용했다
	- 2. 2번 메서드는 키-값 쌍을 출력하는 데 사용했다
	- 3. 3번 메서드는 맵을 생성하고, 맵에 두 개의 키-값 쌍을 넣은 다음, 생성된 맵을 출력하는 데 사용했다
	- 4. TestReporterParameterResolver는 테스트 리포트를 만들 때 사용할 수 있는 TestReporter 객체를 파라미터로 리졸브한다

### 2.6.3 RepetitionInfoParameterResolver
- RepetitionInfoParameterResolver 리졸버는 @RepeatedTest, @BeforeEach, @AfterEach 애노테이션이 달린 메서드의 파라미터가 RepetitionInfo 타입일 때 RepetitionInfo 인스턴스를 리졸브하는 역할
- RepetitionInfo는 @RepeatedTest 애노테이션이 달린 테스트에 대한 현재 반복 인덱스와 총 반복 횟수에 대한 정보를 가지고 있다

## 2.7 반복 테스트
- JUnit 5 에서는 @RepeatedTest 애노테이션을 사용해 반복 횟수를 지정한 후 해당 횟수만큼 테스트를 반복 가능
- @RepeatedTest 애노테이션이 제공하는 플레이스홀더
	- {displayName}
		- @RepeatedTest 애노테이션이 붙은 메서드의 디스플레이 네임
	- {currentRepetition}
		- 현재 반복 인덱스
	- {totalRepetitions}
		- 총 반복 횟수
- 반복 테스트
```Java
public class RepeatedTestsTest {
	private static Set<Integer> integerSet = new HashSet<>();
	private static List<Integer> integerList = new ArrayList<>();

	@RepeatedTest(value = 5, name = "{displayName} - 1️⃣
				repetition {currentRepetition}/{totalRepetitions}")1️⃣
	@DisplayName("Test add operation")
	void addNumber(){
		Calculator calculator = new Calculator();
		assertEquals(2, calculator.add(1,1), "1 + 1 should equal 2");
	}

	@RepeatedTest(value = 5
				 name = "the list conatins {currentRepetition} elements(s),
				 the set contains 1 element")
	void testAddingToCollections(TestReporter testReporter,
								RepetitionInfo repetitionInfo){
		integerSet.add(1);
		integerList.add(repetitionInfo.getCurrentRepetition());

		testReporter.publishEntry("Repetition number",
								String.valueOf(repetitionInfo.
												getCurrentRepetition()));
		assertEquals(1, integerSet.size());
		assertEquals(repetitionInfo.getCurrentRepetition(),
						integerList.size());
	}
}
```
	- 첫 번째 테스트는 다섯 번 반복. 각 반복마다 디스플레이 네임, 현재 반복 인덱스, 전체 반복 횟수를 보여준다
	- 두 번째 테스트도 다섯 번 반복. 각 반복마다 리스트의 요소 수(현재 반복 인덱스)를 표시하고 집합(이 경우 integerSet)에 항상 하나의 요소만 있는지 검증
	- 두 번째 테스트가 반복될 때마다 RepetitionInfo가 현재 반복 인덱스를 가지고 있는 것을 알 수 있다

## 2.8 파라미터를 사용한 테스트
- 파라미터를 사용한 테스트(parameterized test)는 하나의 테스트를 다양한 파라미터를 가지고 여러 번 실행하게 해 주는 기능
- 장점으로 다양한 입력을 두고 테스트를 실행할 수 있다는 점이 있다
- @ValueSource 애노테이션 활용 사례
```Java
class ParameterizedWithValueSourceTest {
	private WordCounter wordCounter = new WordCounter();

	@ParameterizedTest 1️⃣
	@ValueSource(strings = {"Check three parameters", 2️⃣
					"JUnit in Action"}) 2️⃣
	void testWordsInSentence(String sentence){
		assertEquals(3, wordCounter.countWords(sentence));
		
	}

}
```
	- 1. @ParameterizedTest 애노테이션을 사용해 해당 테스트가 파라미터를 사용한 테스트임을 명시한다
	- 2. 테스트 메서드의 파라미터로 전달할 값을 특정한다. 테스트 메서드는 @ValueSource에 적혀 있는 문자열의 수만큼 총 두 번 실행된다
- @EnumSource
	- 사용하면 파라미터에 enum을 사용할 수 있다
	- names 속성을 통해 @EnumSource를 사용하거나 제외할 열거형 인스턴스 지정 가능
	- 기본적으로 열거형의 모든 인스턴스를 대상으로 함
	- @EnumSource 예제
```Java
class ParameterizedWithEnumSourceTest {
	private WordCounter wordCounter = new WordCounter();

	@ParameterizedTest
	@EnumSource(Sentences.class)
	void testWordsInSentence(Sentences sentences){
		assertEquals(3, wordCounter.countWords(sentences.value()));
	}

	@ParameterizedTest
	@EnumSource(Sentences.class
				name = {"JUNIT_IN_ACTION", "THREE_PARAMETERS"})
	void testSelectedWordsInSentence(Sentences sentences){
		assertEquals(3, wordCounter.countWords(sentences.value()));
	}

	@ParameterizedTest
	@EnumSource(Sentences.class, mode = EXCLUDE,
				name = { "THREE_PARAMETERS" })
	void testSelectedWordsInSentence(Sentences sentences){
		assertEquals(3, wordCounter.countWords(sentences.value()));
	}

	enum Sentences {
		JUNIT_IN_ACTION("Junit in Action"),
		SOME_PARAMETERS("Check some parameters"),
		THREE_PARAMETERS("Check three parameters");

		private final String sentence;

		Sentences(String sentence){
			this.sentence = sentence;
		}

		public String value(){
			return sentence;
		}
	}
}
```
	- 첫 번째 테스트는 @ParameterizedTest 애노테이션을 달아 파라미터를 사용한 테스트임을 명시한다. 여기서는 @EnumSource의 대상을 Sentences.class 열거형 전체로 잡는다. 따라서 이 테스트는 Sentences 열거형의 각 인스턴스에 대해 한 번씩 총 세 번 실행한다
	- 두 번째 테스트도 @ParameterizedTest 애노테이션이 달려있다. 대상 인스턴스를 JUNIT_IN_ACTION, THREE_PARAMETERS만으로 한정해 총 두 번 실행된다
	- 세 번째 테스트도 @ParameterizedTest 애노테이션이 달려있다. @EnumSource가 가리키는 대상을 Sentences.class로 지정한 것은 같지만 THREE_PARAMETERS를 제외했다. 따라서 JUNIT_IN_ACTION, SOME_PARAMETERS에 대해 총 두 번 실행된다
- @CsvSource 애노테이션 활용
	- csv 형식으로 파라미터 제공
```Java
class ParameterizedWithCsvSourceTest {
	private WordCounter wordCounter = new WordCounter();

	@ParameterizedTest 1️⃣
	@CsvSource({"2, Unit testing", "3, JUnit in action", 2️⃣
				"4, Write solid Java code"}) 2️⃣
	void testWordsInSentence(int expected, String sentence){P
		assertEquals(expected, wordCounter.countWords(sentence));
	}

}
```
	- 테스트는 @ParameterizedTest 애노테이션을 달아 파라미터를 사용한 테스트임을 명시
	- 테스트에 전달된 파라미터는 @CsvSource 애노테이션에 나열된 CSV 형식의 문자열에서 구문을 분석해 가져온다. 이 테스트는 각 CSV 행에 대해 한 번씩 총 세 번 실행된다
	- 각 CSV 행은 구문 분석되어 첫 번째 값은 expected에 할당되고, 두 번째 값은 sentence에 할당된다
- @CsvFileSource 애노테이션 활용
	- 클래스패스에 있는 CSV 파일을 파라미터의 소스로 사용
```Java
class ParameterizedWithCsvFileSourceTest {
	private WordCounter wordCounter = new WordCounter();

	@ParameterizedTest 1️⃣
	@CsvSource(resources = "/word_counter.csv") 1️⃣
	void testWordsInSentence(int expected, String sentence){P
		assertEquals(expected, wordCounter.countWords(sentence));
	}

}
```
	- @CsvFileSource 애노테이션에 등록된 리소스를 파라미터로 사용하는 테스트
## 2.9 동적 테스트
- JUnit 5는 런타임에 테스트를 생성할 수 있는 동적 프로그래밍 모델을 도입
- 개발자가 팩터리 메서드를 작성하기만 하면 프레임워크가 런타임에 실행할 테스트를 생성
- 팩터리 메서드에는 @TestFactory 애노테이션을 달면 된다
- @TestFactory
	- 테스트를 생성하는 팩터리
	- DynamicNode(추상 클래스, DynamicContainer나 DynamicTest가 DynamicNode를 상속하였고, 인스턴스화가 가능한 구체 클래스)
	- DynamicNode 객체의 배열
	- DynamicNode 객체의 스트림
	- DynamicNode 객체의 컬렉션
	- DynamicNode 객체의 Iterable
	- DynamicNode 객체의 Iterator
	- 디폴트 접근 제어자를 사용할 수 있지만 private이거나 정적일 수 없다
	- ParameterResolver에 의해 리졸브 될 파라미터를 선언할 수는 없다
- 동적 테스트는 @Test 애노테이션이 달린 보통의 테스트와 다른 생애 주기를 가지고 있다
- @BeforeEach, @AfterEach 애노테이션이 달린 메서드는 @TestFactory 메서드 전체에 대해 실행될 뿐 개별 테스트 각각에 대해서는 실행되지 않는다
- @TestFactory 애노테이션 활용 사례
```Java
class DynamicTestsTest {
	private PositiveNumberPredicate predicate = new PositiveNumberPredicate();

	@BeforeAll 1️⃣
	static void setUpClass(){ 1️⃣
		System.out.println("@BeforeAll method");
	}

	@AfterAll 2️⃣
	static void tearDownClass(){ 2️⃣
		System.out.println("@AfterAll method");
	}

	@BeforeEach 3️⃣
	void setUp(){ 3️⃣
		System.out.println("@BeforeEach method");
	}

	@AfterEach 4️⃣
	void tearDown(){ 4️⃣
		System.out.println("@AfterEach method");
	}

	@TestFactory 5️⃣
	Iterator<DynamicTest> positiveNumberPredicateTestCases() { 5️⃣
		return asList(
			dynamicTest("negative number", 6️⃣
						() -> assertFalse(predicate.check(-1))), 6️⃣
			dynamicTest("zero", 7️⃣
						() -> assertFalse(predicate.check(0))), 7️⃣
			dynamicTest("positive number", 8️⃣
						() -> assertFalse(predicate.check(1))), 8️⃣
		)
	}
}
```
	- @BeforeAll 애노테이션이 달린 setUpClass 메서드(1)와 @AfterAll 애노테이션이 달린 tearDownClass 메서드(2)는 전체 테스트를 시작하기 전과 전체 테스트를 끝낸 다음에 한 번씩 실행된다
	- @BeforeEach 애노테이션이 달린 setup 메서드(3)와 @AfterEach 애노테이션이 달린 tearDown 메서드(4)는 @TestFactory 애노테이션이 달린 메서드가 실행되기 전후에 실행된다(5)
	- 팩터리 메서드는 "negative number", "zero", "positive number" 레이블을 달고 있는 세 가지 테스트 메서드를 생성한다
	- 각 테스트는 dynamicTest 메서드의 두 번째 파라미터로 주어지는 Executable 객체가 실행

## 2.10 Hamcrest 매처 사용하기
- Hamcrest 매처 라이브러리를 사용해 테스트 표현식을 작성
- Hamcrest는 테스트 프레임워크는 아니다. 그러나 간명한 매치 규칙을 선언하는 데 도움이 된다. 매치 규칙은 다양한 상황에 쓰이지만 특히 단위 테스트에 유용하게 쓰일 수 있다
- org.hamcrest.Matchers 클래스의 팩터리 메서드들

| 팩터리 메서드                                                                 | 사용사례                                                      |
| ----------------------------------------------------------------------- | --------------------------------------------------------- |
| anything                                                                | 아무것이나 일치하면 될 때 사용한다. 단언문을 더 읽기 쉽게 만들 때 유용하다               |
| is                                                                      | 문장의 가독성을 높이고 싶을 때 사용한다. 일종의 장식 표현                         |
| allOf                                                                   | 모든 매처 규칙을 만족하는지 확인한다(&& 연산자와 비슷하다)                        |
| anyOf                                                                   | 하나라도 일치하는 매치 규칙이 있는지 확인한다(\|\| 연산자와 비슷)                   |
| not                                                                     | 매치 규칙의 의미를 뒤집는다(! 연산자와 비슷하다)                              |
| instanceOf                                                              | 객체가 특정 클래스의 인스턴스인지 확인한다                                   |
| sameInstance                                                            | 객체 동일성을 확인한다                                              |
| nullValue, notNullValue                                                 | null인지 아닌지 확인한다                                           |
| hasProperty                                                             | 객체가 특정 속성을 가졌는지 확인한다                                      |
| hasEntry, hasKey, hasValue                                              | 맵이 특정 엔트리, 키, 값을 가졌는지 확인한다                                |
| hasItem, hasItems                                                       | 컬렉션이 특정 요소나 요소들을 가졌는지 확인한다                                |
| closeTo, GreaterThan, GreaterThanOrEqualTo, lessThan, lessThanOrEqualTo | 주어진 숫자가 가까운지, 큰지, 크거나 같은지, 작은지, 작거나 같은지를 확인한다             |
| equalToIgnoringCase                                                     | 대소문자를 무시하고 주어진 문자열이 일치하는지 확인한다                            |
| equalToIgnoringWhiteSpace                                               | 공백을 무시하고 주어진 문자열이 일치하는지 확인한다                              |
| containsString, startsWith, endsWith                                    | 주어진 문자열이 특정 문자열을 포함하는지, 특정 문자열로 시작하는지, 특정 문자열로 끝나는지를 확인한다 |
- Hamcrest 메서드를 사용한 사례
```Java
public class HamcrestMatchersTest {
	 private static String FIRST_NAME = "John";
    private static String LAST_NAME = "Smith";
    private static Customer customer = new Customer(FIRST_NAME, LAST_NAME);


    @Test
    @DisplayName("Hamcrest is, anyOf, allOf")
    public void testHamcrestIs() {
        int price1 = 1, price2 = 1, price3 = 2;

        assertThat(1, is(price1)); 1️⃣
        assertThat(1, anyOf(is(price2), is(price3))); 1️⃣
        assertThat(1, allOf(is(price1), is(price2))); 1️⃣
    }

    @Test
    @DisplayName("Null expected")
    void testNull() {
        assertThat(null, nullValue()); 2️⃣
    }

    @Test
    @DisplayName("Object expected")
    void testNotNull() {
        assertThat(customer, notNullValue()); 3️⃣
    }

    @Test
    @DisplayName("Check correct customer properties")
    void checkCorrectCustomerProperties() {
        assertThat(customer, allOf( 4️⃣
                hasProperty("firstName", is(FIRST_NAME)), 4️⃣
                hasProperty("lastName", is(LAST_NAME)) 4️⃣
        ));
    } 
}
```
	- is, anyOf, allOf를 사용했다. anyOf, allOf에는 is를 여러번 중첩
	- nullValue 매처를 사용해 예상 값이 null인지 검증
	- notNullValue 매처를 사용해 customer가 null이 아닌지 검증
	- assertThat 메서드에서 표에 제시된 메서드를 사용

