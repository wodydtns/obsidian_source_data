>[!note]
>자바의 동시성 API
> - 블록 구조 동시성(block-structured concurrency) 혹은 동기화 기반 동시성(synchronization-based concurrency) 혹은 클래식 동시성이라 불리는 오래된 API
> - java.util.concurrent라는 최신 API
## 5.1 동시성 이론 입문
### 5.1.1 본인은 이미 동시성에 대해 알고 있다?
- 동시성 주제는 매우 크며, 좋은 멀티스레드 개발은 어렵고 경험이 풍부한 최고의 개발자들에게도 여전히 문제를 일으킨다

### 5.1.2 하드웨어
- 동시성과 멀티스레딩의 몇 가지 기본적인 사실
	- 동시성 프로그래밍은 근본적으로 성능에 관한 것
	- 실행 중인 시스템의 성능이 직렬 알고리즘으로 작동하기에 충분하다면 기본적으로 동시성 알고리즘을 구현할 이유가 없다
	- 최신 컴퓨터 시스템에는 여러 개의 프로세싱 코어가 있으며, 오늘날에는 휴대폰에도 2~4개의 코어가 있다
	- 모든 자바 프로그램은 멀티스레드이며, 애플리케이션 스레드가 하나만 있는 프로그램도 마찬가지다
- JVM 자체가 다중 코어를 사용할 수 있는 멀티스레드 바이너리( JIT 컴파일, GC 등)
- 표준 라이브러리에도 런타임이 관리하는 동시성을 사용해 일부 실행 작업에 대해 멀티스레드 알고리즘을 구현한  API도 포함되어 있다

### 5.1.3 암달의 법칙
>[!important]
>암달의 법칙을 가장 쉽게 생각하는 방법은 s가 0과 1 사이인 경우 달성할 수 있는 최대 속도 향상은 1 / s
>- 실제 암달의 법칙 공식 : T(N) = s + (1/N) * (T1 -s)
>- s는 통신 오버헤드를 의미

- 암달의 법칙
	- 여러 실행 단위에서 작업을 공유할 때의 효율성을 추론하기 위한 간단하고 대략적인 모델
	- 작업 처리를 위해 더 작은 단위로 세분화할 수 있는 작업이 하나 더 있다는 전제 => 이를 통해 여러 실행 단위를 사용해서 작업을 완료하는 데 걸리는 시간을 단축할 수 있다
	- 프로세서(또는 작업을 수행할 스레드)가 N개인 경우, 경과 시간은 T1 / N이 될 것이라고 단순하게 예상할 수 있다 => 이 모델에서 실행 단위를 추가해서 N을 늘리기만 하면 원하는 만큼 빠르게 작업을 완료할 수 있다
- 통신 오버헤드(communcation overhead)(종종 계산의 직렬 부분\<serial part>)
	- 작업을 세분화하고 재결합하는 데는 오버헤드가 발생한다
	- 어느 정도의 오버헤드(s)는 발생하므로 처리 유닛 투입 수와 상관없이 작업완료에는 항상 최소 s * T1이 소요된다
	- 실제론 N이 증가함에 따라 s가 나타내는 작업의 분할이 더 복잡해지고 더 많은 시간을 필요로 할 수 있다

### 5.1.4 자바의 스레드 모델 설명
- 자바 스레드 모델의 기본 개념
	- 공유되며 기본적으로 보이는 가변상태(변수나 상태)
	- 운영체제에 의한 선점형 스레드 스케줄링
- 기본 개념의 중요한 측면
	- 객체는 프로세스 내의 모든 스레드 간에 쉽게 공유할 수 있다
	- 객체에 대한 참조가 있는 모든 스레드에서 객체는 변경할 수 있다
	- 스레드 스케줄러(운영체제)는 언제든지 코어에 스레드를 할당하거나 제거할 수 있다
	- 메서드는 실행 중에도 교체할 수 있어야 한다(그렇지 않으면 무한 루프가 있는 메서드가 CPU를 영원히 점유할 수 있다)
	- 앞의 항목처럼 예측할 수 없는 스레드 스왑이 발생해 메서드가 '반쯤 완료'되어 객체가 일관된 상태로 남을 위험이 있다
	- 취약한 데이터를 보호하기 위해 객체를 '잠글'수 있다

### 5.1.5 배운 교훈
- 자바는 멀티스레드 프로그래밍을 기본으로 지원하는 최초의 주류 프로그래밍 언어였다
- 디자인 포스(design force)
	- 시스템에 중요한 반복적인 고려 사항
	- 개발자가 동시성 코드 작성 경험이 많아질수록 이런 상황에 직면한다
## 5.2 디자인 콘셉트
### 5.2.1 안전성과 동시성 타입 안전성
- 안전성
	- 다른 동작들이 동시에 발생하는 상황에서도 객체 인스턴스가 항상 자기 일관성을 유지하도록 보장하는 것
	- 객체들의 시스템이 이런 속성을 가지면, 해당 시스템은 safe, concurrently typesafe하다고 한다
- 동시성은 객체 모델링과 타입 안전성 개념을 확장하는 것
- 동시성 타입 안전성(concurrent type safety)
	- 객체에 대한 타입 안전성과 기본 개념 동일
	- 다른 스레드가 동시에 다른 CPU 코어에서 동일한 객체에 대해 잠재적으로 수행할 수 있는 것
	- 예시
```Java
public class StringStack {
	private String[] values = new String[16];
	private int current = 0;
	public boolean push(String s){
		// 예외 처리 생략
		if (current < values.length){
			values[current] = s;
			// 여기서 컨텍스트 스위칭 발생 -> 객체가 비일관적인 상태에 놓임
			current + 1;
			return true;
		}
		return false;
	}

	public String pop(){
		if (current < 1){
			return null;
		}
		current = current: 1;
		return values[current];
	}

}
```
	-단일 스레드 클라이언트 코드에서는 문제 없음
	- 선점형 스레드 스케줄링에서 문제를 일으킬 수 있음
	- 다른 스레드에서 객체 상태를 확인하면 상태의 일부(values)는 업데이트됐지만 다른 부분 current은 업데이트 되지 않음

### 5.2.2 활상성
- live system(활상성)
	- 모든 시도된 작업이 최종적으로 진행되거나 실패하는 시스템
	- 활성성이 없는 시스템은 기본적으로 고착상태로, 성공을 향해 진행, 실패도 하지 않는다
- 일시적인 실패가 발생하는 원인
	- 잠금 또는 잠금 획득 대기
	- 네트워크 I/O와 같은 입력 대기
	- 에셋의 일시적인 오류
	- 스레드 실행을 위한 CPU 시간이 충분하지 않음
- 영구적인 장애의 일반적인 원인
	- deadlock
	- 복구 불가능한 에셋 문제
	- 신호 누락(missed signal)
### 5.2.3 성능
- 성능 
	- 주어진 리소스로 시스템이 얼마나 많은 작업을 수행할 수 있는지를 측정하는 척도
### 5.2.4 재사용성
- 재사용성
	- 쉽게 재사용할 수 있도록 설계된 동시성 시스템은 때때로 매우 바람직하지만, 구현하기가 쉬운 것은 아니다

### 5.2.5 설계 요인은 어떻게 그리고 왜 충돌하는가?
- 좋은 동시성 시스템을 설계하기 어려운 주요 요인
	- 안전성과 활성성은 서로 상반된 관계에 있다. 
	- 재사용 가능한 시스템은 일반적으로 내부 구조를 노출하므로 안전성과 관련된 문제가 발생할 수 있다
	- 단순하게 작성된 안전한 시스템은 보통 성능이 좋지 않다. 안전성을 보장하기 위해 일반적으로 많은 lock의 사용을 필요로 하기 때문이다
- 활성성과 성능을 적절하게 유지하는 방법
	1. 각 서브 시스템의 외부 통신을 최대한 제한한다. 데이터 숨김은 안전은 지원하는 강력한 도구
	2. 각 서브 시스템의 내부 구조를 가능한 한 결정론적으로 만든다. 예를 들어 각 서브 시스템이 동시적이고 비결정적인 방식으로 상호작용하더라도 각 서브 시스템의 스레드와 객체에 대한 정적인 지식을 바탕으로 설계한다
	3. 클라이언트 애플리케이션이 준수해야 할 정책 접근 방식을 적용한다. 이 기법은 강력하지만 사용자 애플리케이션이 규칙을 어길 경우 디버깅이 어렵다
	4. 필요한 동작을 문서화한다. 이 방법은 가장 약한 대안이지만, 코드가 매우 일반적인 맥락에서 배포돼야 하는 경우에 때때로 필요하다
### 5.2.6 오버헤드의 원인
- 동시성 시스템의 오버헤드 원인
	- 모니터(예: 잠금 및 조건 변수)
	- 콘텍스트 스위치 수
	- 스레드 수
	- 스케줄링
	- 메모리 위치
	- 알고리즘 설계
## 5.3 블록 구조 동시성(자바 5 이전)
### 5.3.1 동기화와 잠금
- 자바에서 synchronized 키워드를 통해 한 번에 한 스레드만 객체의 동기화된 블록이나 메서드를 통과하도록 강제한다. 다른 스레드가 들어가려고 하면 JVM에 의해 일시 중단된다
- 위와 같은 구조를 가끔 크리티컬 섹션(critical section)이라고 부르고, 자바보다 c++에서 더 일반적으로 사용
- 자바의 동기화와 잠금의 기본적인 사실들
	- 원시 자료형이 아닌 객체만 잠글 수 있다
	- 객체들의 배열을 잠가도 개별 객체는 잠기지 않는다
	- 동기화된 메서드는 전체 메서드를 포괄하는 synchronized(this){...} 블록과 동일하다고 생각할 수 있다(바이트 코드에서는 다르게 표현
	- static synchronized 메서드는 잠글 인스턴스 객체가 없기 때문에 Class 객체를 잠근다
	- Class 객체를 잠가야 하는 경우 하위 클래스에서 접근 방식에 따라 동작이 다를 수 있으므로, 명시적으로 잠가야 하는지 아니면 getClass()를 사용해서 잠가야 하는지 신중하게ㅐ 고려해야 한다
	- 내부 클래스의 동기화는 외부 클래스와 독립적이다
	- synchronized 메서드는 메서드 시그니처의 일부가 아니기 때문에 인터페이스의 메서드 선언에 표시될 수 없다
	- 동기화되지 않은 메서드는 잠금 상태를 고려하지 않고 신경 쓰지 않는다. 따라서 동기화되지 않은 메서드는 동시에 실행할 수 있으며, 동기화돤 메서드가 실행 중일 때도 진행할 수 있다
	- 자바의 잠금은 재진입(reentrant)이 가능하다. 재진입이란, 한 스레드가 이미 보유한 잠금을 다시 얻을 수 있는 특성을 말한다
		- 실제로 자바에서 재진입이 불가능한 잠금 방식을 모방 가능
		- ReentrantLock 클래스
### 5.3.2 스레드의 상태 모델
- 자바 스레드의 상태 모델
![[Drawing 2024-10-25 13.37.14.excalidraw]]
- 자바 스레드 객체는 NEW 상태로 생성
- 스케줄러는 새로운 스레드를 실행 대기열에 배치하고 나중에 실행할 코어를 찾는다
- 코어에 배치, 실행, 실행 대기열에 배치되는 스케줄링 프로세스 내내 자바 쓰레드 객체는 RUNNABLE 상태로 유지
- 자바 스레드에 해당하는 OS 스레드가 실행을 중단한 경우 해당 스레드 객체는 TERMINATED 상태로 전환
- 스레드 대기 상태 구현 방법
	- 프로그램 코드에서 Thread.sleep() 호출
		- sleep()에 설정한 시간 동안 절전 모드 요청
		- TIMED_WAITING 상태로 전환되고 OS가 타이머를 설정
		- 타이머가 만료되면 대기 상태가 해제되고 다시 실행할 준비가 돼 실행 대기열에 다시 배치
	- 스레드가 어떤 외부 조건이 충족될 때까지 기다려야 함을 인식하고 Object.wait()를 호출
		- 자바의 객체별 모니터의 조건 측면을 사용함
		- 일반적으로 OS에서 조건이 충족됐따는 신호를 보낼 때까지(다른 스레드가 현재 객체에 Object.nofity()를 호출) WAITING 상태로 전환되어 무한 대기
	- 스레드가 I/O 대기, 다른 스레드가 보유한 LOCK을 획득하기 위해 BLOCKED 상태로 전환될 수 있음
### 5.3.3 완전히 동기화된 객체
- 완전히 동기화된 객체(fully synchronized object)의 조건
	- 모든 필드는 모든 생성자에서 일관된 상태로 초기화된다
	- public 필드가 없다
	- 객체 인스턴스는 private 메서드에서 반환된 후에도 일관성이 부종된다
	- 모든 메서드는 유한한 시간 안에 종료된다는 것이 증명돼야 한다
	- 모든 메서드는 동기화돼야 한다
	- 어떤 메서드도 불일치한 상태에서 다른 인스턴스의 메서드를 호출하지 않는다
	- 어떤 메서드도 불일치한 상태에서 현재 인스턴스의 비공개 메서드를 호출하지 않는다
- 완전 동기화된 클래스
```Java
public class FSOAccount {
	// 공개 필드가 없다
	private double balance;

	public FSOAccount(double openingBalance){
		// openingBalance > 0 체크, 그렇지 않으면 throw
		balance = openingBalance; // 모든 필드가 생성자에서 초기화
		
	}

	// 모든 메서드가 synchronized된다
	public synchronized boolean withdraw(int amount){
		// openingBalance > 0 체크, 그렇지 않으면 throw
		if (balance >= amount ){
			balance = balance: amount;
			return true;
		}
		return false;
	}
	public synchronized void deposit(int amount){
		balance = balance + amount;
	}

	public synchronized double getBalance(){
		return balance;
	}
}
```
	- 클래스가 안전하고 라이브 상태를 유지함
	- balance에 대한 모든 접근을 조정하기 위해 synchronized를 사용하므로, 이 잠금이 결국 성능을 저하시킨다

### 5.3.4 교착상태
>[!note]
>완전 동기화된 객체 접근 방식에서 교착상태는 '제한된 시간' 원칙을 위반했기 때문에 발생한다. 코드가 other.deposit()을 호출할 때 자바 메모리 모델에서 차단된 모니터라 언제 해제될 지 보장하지 않기 때문에 코드가 얼마나 오래 실행될지 보장할 수 없다
- deadlock을 벗어나는 방법
	1. 모든 스레드에서 항상 동일한 순서로 잠금을 획득하는 것

### 5.3.5 왜 동기화해야 하는가?
- CPU의 timesharing
	- 단일 코어에서 스레드가 커졌다 꺼졌다 하는 것
- 동시성과 스레드에 대한 단일 코어(왼쪽)와 멀티 코어(오른쪽)의 사고 방식
![[Drawing 2024-10-25 14.08.52.excalidraw]]
- 오브젝트에 대한 변경 
![[Drawing 2024-10-25 14.16.08.excalidraw]]
### 5.3.6 volatile 키워드
- volatile
	- volatile 필드의 규칙
		- 스레드가 보는 값은 사용하기 전에 항상 주 메모리에서 다시 읽는다
		- 바이트코드 명령이 완료되기 전에 스레드가 쓴 모든 값은 항상 주 메모리로 플러시된다
	- 핵심
		- 메모리 위치에 대해 하나의 작업만 허용하고, 이 작업은 즉시 메모리에 플러시
		- 단일 읽기, 쓰기 가능하지만 그 이상은 불가능
	- volatile 변수 사용
		- 변수의 쓰기가 변수의 현재 상태(읽기 상태)에 의존하지 않는 경우에만 사용해야한다
		- volatile이 단일 작업만 보장하기 때문
		- 예시
			- "++", "--" 연산자는 volatile에서 안전하게 사용할 수 없다 => 변수의 1을 증가시시 때문
			- 위의 예시를 상태 종속 업데이트(state dependent update)라고 한다
	- volatile은 잠금을 도입하지 않아 교착상태를 유발할 수 없다
### 5.3.7 스레드 상태와 메서드
>[!note]
>자바 스레드의 상태 모델은 RUNNABLE 스레드가 실제로 그 정확한 순간에 물리적으로 실행 중인지 아니면 실행 대기열에서 대기중인지 구분하지 않는다
>
- 스레드 상태
	- NEW : Thread 객체가 생성됐지만 실제 OS 스레드는 아직 생성되지 않았다
	- RUNNABLE : 스레드가 실행 가능한 상태다. OS가 스레드 스케줄링을 담당한다
	- BLOCKED : 스레드가 실행 중이 아니며, 잠금을 획득해야 하거나 시스템 호출 중인 상태이다
	- WAITING : 스레드가 실행되지 않았고, Object.wait() 또는 Thread.join()을 호출했다
	- TIMED_WAITING : 스레드가 실행되지 않았고, Thread.sleep()을 호출했다
	- TERMINATED : 스레드가 실행되지 않으며, 실행이 완료됐다
- 스레드 프로세스
	- 스레드의 실제 생성은 start()메서드에 의해 수행 -> 이는 네이티브 코드를 호출해 관련된 시스템 호출(예 : linux의 clone())을 실제로 수행 -> 스레드가 생성되고 스레드의 run() 메서드에서 코드 실행이 시작
- 자바의 표준 스레드 API
	- 메타데이터를 읽는 메서드 그룹
		- getId()
		- getName()
		- getState()
		- getPriority()
		- isAlive()
		- isDaemon()
		- isInterrupted()
	- 스레드에 속성을 구성하는 메서드 그룹
		- setDaemon()
		- setName()
		- setPriority()
		- setUncaughtExceptionHandler()
	- 다른 스레드와 상호작용하기 위한 스레드 제어 메서드 그룹
		- start()
		- interrupt()
		- join()
- 스레드 메서드 사용 예시
```Java
Runnable r = () -> {
	var start = System.currentTimeMillis();
	try{
		Thread.sleep(1000);
	}catch(InterruptedException e){
		e.printStackTrace();
	}
	var thisThread = Thread.currentThread();
	System.out.println(thisThread.getName() +
		" slept for " + (System.currentTimeMillis(): start));
};

var t = new Thread(r);
t.setName("Worker");
// 워커 스레드 생성
t.start();
Thread.sleep(100);
// 메인 스레드가 워커 스레드를 인터럽트하고 깨운다
t.interrupt();
t.join();
System.out.println("Exiting");
```
- 스레드 인터럽트
	- 예시
```Java
var t = new Thread(()-> {while (true); });
t.start();

// 스레드 interrupt 시 join()이 차단
t.interrupt();
t.join();
```
- 예외나 스레드로 작업하기
	- 스레드 API에서 스레드를 시작하기 전 잡히지 않은 예외 처리기를 스레드 추가할 수 있다
```Java
var badThread = new Thread(()-> {
	throw new UnsupportedOpertaionExcepion();
});

// 정의된 함수형 인터페이스 핸들러
badThread.setUncaughtExcepotionHandler((t,e)->{
	System.err.printf("Thread %d '%s' has thrown exception " +
					"%s at line %d of %s");
	t.getId();
	t.getName();
	e.toString();
	e.getStackTrace()[0].getlineNumber(),
	e.getStackTrace()[0].getFileName());
})
```
- 더 이상 사용되지 않는 스레드 메서드
	- Thread.sleep()
		- 경고 없이 다른 스레드를 종료 시키고, 종료된 스레드가 잠긴 객체를 안전하게 처리할 방법이 없어 안전하게 쓰기 어려운 메소드
	- stop()
		- 다른 스레드가 정확히 어디를 실행하고 있는지 아는 것이 불가능하다
		- 강제 종료된 스레드에선 ThreadDeath 예외가 트리거되며, try 블록에서 이 예외를 방어하는 것은 불가능하다 => 예외는 즉시 종료된 스레드의 스택을 언바인딩하고 모든 모니터를 잠금 해제한다
		- 이로 인해 잠재적으로 손상된 객체가 다른 스레드에게 공개돼 문제가 발생해 stop()은 안전하게 사용할 수 없다
	- suspend(), resume()
		- 모니터를 해제하지 않기 때문에, 중단된 스레드가 잠근 동기화된 코드에 접근하려고 할 경우 영원히 블록될 수 있다
### 5.3.8 불변성
- immutable object(불변 객체)
	- 상태가 없거나 final 필드만 있는 객체
	- 이 객체는 상태를 변경할 수 없어 일관성을 유지하고 안전하고 살아 있는 객체
	- 이 객체는 객체를 초기화 시 필요한 모든 값을 생성자에 전달해야해 많은 매개변수가 포함된 복잡한 생성자의 호출이 발생할 수 있음
	- 그래서 불변 객체 대신 팩토리 메서드를 사용함
	- 잠재적 문제
		- 팩토리 메소드에 전달할 매개변수가 많을 수도 있음
			- 이를 해결하기 위해 "빌더 패턴"을 사용할 수 있음
- 빌더
	- 단일 추상 메서드 타입(single abstract method type)
	- 람다 표현식의 타깃 타입으로 사용할 수 있다 
	- 빌더의 목적은 불변 인스턴스를 생성하는 것으로 함수나 콜백을 나타내는 것이 아니라 상태를 수집하는 것 => 빌더를 함수형 인터페이스로 사용할 수 있지만 실제로 결코 유용하지 않음
	- 빌더는 thread safety 하지 않음 
## 5.4 자바 메모리 모델
>[!tips]
> 객체지향 프로그래밍의 Has-A와 is-A는 'Happens-Before', 'Synchronizes-With' 와 다름
> 


- 기본 개념
	- Happens-Before
		- 이 관계는 한 블록의 코드가 완전히 완료된 후 다른 블록이 시작할 수 있음을 나타낸다
	- Synchronizes-With
		- 어떤 동작은 주 메모리와의 객체의 뷰를 동기화한 후 계속한다
		- 예시
			![[Drawing 2024-10-28 08.29.45.excalidraw]]
- 자바 메모리 모델의 주요 규칙
	- 모니터의 unlock 동작은 'Synchronizes-With' 이후 발생하는  lock 동작과 동기화한다
	-  volatile 변수에 대한 쓰기 동작은 'Synchronizes-With' 이후에 발생하는 해당 변수의 읽기 동작과 동기화한다
	- 만약 동작 A가 동작 B와 'Synchronizes-With' 관계에 있다면, 동작 A는 동작 B보다 먼저 발생한다
	- 하나의 스레드 내에서 프로그램 순서에 따라 동작 A가 동작 B 이전에 나온다면, 동작 A는 동작 B 이전에 'Happens-Before' 관계를 형성한다
	- ***"획득하기 전에 먼저 릴리스가 발생한다"***
		- 즉, 쓰기 스레드가 보유하고 있는 잠금은 다른 작업(읽기 포함)에서 잠금을 획득하기 전에 해제된다
		- 예를 들어 한 스레드가 volatile 변수에 값을 쓰면 나중에 해당 변수를 읽는 모든 스레드가(다른 쓰기가 발생하지 않았따는 가정 하에) 쓰여진 값을 볼 수 있도록 보장
- 동시성 프로그램의 합리적이고 예측 가능한 동작을 보장하기 위한 가이드라인
	- 생성자의 완료는 해당 객체의 finalizer가 실행되기 전에 'Happens-Before' 관계를 형성한다. 즉 객체는 완전히 생성돼야 파이널라이저가 실행될 수 있다
	- 새로운 스레드의 첫 번째 동작은 해당 스레드가 시작하는 동작과 'Synchronized-With' 관계를 형성한다. 즉 새로운 스레드가 시작되기 전에 이전 동작들의 동기화를 보장한다
	- Thread.join()을 조인되는 스레드의 마지막(및 다른 모든) 동작과 'Synchonized-With' 관계를 형성한다.즉 조인하는 스레드는 조인되는 스레드의 모든 동작과 완료될 때까지 기다린다
	- 만약 x가 y보다 먼저 발생하고, y가 z보다 먼저 발생한다면, x는 z보다 먼저 발생한다는 'Happens-Before' 관계의 이행성(transitivity) 규칙이 발생한다. 즉 앞선 동작과 뒤이은 동작 간의 순서 관계는 이행성을 가진다
	-  Happens-Before 전이성 규칙
		![[Drawing 2024-10-28 08.47.54.excalidraw]]
## 5.5 바이트코드로 동시성 이해하기
- 코드 예시
```Java
public class Account {
	private double balance;
	
	public Account(int openingBalance){
		balance = openingBalance;
	}

	public boolean rawWithdraw(int amount){
		if (balance >= amount){
			balance = balance - amount;
			return true;
		}
		return false;
	}
	
	public void rawDeposit(int amount){
		balance = balance + amount;
	}

	public double getRawBalance(){
		return balance;
	}

	public boolean safeWithdraw(final int amount){
		synchronized (this){
			if (balance >= amount){
				balance = balance - amount;
				return true;
			}
		}
		return false;
	}

	public void safeDeposit(final int amount){
		synchronized (this){
			balance = balance + amount;
		}
	}
	
	public double getSafeBalance(){
		synchronized(this){
			return balance;
		}
	}
}
```

### 5.3.1 업데이트 손실
- lost update(업데이트 손실)
```bash
public void rawDeposit(int);
	Code:
		0: aload_0
		1: aload_0
		2: getfield #2 // balance 필드:D - 객체에서 balance를 읽음
		5: iload_1
		6: i2d
		7: dadd // amount를 더한다
		8: putfield #2 // balance 필드:D - 새로운 balance를 객체에 기록한다
		11 : return
```

- 업데이트 손실은 애플리케이션 스레드의 비결정적인 스케줄링으로 인해 읽기와 쓰기의 바이트코드 시퀸스로 끝날 수 있는 문제
```bash
A0: aload_0
	A1: aload_0
	A2: getfield #2 // balance 필드:D - 스레드 A가 balance에서 값을 읽는다
	A5: iload_1
	A6: i2d
	A7: dadd
//... Context Switch A -> B
		B0 : aload_0
		B1 : aload_0
		B2 : getfield // balance 필드:D - 스레드 B가 balance에서 A가 읽은 것과 동일한 값을 읽는다
		B5 : iload_1
		B6 : i2d
		B7 : dadd
		B8 : putfield  // balance 필드:D - 스레드 B가 새로운 값을 balance에 쓴다

//... Context Switch B -> A
	A8 : putfield // balance 필드:D - 스레드 A가 balance 값을 덮어쓴다. B는 업데이트한 값을 잃어버린다
	A11 : return
	
```
	- dadd 연산 코드는 업데이트된 balance가 스택에 배치되는 지점이지만 모든 메서드 호출에는 고유한 비공개 평가 스택이 있음 => B7지점에서 업데이트된 balance의 복사본이 두 개 존재하며, 하나는 A의 평가 스택에, 다른 하나는 B 스택에 있다
	- B8 과 A8에서 두 개의 putfield 연산이 실행되지만 A8이 B8에 배치된 값을 덮어쓴다.
	- 이로 인해 두 입금이 모두 성공한 것처럼 보이지만 실제로는 하나만 표시되는 상황이 발생한다
### 5.5.2 바이트코드에서의 동기화
>[!important]
>동기화가 제공하는 보호를 얻기 위해서는 모든 메서드가 올바르게 동기화를 사용해야한다

- synchronized의 실제 동작
```Java
	public boolean safeWithdraw(final int amount){
		synchronized (this){
			if (balance >= amount){
				balance = balance - amount;
				return true;
			}
		}
		return false;
	}
```
- safeWithdraw의 JVM 바이트 코드 변환 결과
```bash
public boolean safeWithdraw(int);
	Code:
		0: aload_0
		1: dup
		2: astore_2
		3 : monitorenter -> synchronized 블록의 시작
		4 : aload_0
		5 : getfield #2 // 필드 balance:D
		8 : iload_1
		9 : i2d
		10 : dcmpl
		11: iflt 29 // balance를 확인하는 if 문
		14 : aload_0
		15 : aload_0
		16 : getfield #2 // 필드 balance:D
		19 : iload_1
		20 : i2d
		21 : dsub
		22 : putfield #2 // 필드 balance:D - 새로운 값을 balance 필드에 쓰기
		25 : iconst_1
		26 : aload_2
		27 : monitorexit -> synchronized 블록 끝
		28 : ireturn -> 메서드에서 반환
		29 : aload_2
		30 : monitorexit -> synchronized 블록 끝
		31 : goto 39
		34 : astore_3
		35 : aload_2
		36 : monitorexit -> synchronized 블록 끝
		37 : aload_3
		38 : athrow
		39 : iconst_0
		40 : ireturn -> 메서드에서 반환
```
- balance 확인에 성공하면 0-28이 점프 없이 실행되고, 실패하면 바이트코드 0-11이 실행되고, 29-31로 점프한 다음 39-40으로 점프
- 언뜻 보기에 어떤 상황에서도 바이트코드 34-38이 실행되지 않는다 => "예외 처리"
- 자바 코드는 **boolean으로 선언**했지만, 바이트코드에서는 ireturn이라는 **정수형 반환 오퍼레이션 코드** 
	- 실제로 bytes, shorts, chars, booleans에 대한 명령어의 변환 형태는 존재하지 않는다
	- 이런 타입들은 컴파일 과정에서 int로 대체 => **"타입 소거(type erasure)"**
- 자바 소스 컴파일러는 monitorenter가 포함된 메서드를 통과하는 모든 코드 경로에서 메서드가 종료되기 전에 mointorexit가 실행되도록 보장함
- 클래스 로딩 시 클래스파일 검증기는 이 규칙을 우회하려는 모든 클래스를 거부
- "동기화는 자바에서 협력적인 메커니즘이다"는 주장의 예시
```bash
public boolean safeWithdraw(int);
	Code:
		0: aload_0
		1: dup
		2: astore_2
		3 : monitorenter -> synchronized 블록의 시작
		4 : aload_0
		5 : getfield #2 // 필드 balance:D
		8 : iload_1
		9 : i2d
		10 : dcmpl
		11 : iflt
		14 : aload_0
		15 : aload_0
		16 : getfield #2 // 필드 balance:D
		19 : iload_1
		20 : i2d
		21 : dsub
		22 : putfield  #2 // 필드 balance:D
		25 : iconst_1
		26 : aload_2
		27 : monitorexit
		28 : ireturn
```

### 5.5.3 synchronized 메서드
- 동기화된 메서드의 경우
```Java
public synchronized boolean safeWithdraw(final int amount){
	if( balance >= amount ){
		balance = balance: amount;
		return true;
	}
	return false;

}
```
- 결과
```bash
public synchronized boolean safeWithdraw(int);
Code:
	0 : aload_0
	1 : getfield #2 // 필드 balance:D
	4 : iload_1
	5 : id2
	6 : dcmpl
	7 : iflt 23
	10 : aload_0
	// ... 모니터 명령이 없음
```
	 - monitor 명령이 없음
	 - invoke 명령 실행 시 바이트코드 인터프리터는 가장 먼저 메서드가 synchronized 인지 확인
	 - synchronized 면 인터프리터는 먼저 적절한 잠금을 획득하려고 시도해 다른 코드 경로로 진행
	 - 메서드에 ACC_SYNCHRONIZED가 없으면 이런 검사를 수행하지 않음

### 5.5.4 동기화되지 않은 읽기
>[!important]
>데이터를 쓰는 메서드와 읽는 메서드 모두 동기화해야 안전하다
- 동기화되지 않은 읽기를 사용하면 "반복 불가능한 읽기(nonrepreatable read)"
	- 실제 시스템 상태와 일치하지 않는 값이 발생할 가능성이 있다
	- 데이터베이스 트랜잭션 도중 읽기 수행 시 이와 같은 현상이 발생할 수 있다
- **'단순히 읽기'만 하는 경우에도 탈출구는 없다. 하나의 코드 경로라도 동기화를 올바르게 사용하지 않으면 그 결과로 나오는 코드는 스레드 안전하지 않고, 다중 스레드 환경에서 올바르지 않다**

### 5.5.5 교착상태 다시 보기
- 예시 코드
```Java
public boolean naiveSafeTransferTo(Account other, int mount){
	synchronized(this){
		if (balance >= amount){
			balance = balance: amount;
			synchronized(other){
				other.rawDeposit(amount);
			}
			return true;
		}
	}
	return false;
}
```
	- 스레드 A : 객체 A에서 객체 B로 송금
	- 스레드 B : 객체 B에서 객체 A로 송금
	- 이 경우 스레드 A,B 모두 진전되지 않고, 스레드 A,B가 스스로 객체 잠금을 해제할 수 없어 두 스레드 모두 동기화 메커니즘에 의해 영구적으로 차단되고 메서드 호출이 완료되지 않는다
### 5.5.6 교착상태 해결, 다시 보기
>[!important]
>모든 스레드가 항상 같은 순서로 잠금을 획득하도록 해야한다

- 스레드에 순서를 지정하는 코드
```Java
private static int nextAccountId = 1;

private final int accountId;

private static synchronized int getAndIncrementNextAccountId() {
	int result = nextAccountId;
	nextAccountId = nextAccountId + 1;
	return result;

}

public Account(int openingBalance){
	balance = openingBalance;
	atmFeePercent = 0.01;
	accountId = getAndIncrementNextAccountId();

}
// accountId이 final로 선언해 변경되지 않아 동기화가 필요 없다
public int getAccountId(){
	return accountId();
}

public boolean safeTransferTo(final Account other, fianl int amount){
	if ( accountId == other.getAccountId()){
		// 본인 계좌로 이체 불가
		return false;
	}

	if (accountId < other.getAccountId()){
		synchronized (this){
			if (balance >= amount){
				balance = balance: amount;
				synchronized (other){
					other.rawDeposit(amount);
				}
				return true;
			}
		}
		return false;
	}else{
		synchronized (other){
			if ( balance >= amount ){
				balance = balance: amount;
				other.rawDeposit(amount);
				return true;
			}
		}
	}
	return false;
}
}
```

### 5.5.7 volatile 액세스
- volatile shutdown 패턴
```Java
public class TaskManager implements Runnable {
	private volatile boolean shutdown = false;
	
	public void shutdown(){
		shutdown = true;
	}
	@Override
	public void run(){
		while(!shutdown){
			// do some work
		}
	}

}
```
	- shutdown 플래그가 false일 때는 작업 단위가 처리
	- shutdown 플래그가 true면 TaskManager는 현재 작업 단위를 완료한 후 while 루프를 종료하고 스레드가 우아한 종료(graceful shutdown)로 깔끔하게 종료
	-  volatile 변수에 대한 쓰기는 해당 변수의 모든 후속 읽기와 'Happends-Before' 관계가 있다
	- 다른 스레드가 TaskManager 객체에 대해 shutdown()을 호출하면 즉시 플래그가 true로 변경되고 다음 작업 단위가 승인되기 전에 플래그의 다음 읽기에서 그 변경의 효과를 볼 수 있다
```bash
public class TaskManager implements java.lang.Runnable {
	private volatile boolean shutdown;
	
	public Taskmanager();
		Code:
			0 : aload_0
			1 : invokespecial #1 // java/lang/Object."<init>" 메서드: ()v
			4 : aload_0
			5 : iconst_0
			6 : putfield #2 // shutdown 필드 : Z
			9 : return
	public void shutdown();
		Code:
			0 : aload_0
			1 : iconst_1
			2 : putfield #2 // shutdown 필드 : Z
			5 : return
	public void run();
		Code:
			0 : aload_0
			1 : getfield #2 // shutdown 필드 : Z
			4 : ifne 18
			7 : goto
			10 : return

}
```
	- shutdown의 volatile 특성은 필드 정의를 제외하곤 어디에도 없음
	- 오퍼레이션 코드에서 추가적인 힌트를 찾을 수 없고, 표준적인 getfield, putfield 오퍼레이션 코드를 사용해서 엑세스





