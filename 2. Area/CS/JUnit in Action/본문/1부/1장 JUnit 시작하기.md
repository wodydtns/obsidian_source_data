## 1.1 프로그램이 제대로 동작하는지 증명하기
 - 단위 테스트
	 - 단일한 단위를 다른 단위와 격리시켜 검사하는 테스트
	 - 개발자가 자신의 코드에 적용하기 위해 사용하는, 작고 점진적으로 발전하는 테스트
	 - 주로 메서드가 API 계약 조건을 잘 따르고 있는지 검사
	 - 메서드가 API 계약을 이행할 수 없다면 테스트에서 예외가 발생해야 하며 이때 메서드는 계약을 위반했다고 할 수 있다

## 1.2 밑바닥부터 시작하기
- Calculator 클래스
```Java
public class Calculator {
	public double add(double number1, double number2){
		return number1 + number2;
	}
	
}
```
- Calculator의 테스트 클래스
```Java
public class CalculatorTest {
	public static void main(String[] args){
		Calculator calculator = new Calculator();
		double result = calculator.add(10,50);
		if (result != 60){
			System.out.println("Bad result: " + result);
		}
	}
}
```
- 조금 더 발전한 Calculator의 테스트 클래스
```Java
public class CalculatorTest {
	private int nbErrors = 0;

	public void testAdd(){
		Calculator calculator = new Calculator();
		double result = calculator.add(10,50);
		if (result != 60){
			System.out.println("Bad result: " + result);
		}
	}

	public static void main(String[] args){
		CalculatorTest test = new CalculatorTest();
		try {
			test.testAdd();
		} catch(Throwable e){
			test.nbError++;
			e.printStackTrace();
		}
		if(test.nbError > 0){
			throw new IllegalStateException(test.nbErros + " error(s");
		}
	}
}

```

### 1.2.1 단위 테스트 프레임워크 이해하기
- 단위테스트 프레임워크가 따라야하는 세 가지 규칙
	- 단위 테스트는 다른 단위 테스트와 독립적으로 실행되어야 한다
	- 프레임워크는 각 단위 테스트의 오류를 파악해 알려주어야 한다
	- 어떤 테스트를 실행할지 쉽게 정의할 수 있어야 한다

### 1.2.2 단위 테스트 추가하기
- JUnit 팀의 프레임워크 설계 시 세 가지 구체적 목표
	- 프레임워크는 유용한 테스트를 작성하ㅏ는 데 도움이 되어야 한다
	- 프레임워크는 시간이 지나도 그 가치를 유지하는 테스트를 만드는 데 도움이 되어야 한다
	- 프레임워크는 코드를 재사용해 테스트를 작성하는 비용을 낮추는 데 도움이 되어야 한다ㅏ

## 1.3 JUnit 설치하기

## 1.4 JUnit을 활용하여 테스트하기
- JUnit으로 구현한 CalculatorTest
```Java
import static org.junit.jupiter.api.Assertions.assertEquals;
import org.junit.jupiter.api.Test;

public class CalculatorTest {

	@Test
	public void testAdd(){
		Calculator calculator = new Calculator();
		double result = calculator.add(10, 50);
		assertEquals(60, result, 0); 
	}
}
```

