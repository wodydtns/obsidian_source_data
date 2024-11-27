>[!important]
>세상 무엇도 지속적인 개선 없이는 살아남을 수 없다 - Charles M. Tadros(찰스 M.타드로스)

## 4.1 JUnit 4에서 JUnit 5로의 전환 과정
- JUnit은 JUnit Vintage 테스트 엔진을 활용해 테스트를 JUnit 4에서 JUnit 5로 전환하는 로드맵을 제시
- JUnit 4와 관련된 모든 클래스와 애노테이션은 org.junit 패키지에 있어, 클래스패스에 JUnit 5 jupiter와 JUnit 4가 모두 존재해도 충돌이 발생하지 않는다
- JUnit 4에서 JUnit 5로 전환하는 주요 과정

| 주요 단계                                       | 설명                                                                                                                                                                   |
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 의존성을 교체한다                                   | JUnit 4에서는 하나의 의존성만 있으면 된다. 하지만 JUnit 5에는 목적에 따라 다양한 의존성이 필요할 수 있다. 예를 들어 JUnit 5에서 JUnit 4 테스트를 실행하기 위해서는 Vintage가 필요하다                                             |
| JUnit 4 애노테이션을 JUnit 5 애노테이션으로 교체한다         | JUnit 5는 JUnit 4의 애노테이션 중의 일부를 가지고 있다. <br>JUnit 5에서 새로 추가된 애노테이션은 신규 기능을 적용하고 더 나은 테스트를 작성하는 데 도움이 된다                                                               |
| 테스트 클래스와 메서드를 교체한다                          | JUnit 5에서 사용하는 단언문이나 가정문은 다른 패키지의 다른 클래스로 옮겨졌다                                                                                                                       |
| JUnit 4 rule과 runner 를 JUnit 5의 확장 모델로 교체한다 | JUnit 4 rule과 runner 를 JUnit 5 확장 모델로 바꾸는 작업에는 일반적으로 다른 단계보다 더 많은 노력이 필요하다.하지만 JUnit 4와 JUnit 5는 장기간 공존할 수 있으므로 JUnit 4 rule과 runner는 코드에 계속 남아 있어도 상관없고 나중에 바꿔도 괜찮다 |

## 4.2 JUnit 4에서 JUnit 5로 전환하는 데 필요한 의존성
- 의존성 대체를 위해 junit-vintage-engine이 필요
- JUnit 5를 사용해 테스트 작성 시 junit-jupiter-api, junit-jupiter-engine 의존성 필요
	- junit-jupiter-api는 애노테이션, 클래스, 메서드를 포함하고, JUnit Jupiter로 테스트 작성을 위한 API를 제공하는 아티팩트
	- junit-jupiter-engine는 테스트 엔진을 실행하기 위한 JUnit Jupiter의 핵심적인 아티팩트
- 그 외 파라미터를 사용한 테스트를 작성하려면 junit-jupiter-params 의존성이 필요할 수 있다

## 4.3 JUnit 5 애노테이션, 클래스, 메서드
### 4.3.1 JUnit 4와 JUnit 5에서 비슷하게 사용하는 애노테이션, 클래스, 메서드
- 애노테이션

| JUnit 4                   | JUnit 5                 |
| ------------------------- | ----------------------- |
| @BeforeClass, @AfterClass | @BeforeAll, @ AfterAll  |
| @Before, @After           | @BeforeEach, @AfterEach |
| @Ignore                   | @Disabled               |
| @Category                 | @Tag                    |
- 단언문

| JUnit 4                 | JUnit 5                                                      |
| ----------------------- | ------------------------------------------------------------ |
| Assert 클래스 사용           | Assertions 클래스 사용                                            |
| 단언문 메시지는 첫 번째 파라미터에 적는다 | 단언문 메시지는 마지막 파라미터에 적는다                                       |
| assertThat 메서드 사용       | assertThat 메서드를 지원하지 않는다. assertAll과 assertThrows 메서드가 추가되었다 |

- 가정문

| JUnit 4                                         | JUnit 5                                        |
| ----------------------------------------------- | ---------------------------------------------- |
| Assume 클래스 사용                                   | .Assumptions 클래스를 사용                           |
| assumeNotNull, assumedNoException 메서드를 사용할 수 있다 | assumeNotNull, assumeNoException 메서드를 사용할 수 없다 |
- JUnit 5의 기능을 사용한 JUnit5SUTTest 클래스
```Java
class JUnit5SUTTest {
    private static ResourceForAllTests resourceForAllTests;
    private SUT systemUnderTest;

    @BeforeAll 1️⃣
    static void setUpClass() {
        resourceForAllTests = new ResourceForAllTests("Our resource for all tests");
    }

    @AfterAll 2️⃣
    static void tearDownClass() {
        resourceForAllTests.close();
    }

    @BeforeEach 3️⃣
    void setUp() {
        systemUnderTest = new SUT("Our system under test");
    }

    @AfterEach 4️⃣
    void tearDown() {
        systemUnderTest.close();
    }

    @Test 5️⃣
    void testRegularWork() {
        boolean canReceiveRegularWork = systemUnderTest.canReceiveRegularWork();

        assertTrue(canReceiveRegularWork);
    }

    @Test
    void testAdditionalWork() {
        boolean canReceiveAdditionalWork = systemUnderTest.canReceiveAdditionalWork();

        assertFalse(canReceiveAdditionalWork);
    }

    @Test
    @Disabled 6️⃣
    void myThirdTest() {
        assertEquals(2, 1, "2 is not equal to 1");
    }
}
```
	- @BeforeClass와 @BeforeAll 애노테이션이 달린 메서드는 모든 테스트 메서드가 실행되기 전에 한 번 실행된다. 이때 메서드는 정적이어야 한다. JUnit 4에서는 @BeforeClass가 달린 메서드는 반드시 public 접근 제어자를 사용해야 했다. JUnit 5에서는 @BeforeAll이 달린 메서드라도 테스트 클래스에 @TestInstance(Lifecycle.PER_CLASS) 애노테이션을 추가하면 nonstatic 선언할 수 있다
	- @AfterClass와 @AfterAll 애노테이션이 달린 메서드는 모든 테스트 메서드가 실행된 후 한 번 실행된다. 이때 메서드는 정적이어야 한다. JUnit 4에서는 @AfterClass와 달린 메서드는 반드시 public 접근 제어자를 사용해야 했다. JUnit 5에서는 @AfterAll 달린 메서드라도 테스트 클래스에 @TestInstance(Lifecycle.PER_CLASS) 애노테이션을 추가하면 nonstatic 선언할 수 있다
	- @Before와 @BeforeEach 애노테이션이 달린 메서드는 각각의 테스트 전에 실행된다. JUnit 4에서 @Before가 달린 메서드는 반드시 public 접근 제어자를 사용해야 했다
	- @After와 @AfterEach 애노테이션이 달린 메서드는 각각의 테스트 후에 실행된다. JUnit 4에서 @After가 달린 메서드는 반드시 public 접근 제어자를 사용해야 했다
	- @Test가 달린 메서드는 각각 독립적으로 실행된다. JUnit 4에서 @Test가 달린 메서드는 반드시 public 접근제어자를 사용해야 했다. JUnit 4는 org.junit.Test에 JUit5는 org.junit.jupiter.api.Test에 속한다
	- 테스트 비활성화를 위해 JUnit 4는 @Ignore, JUnit 5는 @Disabled를 사용한다
	- JUnit 5는 기본 접근 제어 수준이 public에서 디폴트(package-private)로 완화

### 4.3.2 JUnit 4의 @Category와 JUnit 5의 @Tag
- JUnit 4의 @Category에 사용 예시
```Java
public interface IndividualTests {}
public interface RepositoryTests {}
```
- @Category 애노테이션이 달린 JUnit4CustomerTest 클래스
```Java
public class JUnit4CustomerTest {
    private String CUSTOMER_NAME = "John Smith";

    @Category(IndividualTests.class)
    @Test
    public void testCustomer() {
        Customer customer = new Customer(CUSTOMER_NAME);

        assertEquals("John Smith", customer.getName());
    }
}
```
- @Category 애노테이션이 달린 JUnit4CustomerREpositoryTest 클래스
```Java
@Category({IndividualTests.class, RepositoryTests.class})
public class JUnit4CustomersRepositoryTest {
    private String CUSTOMER_NAME = "John Smith";
    private CustomersRepository repository = new CustomersRepository();

    @Test
    public void testNonExistence() {
        boolean exists = repository.contains(CUSTOMER_NAME);

        assertFalse(exists);
    }

    @Test
    public void testCustomerPersistence() {
        repository.persist(new Customer(CUSTOMER_NAME));

        assertTrue(repository.contains("John Smith"));
    }
}
```
- JUnit4IndividualTestsSuite 클래스
```Java
@RunWith(Categories.class) 1️⃣
@Categories.IncludeCategory(IndividualTests.class) 2️⃣
@Suite.SuiteClasses({JUnit4CustomerTest.class, JUnit4CustomersRepositoryTest.class}) 3️⃣
public class JUnit4IndividualTestsSuite {
}
```
	- @RunWith(Categories.class) 애노테이션을 달아 특정한 runner로 테스트를 실행하도록 지정(1)
	- @Category(IndividualTests.class) 애노테이션으로 실행할 테스트의 카테고리 지정(2)
	- JUnit4CustomerTest, JUnit4CustomersRepositoryTest 클래스에서 해당 애노테이션이 달린 테스트를 찾아 실행(3)
- JUnit4RepositoryTestsSuite 
```Java
@RunWith(Categories.class) 1️⃣
@Categories.IncludeCategory(RepositoryTests.class) 2️⃣
@Suite.SuiteClasses({JUnit4CustomerTest.class, JUnit4CustomersRepositoryTest.class}) 3️⃣
public class JUnit4RepositoryTestsSuite {
}
```
	- @RunWith(Categories.class) 애노테이션을 달아 특정한 runner로 테스트를 실행하도록 지정(1)
	- @Category(RepositoryTests.class) 애노테이션으로 실행할 테스트의 카테고리 지정(2)
	- JUnit4CustomerTest, JUnit4CustomersRepositoryTest 클래스에서 해당 애노테이션이 달린 테스트를 찾아 실행(3)
- JUnit4ExcludeRepositoryTestsSuite
```Java
@RunWith(Categories.class)
@Categories.ExcludeCategory(RepositoryTests.class)
@Suite.SuiteClasses({JUnit4CustomerTest.class, JUnit4CustomersRepositoryTest.class})
public class JUnit4ExcludeRepositoryTestsSuite {
}
```
	- @RunWith(Categories.class) 애노테이션을 달아 특정한 runner로 테스트를 실행하도록 지정(1)
	- 제외할 테스트의 카테고리를 @Category.ExcludeCategory(RepositoryTests.class) 애노테이션으로 지정
	- JUnit4CustomerTest, JUnit4CustomersRepositoryTest 클래스에서 해당 애노테이션이 달린 테스트를 제외하고 실행(3)
- @Tag("individual") 태그를 지정한  JUnit5CustomerTest 클래스
```Java
@Tag("individual")
public class JUnit5CustomerTest {
    private String CUSTOMER_NAME = "John Smith";

    @Test
    void testCustomer() {
        Customer customer = new Customer(CUSTOMER_NAME);

        assertEquals("John Smith", customer.getName());
    }
}
```
	-@Tag("individual") 애노테이션은 JUnit5CustomerTest수준에 적용
- @Tag("repository") 태그를 지정한 JUnit5CustomersRepositoryTest 클래스
```Java
@Tag("repository")
public class JUnit5CustomersRepositoryTest {
    private String CUSTOMER_NAME = "John Smith";
    private CustomersRepository repository = new CustomersRepository();

    @Test
    void testNonExistence() {
        boolean exists = repository.contains("John Smith");

        assertFalse(exists);
    }

    @Test
    void testCustomerPersistence() {
        repository.persist(new Customer(CUSTOMER_NAME));

        assertTrue(repository.contains("John Smith"));
    }
}
```
	- @Tag("repository") 애노테이션이 전체 JUnit5CustomersRepositoryTest 클래스 수준에 적용

### 4.3.3 Hamcrest 매처 기능 전환하기
- JUnit4HamcrestListTest 클래스
```Java
public class JUnit4HamcrestListTest {

    private List<String> values;

    @Before 1️⃣
    public void setUp() {
        values = new ArrayList<>();
        values.add("Oliver");
        values.add("Jack");
        values.add("Harry");
    }

    @Test 2️⃣
    public void testListWithHamcrest() {
        assertThat(values, hasSize(3)); 3️⃣
        assertThat(values, hasItem(anyOf(equalTo("Oliver"), equalTo("Jack"), 4️⃣
                equalTo("Harry")))); 4️⃣
        assertThat("The list doesn't contain all the expected objects, in order", values, contains("Oliver", "Jack", "Harry")); 5️⃣
        assertThat("The list doesn't contain all the expected objects", values, containsInAnyOrder("Jack", "Harry", "Oliver")); 6️⃣
    }
}
```
- JUnit5HamcrestListTest 클래스
```Java
public class JUnit5HamcrestListTest {

    private List<String> values;

    @BeforeEach 1️⃣
    public void setUp() {
        values = new ArrayList<>();
        values.add("Oliver");
        values.add("Jack");
        values.add("Harry");
    }

    @Test 2️⃣
    @DisplayName("List with Hamcrest")
    public void testListWithHamcrest() {
        assertThat(values, hasSize(3)); 3️⃣
        assertThat(values, hasItem(anyOf(equalTo("Oliver"), equalTo("Jack"), 4️⃣
                equalTo("Harry")))); 4️⃣
        assertThat("The list doesn't contain all the expected objects, in order", values, contains("Oliver", "Jack", "Harry")); 5️⃣
        assertThat("The list doesn't contain all the expected objects", values, containsInAnyOrder("Jack", "Harry", "Oliver")); 6️⃣
    }
}
```
- JUnit4HamcrestListTest, JUnit5HamcrestListTest 에서 @Before/BeforeEach와 @DisplayName 애노테이션 정도를 제외하곤 유사함

### 4.3.4 JUnit 4 rule 과 JUnit 5의 확장 모델
- JUnit 4, 5의 예외 처리 차이
- JUnit 4 rule의 ExpectedException
```Java
public class JUnit4RuleExceptionTester {
    @Rule
    public ExpectedException expectedException = ExpectedException.none();

    private Calculator calculator = new Calculator();

    @Test
    public void expectIllegalArgumentException() {
        expectedException.expect(IllegalArgumentException.class);
        expectedException.expectMessage("Cannot extract the square root of a negative value");
        calculator.sqrt(-1);
    }

    @Test
    public void expectArithmeticException() {
        expectedException.expect(ArithmeticException.class);
        expectedException.expectMessage("Cannot divide by zero");
        calculator.divide(1, 0);
    }
}
```
	- @Rule 애노테이션이 달린 ExpectedException 타입의 객체 선언
	- ExpectedException.none()으로 ExpectedException 객체 생성
	- 테스트 대상 초기화
	- ExpectedException의 sqrt 메소드에서 예외 타입과 오류 메시지 정의
- JUnit 5의 assertThrows
``` Java
public class JUnit5ExceptionTester {
    private Calculator calculator = new Calculator();

    @Test
    public void expectIllegalArgumentException() {
        Throwable throwable = assertThrows(IllegalArgumentException.class, () -> calculator.sqrt(-1));
        assertEquals("Cannot extract the square root of a negative value", throwable.getMessage());
    }

    @Test
    public void expectArithmeticException() {
        Throwable throwable = assertThrows(ArithmeticException.class, () -> calculator.divide(1, 0));
        assertEquals("Cannot divide by zero", throwable.getMessage());
    }
}
```
	- 테스트를 실행할 대상 객체 초기화
	- assertThrow 메서드를 사용해 sqrt 메소드에서 IllegalArgumentException을 던진다고 단언하고, 오류 메시지 검증
	- divide 메소드에서 ArithmeticException을 던진다고 단언하고, 오류 메시지 검증
- JUnit 4의 Temporary Folder 대체하기
- JUnit4RuleTester
```Java
public class JUnit4RuleTester {
    @Rule
    public TemporaryFolder folder = new TemporaryFolder(); 1️⃣

    @Test
    public void testTemporaryFolder() throws IOException {
        File createdFolder = folder.newFolder("createdFolder"); 2️⃣
        File createdFile = folder.newFile("createdFile.txt"); 2️⃣
        assertTrue(createdFolder.exists()); 3️⃣
        assertTrue(createdFile.exists()); 3️⃣
    }
}
```
	- @Rule 애노테이션을 단 TemporaryFolder 타입의 객체를 선언하고 초기화했다
	- @Rule 애노테이션은 public 필드나 public 메서드에만 적용할 수 있다(1)
	- TemporaryFolder 필드를 보면 사용자 이름 폴더 아래 /Temp 폴더에 임시 폴더와 파일 생성(2)
	- 임시 폴더와 임시 파일이 만들어졌는지 검증(3)
- JUnit5TempDirTester
```Java
public class JUnit5TempDirTester {
    
    @TempDir 1️⃣
    Path tempDir; 1️⃣

    private static Path createdFile; 2️⃣

    @Test
    public void testTemporaryFolder() throws IOException {
        assertTrue(Files.isDirectory(tempDir)); 3️⃣
        createdFile = Files.createFile(tempDir.resolve("createdFile.txt")); 4️⃣
        assertTrue(createdFile.toFile().exists()); 4️⃣
    }

    @AfterAll
    public static void afterAll() {
        assertFalse(createdFile.toFile().exists()); 5️⃣
    }
}
```
	- @TempDir 애노테이션이 붙은 필드 tempDir을 선언(1)
	- 정적 변수 createdFile을 선언(2)
	- 테스트를 실행하기 전에 임시 디렉터리가 생성되었는지 확인(3)
	- 임시 디렉터리 내에 임시 파일이 생성되었는지 확인(4)
	- 테스트를 실행한 다음 리소스가 삭제되었는지 확인(5). 생성된 임시 폴더는 afterAll 메서드가 종료되면 자동을 ㅗ삭제
### 4.3.5 사용자 정의 rule을 extension으로 전환하기
- CustomRule 클래스
```Java
public class CustomRule implements TestRule { 1️⃣
    private Statement base; 2️⃣
    private Description description; 2️⃣

    @Override
    public Statement apply(Statement base, Description description) {
        this.base = base; 3️⃣
        this.description = description; 3️⃣
        return new CustomStatement(base, description); 3️⃣
    }
}
```
	- TestRule 인터페이스를 구현하는 CustomRule 클래스 선언(1)
	- 참조를 유지하기 위해 Statement 객체와 Description 객체를 인스턴스 필드로 선언(2)
	- 해당 객체들을 파라미터로 받아 CustomStatement 객체를 반환할 수 있도록 apply 메서드를 재정의(3)
- CustomStatement
```Java
public class CustomStatement extends Statement { 1️⃣
    private Statement base; 2️⃣
    private Description description; 2️⃣

    public CustomStatement(Statement base, Description description) {
        this.base = base; 3️⃣
        this.description = description; 3️⃣
    }

    @Override 4️⃣
    public void evaluate() throws Throwable { 4️⃣
        System.out.println(this.getClass().getSimpleName() + " " + description.getMethodName() + " has started"); 4️⃣
        try {
            base.evaluate(); 4️⃣
        } finally {
            System.out.println(this.getClass().getSimpleName() + " " + description.getMethodName() + " has finished"); 4️⃣
        }
    }
}
```
	- Statement 클래스를 상속하는 CustomStatement 클래스를 선언(1)
	- Statement 객체와 Description 객체에 대한 참조를 유지하기 위해 인스턴스 변수로 선언(2)
	- (3)에서 두 개의 필드를 생성자의 파라미터로 초기화
	- evaluate 메서드를 재정의하고 base.evaluate() 문장으로 원래의 테스트 진행(4)
- JUnit4CustomRuleTester
```Java
public class JUnit4CustomRuleTester {

    @Rule 1️⃣
    public CustomRule myRule = new CustomRule(); 1️⃣

    @Test
    public void myCustomRuleTest() { 2️⃣
        System.out.println("Call of a test method"); 2️⃣
    }
}
```
	- CustomRule 타입 필드를 public으로 선언하고 @Rule 애노테이션을 달았다(1)
	- myCustomRuleTest 테스트를 선언하고 그 위에 @Test 애노테이션을 달았다(2)
- CustomExtension 클래스
```Java
public class CustomExtension implements AfterEachCallback, BeforeEachCallback { 1️⃣
    @Override
    public void beforeEach(ExtensionContext extensionContext) throws Exception { 2️⃣
        System.out.println(this.getClass().getSimpleName() + " " + extensionContext.getDisplayName() + " has started"); 2️⃣
    }

    @Override
    public void afterEach(ExtensionContext extensionContext) throws Exception { 3️⃣
        System.out.println(this.getClass().getSimpleName() + " " + extensionContext.getDisplayName() + " has finished"); 3️⃣
    }
}
```
	- JUnit 5의 사용자 정의 extension 사용
	- CustomExtension 클래스는 AfterEachCallback, BeforeEachCallback 인터페이스를 다중 구현(1)
	- CustomExtension으로 확장될 테스트 클래스에서 각 테스트 메서드가 실행되기 전에 실행할 beforeEach 메서드 재정의(2)
	- CustomExtension으로 확장될 테스트 클래스에서 각 테스트 메서드가 실행된 이후 실행할 afterEach 메서드를 재정의(3)
- JUnit5CustomExtensionTester
```Java
@ExtendWith(CustomExtension.class) 1️⃣
public class JUnit5CustomExtensionTester {

    @Test 2️⃣
    public void myCustomRuleTest() { 2️⃣
        System.out.println("Call of a test method"); 2️⃣
    }
}
```
	- JUnit5CustomExtensionTester 테스트를 확장하기 위해 CustomExtension 클래스를 사용(1)
	- myCustomRuleTest 테스트를 선언하고 그 위에 @Test 애노테이션을 달았다(2)

