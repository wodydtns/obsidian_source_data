## 3.1 소프트웨어 아키텍처의 개념과 중요성
- 소프트웨어 아키텍처
	- 소프트웨어 시스템의 기본 구조
	- 소프트웨어 요소, 소프트웨어 요소 간의 관계, 요소와 관계를 이루는 속성들로 구성
	- 소프트웨어 아키텍처의 기초를 이루는 요소는 물리적인 아키텍처의 기초만큼이나 이동이나 교체가 어렵다
### 3.1.1 첫 번째 이야기: 전화번호부
>[!note]
>작은 것이 더 낫다

### 3.1.2 두 번째 이야기: 운동화 제조 업체
>[!note]
>전체 테스트 프레임워크를 불러오는 대신 특정 모듈만 사용한다면 시간과 메모리를 절약할 수 있다

## 3.2 JUnit 4 아키텍처
### 3.2.1 JUnit 4 모놀리식 아키텍처
- 2006년에 출시된 JUnit 4는 단순한 모놀리식 아키텍처를 가지고 있다. JUnit 4의 모든 기능은 jar 파일 한 개 안에 들어 있다. 이는 개발자가 JUnit 4를 프로젝트에서 사용하고자 한다면 클래스패스에 단일 jar 파일만 추가하면 된다는 뜻이다

### 3.2.2 JUnit 4 runner
- JUnit 4 runner
	- JUnit 4 추상 클래스 Runner를 상속한 클래스
	- 테스트 실행을 담당함
	- 리플렉션을 사용해 테스트를 확장할 수 있음
	- JUnit 4 runner 예시
```Java
// 실제 class
public class Calculator {
    public double add(double number1, double number2) {
        return number1 + number2;
    }

    public double sqrt(double x) {
        if (x < 0) {
            throw new IllegalArgumentException("Cannot extract the square root of a negative value");
        }
        return Math.sqrt(x);
    }

    public double divide(double x, double y) {
        if (y == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return x / y;
    }
```

```Java
// test class
public class CustomTestRunner extends Runner {
private Class<?> testedClass; 1️⃣
	public CustomTestRunner(Class<?> testedClass){ 1️⃣
		this.testedClass = testedClass; 1️⃣ 
	}

	@Override
	public Description getDescription(){
		return Description.createTestDescription(testedClass, 2️⃣
				this.getClass().getSimpleName() + " description"); 2️⃣
	}

	 @Override
    public void run(RunNotifier notifier) {
		System.out.println("Running tests with " + this.getClass().getSimpleName() + ": " + testedClass);
        try {
            Object testObject = testedClass.newInstance(); 3️⃣
            for (Method method : testedClass.getMethods()) { 4️⃣
                if (method.isAnnotationPresent(Test.class)) { 4️⃣
                    notifier.fireTestStarted(Description 5️⃣
                            .createTestDescription(testedClass, method.getName())); 5️⃣
                    method.invoke(testObject); 6️⃣
                    notifier.fireTestFinished(Description 7️⃣
                            .createTestDescription(testedClass, method.getName())); 7️⃣
                }
            }
        } catch (InstantiationException | IllegalAccessException | InvocationTargetException e) {
            throw new RuntimeException(e);
        }
    }

}
```
	- 사용자 정의 runner인 CustomTestRunner 클래스에서는 테스트 대상 클래스에 대한 참조를 갖기 위해 멤버 변수 testedClass를 선언했다. testedClass는 생성자에서 초기화된다(1)
	- 추상 클래스 Runner에서 상속한 getDescription 메서드를 재정의한다. getDescription 메서드는 다양한 곳에서 사용할 수 있는 Descritpion 객체를 반환한다(2)
	- 추상 클래스 Runner에서 상속한 run 메서드를 재정의한다. run 메서드에서는 testedClass 변수를 인스턴스화한다(3)
	- testedClass가 가진 모든 public 메서드를 조회하고 그 중에서 @Test 애노테이션이 달린 메서드를 걸러낸다(4)
	- fireTestStarted 메서드를 호출해 리스너에게 atomic 테스트가 곧 시작한다고 알려준다(5)
	- 리플렉션을 활용해 @Test 애노테이션이 달린 메서드를 호출한다(6)
	- fireTestFinished 메서드를 호출해 리스너에게 원자적 테스트가 완료됨을 알려준다(7)
### 3.2.3 Junit 4 rule
- Junit 4 rule
	- 테스트 메서드 호출을 가로채는 컴포넌트
	- Junit 4 rule을 사용해 테스트 메서드가 실행되기 전후에 다른 작업을 수행할 수 있음
	- rule은 JUnit 4에만 적용 가능
	- RuleExceptionTester 클래스
```Java
public class RuleExceptionTester {
    @Rule 1️⃣
    public ExpectedException expectedException = ExpectedException.none(); 1️⃣

    private Calculator calculator = new Calculator(); 2️⃣

    @Test
    public void expectIllegalArgumentException() {
        expectedException.expect(IllegalArgumentException.class); 3️⃣
        expectedException.expectMessage("Cannot extract the square root of a negative value"); 4️⃣
        calculator.sqrt(-1); 5️⃣
    }

    @Test
    public void expectArithmeticException() {
        expectedException.expect(ArithmeticException.class); 6️⃣
        expectedException.expectMessage("Cannot divide by zero"); 7️⃣
        calculator.divide(1, 0); 8️⃣
    }
}
```
	- @Rule 애노테이션이 달린 ExpectedException 타입의 객체를 선언한다. @Rule 애노테이션은 public 인스턴스 필드나 public 인스턴스 메서드에만 붙일 수 있다(1). 팩터리 메서드인 ExpectedException.none()으로 ExpectedException 객체를 쉽게 생성할 수 있다
	- 테스트를 실행할 대상인 Calculator 객체를 초기화한다(2)
	- sqrt(-1) 메서드를 호출하면(5), ExpectedException 인스턴스에 예외가 만들어지고(3), 오류 메시지가 기록된다(4)
	- 한편 divide(1,0) 메서드를 호출하면(8), ExpectedException 인스턴스에 예외가 만들어지고(6), 오류 메시지가 기록된다(7)
- @Rule 애노테이션이 달린 TemporaryFolder를 사용한 사례
```Java
public class RuleTester {
    @Rule
    public TemporaryFolder folder = new TemporaryFolder(); 1️⃣
    private static File createdFolder; 2️⃣
    private static File createdFile; 2️⃣

    @Test
    public void testTemporaryFolder() throws IOException {
        createdFolder = folder.newFolder("createdFolder"); 3️⃣
        createdFile = folder.newFile("createdFile.txt"); 3️⃣
        assertTrue(createdFolder.exists()); 4️⃣
        assertTrue(createdFile.exists()); 4️⃣
    }

    @AfterClass
    public static void cleanUpAfterAllTestsRan() {
        assertFalse(createdFolder.exists()); 5️⃣
        assertFalse(createdFile.exists());5️⃣
    }
}
```
	- @Rule 애노테이션을 단 TemporaryFolder 타입의 객체를 선언하고 초기화한다. @Rule 애노테이션은 public 필드나 public 메서드에만 적용할 수 있다(1)
	- 정적 필드 createdFolder, createdFile을 선언한다(2)
	- TemporaryFolder 타입의 필드를 사용해서 사용자명 폴더 아래 /Temp 폴더에 임시 폴더와 파일을 만든다(3)
	- 임시 폴더와 임시 파일이 만들어졌는지 검증한다(4)
	- 테스트가 모두 끝난 다음에 임시 자원이 더 이상 존재하지 않는지 확인한다(5)
- TestRule 인터페이스
	- TestRule 인터페이스를 구현하려면 Statement 타입 객체를 반환하는 apply(Statement, Description) 메서드를 재정의해야 한다
	- Statement 객체는 JUnit 런타임 내의 테스트를 나타내며 evaluate 메서드로 테스트를 실행할 수 있다
	- Description 객체는 현재 테스트를 설명하는 데 사용한다
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
	- TestRule 인터페이스를 구현하는 CustomRule 클래스를 선언한다
	- 참조를 유지하기 위해 Statement 객체와 Description 객체를 인스턴스 필드로 선언한다(2). 해당 필드들을 파라미터로 전달받아 CustomStatement 객체를 반환할 수 있는 apply 메서드를 재정의한다(3)
- CustomStatement 클래스
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
        try { 4️⃣
            base.evaluate(); 4️⃣
        } finally { 4️⃣
            System.out.println(this.getClass().getSimpleName() + " " + description.getMethodName() + " has finished"); 4️⃣
        }
    }
}
```
	- Statement 클래스를 상속하는 CustomStatement 클래스를 선언한다
	- Statement 객체와 Description 객체에 대한 참조를 유지하기 위해 인스턴스 변수로 선언한다(2). 그리고 (3)에서 두 개의 필드를 생성자에서 초기화한다
	- evaluate 메서드를 재정의하고 base.evaluate() 문장을 실행해 원래의 테스트를 진행한다(4)
- CustomRuleTester 클래스
```Java
public class CustomRuleTester {

    @Rule 1️⃣
    public CustomRule myRule = new CustomRule(); 1️⃣

    @Test 2️⃣
    public void myCustomRuleTest() { 2️⃣
        System.out.println("Call of a test method");
    }
}
```
	- CustomRule 타입 필드를 public으로 선언하고 @Rule 애노테이션을 달았다(1)
	- myCustomRuleTest 테스트를 선언하고 그 위에 @Test 애노테이션을 달았다(2)
- CustomRuleTester2 클래스
```Java
public class CustomRuleTester2 {

    private CustomRule myRule = new CustomRule();

    @Rule
    public CustomRule getMyRule() {
        return myRule;
    }

    @Test
    public void myCustomRuleTest() {
        System.out.println("Call of a test method");
    }
}
```
	- CustomRuleTester2 또한 CustomStatement 클래스의 evaluate 메서드가 실행되어 테스트 수행 전후에 추가적인 메시지 출력 가능
- junit-vintage-engine
	- JUnit 5에서 JUnit 4를 사용하는 데 필요한 의존성 라이브러리
	- Junit-vintage를 활용하면 Junit 4에 추이적으로 접근할 수 있음
### 3.2.4 JUnit 4 아키텍처의 단점
- JUnit 4가 모놀리식 아키텍처는 IDE 등의 많은 도구들과의 상호작용을 고려하지 않았음
	- JUnit 4가 제공하는 API가 유연하지 못해 빌드 도구, IDE와 결합도가 높아졌다

## 3.3 JUnit 5 아키텍처
### 3.3.1 JUnit 5 모듈성
- JUnit 5 요구 사항
	- 개발자가 주로 사용하는 테스트를 작성하기 위한 API
	- 테스트를 발견하고 실행하는 데 사용하는 메커니즘
	- IDE나 빌드 도구와 쉽게 상호작용하고 테스트를 구동할 수 있는 API
- JUnit 5 아키텍처
	- JUnit Platform
		- JVM 위에서 테스트 프레임워크를 구동하기 위한 기반이 되는 플랫폼
		- 콘솔, IDE, 빌드 도구에서 테스트를 구동할 수 있는 API도 제공
	- JUnit Jupiter
		- JUnit 5에서 테스트와 extension을 만들 수 있도록 프로그래밍 모델과 확장 모델을 결합한 것
	- JUnit Vintage
		- JUnit Platform에서 Junit 3 나 4 기반의 테스트를 실행하기 위한 엔진으로, 하위 호환성을 보장
### 3.3.2 JUnit Platform
- JUnit Platform의 아티팩트
	- junit-platform-commons
		- JUnit 안에서 사용하기 위한 JUnit 의 내부 공통 라이브러리
		- 외부 사용을 권장하지 않는다
	- junit-platform-console
		- 콘솔에서 JUnit Platform의 테스트를 발견하고 실행할 수 있도록 지원한다
	- junit-platform-console-standalone
		- 모든 의존성이 포함되어 있는 실행 가능한 jar 파일
		- 이 아티팩트는 콘솔에서 JUnit Platform을 구동할 수 있는 커맨드라인 자바 애플리케이션인 콘솔 런처에서 사용
	- junit-platform-engine
		- 테스트 엔진용 public API
	- junit-platform-launcher
		- 테스트 계획을 구성하고 실행하기 위한 public API
		- 일반적으로 IDE나 빌드 도구에서 사용
	- junit-platform-runner
		- JUnit 4 환경의 JUnit Platform에서 테스트와 테스트 묶음을 실행하기 위한 runner
	- junit-platform-suite-api
		- JUnit Platform에서 테스트 묶음을 구성하기 위한 애노테이션을 갖고 있다
	- junit-platform-surefire-provider
		- Maven Surefire를 사용해 JUnit Platform에서 테스트를 발견하고 실행할 수 있게 한다
	- junit-platform-gradle-plugin
		- Gradle을 사용해 JUnit Platform에서 테스트를 발견하고 실행할 수 있게 한다
### 3.3.3 JUnit Jupiter
- JUnit Jupiter
	- 애노테이션, 클래스, 메서드를 비롯해 JUnit 5 테스트를 작성하기 위한 프로그래밍 모델
	- Extension API를 통해 JUnit 5 extension을 작성하기 위한 확장 모델
- JUnit Jupiter에 포함된 아티팩트
	- junit-jupter-api
		- JUnit 5 테스트나 extension 작성을 위한 JUnit Jupiter API
	- junit-jupiter-engine
		- 런타임에만 사용하는 JUnit Jupiter 테스트 엔진 구현체
	- junit-jupiter-params
		- JUnit Jupiter에서 파라미터를 사용한 테스트를 지원
	- junit-jupiter-migrationsupport
		- JUnit 4에서 JUnit Jupiter로의 전환을 지원하며 JUnit 4 rule을 실행하는 데 사용
### 3.3.4 JUnit Vintage
- JUnit Vintage는 JUnit Platform에서 JUnit 3 와 JUnit 4 기반 테스트를 실행하기 위한 TestEngine 제공
- JUnit Vintage에 포함된 아티팩트는 junit-vintage-engine이 유일
	- JUnit 3 와 JUnit 4 기반 테스트를 실행하기 위한 엔진 구현체
	- JUnit Vintage 사용을 위해 JUnit 3, 4 jar 파일이 필요

### 3.3.5 JUnit 5 내부 아키텍처 구성도
- 테스트 API는 테스트 엔진을 위한 다양한 기능을 제공하는데, JUnit 5 테스트를 위한 junit-jupiter-api, 레거시 테스트를 위한 junit-4.12, 서드 파티 테스트를 위한 사용자 정의 API가 있다
- 테스트 엔진은 JUnit Platform의 일부인 junit-platform-engine의 public API를 상속해 생성한 것이다
- junit-platform-launcher의 public API는 Maven이나 Gradle 같은 빌드 도구나 IDE가 JUnit Platform 내부의 테스트를 발견할 수 있도록 도와준다
- JUnit 5 내부 아키텍처
![[Pasted image 20241127101100.png]]
