## 14.1 JUnit 5 확장 모델 살펴보기
- JUnit 5 확장 모델은 Extension API라는 단일 개념으로 설명
	- Extension 자체는 내부에 필드나 메서드가 없는 인터페이스인 **마커 인터페이스** 
	- 마커 인터페이스
		- 구현 메서드가 따로 없는 인터페이스
		- 해당 인터페이스를 구현하는 클래스에 특별한 의미나 기능을 부여하기 위해 사용
		- Serializable , Coneable 인터페이스 등
	- 확장 지점(extension point)
		- JUnit 5 extension에선 특정 이벤트를 감시하다가 이벤트가 발생하면 테스트를 동작하게 할 수 있는데 이런 이벤트를 확장지점이라고 한다
		- 종류
			- 조건부 테스트 실행
				- 특정 조건을 충족했을 때 테스트를 실행하기 위해 사용
			- 생애 주기 콜백
				- 테스트가 생애 주기에 반응하도록 만들어야 할 때 사용
			- 파라미터 리졸브
				- 런타임에서 테스트에 주입할 파라미터를 리졸브하는 시점에 사용
			- 예외 처리
				- 특정 유형의 예외가 발생할 때 수행할 테스트 동작을 정의
			- 테스트 인스턴스 후처리
				- 테스트 인스턴스가 생성된 다음에 실행할 때 사용
	- JUnit 5 extension은 주로 프레임워크나 빌드 도구에서 사용

## 14.2 JUnit 5 extension 생성하기
- ExecutionContextExtension 클래스
```Java
import org.junit.jupiter.api.extension.ConditionEvaluationResult;
import org.junit.jupiter.api.extension.ExecutionCondition;
import org.junit.jupiter.api.extension.ExtensionContext;

import java.io.IOException;
import java.util.Properties;

public class ExecutionContextExtension implements ExecutionCondition { 1️⃣

    @Override 
    public ConditionEvaluationResult evaluateExecutionCondition(ExtensionContext context) { 2️⃣
        Properties properties = new Properties(); 3️⃣
        String executionContext = ""; 3️⃣

        try {
            properties.load(ExecutionContextExtension.class.getClassLoader() 3️⃣
                    .getResourceAsStream("context.properties")); 3️⃣
            executionContext = properties.getProperty("context"); 3️⃣
            if (!"regular".equalsIgnoreCase(executionContext) && !"low".equalsIgnoreCase(executionContext)) { 4️⃣ 
                return ConditionEvaluationResult.disabled("Test disabled outside regular and low contexts");4️⃣
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        return ConditionEvaluationResult.enabled("Test enabled on the " + executionContext + " context"); 5️⃣
    }
}
```
	- ExecutionCondition 인터페이스를 구현해 조건부 테스트 실행 extension을 만든다(1)
	- evaluateExecutionCondition 메서드를 재정의해 테스트를 활성화할지 말지 결정하는 ConditionEvaluationResult 객체를 반환한다(2)
	- Properties 객체를 만들어 context.properties 파일에서 설정 값을 읽어 온다.(3)
	- context 설정 값이 "regular"나 "low"가 아니라면 반환한 ConditionEvaluationResult는 테스트가 비활성화되도록 한다(4)
	- 정상적인 상황이라면 반환한 ConditionEvaluationResult는 테스트가 실행될 수 있도록 한다(5)

## 14.3 확장 지점을 사용하여 JUnit 5 테스트 구현하기
### 14.3.1. 승객 정보를 데이터베이스에 영속시키기
- 승객 정보를 데이터베이스에 영속시키기를 JUnit 5 extension으로 구현하기
	- 전체 테스트 묶음을 실행하기 전에 데이터베이스를 초기화하고 데이터베이스 커넥션을 얻는다
	- 테스트 묶음이 종료되었을 때 데이터베이스 커넥션을 반납한다
	- 테스트를 실행하기 전에 데이터베이스가 알려진 상태인지 확인해서 개발자가 테스트를 정확하게 실행할 수 있는지 확인 가능하게 한다
- 위의 조건을 구현
```Java
import com.manning.junitbook.ch14.jdbc.TablesManager;
import org.junit.jupiter.api.extension.*;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Savepoint;

import com.manning.junitbook.ch14.jdbc.ConnectionManager;

public class DatabaseOperationsExtension implements
	BeforeAllCallback, AfterAllCallback, BeforeEachCallback, AfterEachCallback { 1️⃣

    private Connection connection; 2️⃣
    private Savepoint savepoint; 2️⃣

    @Override
    public void beforeAll(ExtensionContext context) {
        connection = ConnectionManager.openConnection(); 3️⃣
        TablesManager.dropTable(connection); 3️⃣
        TablesManager.createTable(connection); 3️⃣
    }

    @Override
    public void afterAll(ExtensionContext context) {
        ConnectionManager.closeConnection(); 4️⃣
    }

    @Override
    public void beforeEach(ExtensionContext context) throws SQLException {
        connection.setAutoCommit(false); 5️⃣
        savepoint = connection.setSavepoint("savepoint"); 6️⃣
    }

    @Override
    public void afterEach(ExtensionContext context) throws SQLException {
        connection.rollback(savepoint); 7️⃣
    }
}
```
	- DatabaseOperationsExtension 클래스는 네 가지 생애 주기 인터페이스(BeforeAll Callback, AfterAllCallback, BeforeEachCallback, AfterEachCallback)를 구현한다(1)
	- DatabaseOperationsExtension 클래스에 데이터베이스 커넥션을 얻기 위한 필드인 connection과 테스트 실행 전 데이터베이스의 상태를 기록하고, 테스트 후 롤백하기 위한 필드인 savepoint를 선언(2)
	- BeforeAllCallback 인터페이스로부터 재정의한 beforeAll 메서드는 전체 테스트 묶음이 실행되기 전에 한 번 실행된다. 데이터베이스 커넥션을 얻고 기존 테이블을 드랍하고 , 새로 생성한다(3)
	- AfterAllCallback 인터페이스로부터 재정의한 aferAll 메서드는 전체 테스트 묶음이 실행된 다음 한 번 실행된다. 데이터베이스 커넥션을 반납한다(4)
	- BeforeEachCallback 인터페이스에서 재정의한 beforeEach 메서드는 각 테스트가 실행되기 전에 실행된다. 이는 자동 커밋 모드를 비활성화하는데, 테스트 때문에 변경된 데이터가 커밋되는 것을 막아준다(5). 그 다음 beforeEach 메서드는 테스트 실행 전에 데이터베이스의 상태를 저장한다. 그러므로 테스트가 수행된 다음 개발자는 데이터베이스의 상태를 롤백할 수 있다(6)
	- AfterEachCallback 인터페이스에서 재정의한 afterEach 메서드는 각 테스트가 수행된 다음 실행된다. 테스트가 실행되기 전으로 데이터베이스의 상태를 롤백한다(7)
- ParameterResolver 예제
```Java
import com.manning.junitbook.ch14.jdbc.ConnectionManager;
import com.manning.junitbook.ch14.jdbc.PassengerDao;
import com.manning.junitbook.ch14.jdbc.PassengerDaoImpl;
import org.junit.jupiter.api.extension.ExtensionContext;
import org.junit.jupiter.api.extension.ParameterContext;
import org.junit.jupiter.api.extension.ParameterResolutionException;
import org.junit.jupiter.api.extension.ParameterResolver;

public class DataAccessObjectParameterResolver implements ParameterResolver { 1️⃣

    @Override
    public boolean supportsParameter(ParameterContext parameterContext, ExtensionContext extensionContext) throws ParameterResolutionException {
        return parameterContext.getParameter().getType().equals(PassengerDao.class); 2️⃣
    }

    @Override
    public Object resolveParameter(ParameterContext parameterContext, ExtensionContext extensionContext) throws ParameterResolutionException {
        return new PassengerDaoImpl(ConnectionManager.getConnection()); 3️⃣
    }

}
```
	- DataAccessObjectParameterResolver 는 ParameterResolver 인터페이스를 구현한다(1). ParameterResolver 인터페이스는 테스트가 필요로 하는 파라미터를 리졸브할 때 사용
	- supportsParameter 메서드는 테스트가 필요로 하는 파라미터가 PassengerDao 타입일 경우 true를 반환한다. PassengerDao는 PassengerTest 클래스 생성자에서 주입하지 못했던 파라미터(3)
	- resolveParameter 메서드는 초기화된 PassengerDaoImpl 객체를 반환한다. 이때 PassengerDaoImpl 객체는 ConnectionManager에서 가져온 커넥션을 파라미터로 사용해 생성된다(3)

### 14.3.2 승객의 고유성 검증하기
- LogPassengerExistsExceptionExtension 
```Java
import com.manning.junitbook.ch14.jdbc.PassengerExistsException;
import org.junit.jupiter.api.extension.ExtensionContext;
import org.junit.jupiter.api.extension.TestExecutionExceptionHandler;

import java.util.logging.Logger;

public class LogPassengerExistsExceptionExtension implements TestExecutionExceptionHandler { 1️⃣
    private Logger logger = Logger.getLogger(this.getClass().getName()); 2️⃣

    @Override
    public void handleTestExecutionException(ExtensionContext context, Throwable throwable) throws Throwable { 3️⃣
        if (throwable instanceof PassengerExistsException) { 4️⃣
            logger.severe("Passenger exists:" + throwable.getMessage()); 4️⃣
            return;
        }
        throw throwable; 5️⃣
    }
}
```
	- LogPassengerExistsExceptionExtension 클래스는 TestExecutionExceptionHandler 인터페이스를 구현한다(1)
	- 클래스에의 로거를 선언하고(2), TestExecutionExceptionHandler 인터페이스를 구현해 예외가 발생했을 때 그 예외를 처리하는 handleTestExecutionException 메서드를 재정의한다(3)
	- 발생한 예외의 타입이 PassengerExistsException이라면 로그를 남기고 끝낸다(4)
	- 그렇지 않다면 예외를 다시 던져서 다른 곳에서 처리할 수 있게 한다(5)
- LogPassengerExistsExceptionExtension을 추가
```Java
@ExtendWith({ExecutionContextExtension.class,
			DatabaseOperationExtension.class,
			DatabaseAccessObjectParameterResolver.class,
			LogPassengerExistsExceptionExtension.class})
public class PassengerTest{
	[...]
}
```