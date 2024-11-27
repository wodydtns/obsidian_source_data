## 6.1 최신 동시 애플리케이션을 위한 빌딩 블록
>[!important]
>자바 5 이전 접근 방식을 기반으로 한 멀티스레드 코드를 가지고 있다면 java.util.concurrent 를 사용하도록 해야한다

## 6.2 아토믹 클래스
- Atomic class
	- 스레드에서 안전한 가변 변수를 제공하느 ㄴ것
	- lock-free 액세스를 지원하므로, volatile 필드와 유사한 방식으로 동작
	- 예시
```Java
private static AtomicInteger nextAccountId = new AtomicInteger(1);

private final int accountId;
private double balance;

public Account(int openingBalance){
	balance = openingBalance;
	accountId = nextAccountId.getAndIncrement();
}
```
	- 각 객체가 생성될 때마다 정적 인스턴스인 AtomicInteger에서 getAndIncrement를 호출하면 int 값을 반환하고, 변경 가능한 변수를 원자 단위로 증가시킴
	- 이 아토믹은 두 개의 객체가 동일한 accountId를 공유하지 않는다는 것을 보증
- AtomicReference는 객체에 대한 아토믹 변경을 구현하는 데 사용
	- CAS(compare and swap) 연산을 사용해 해당 상태를 교체
>[!tips]
>CAS 연산은 현재 값과 기대하는 값이 일치하는지 비교하고, 일치하는 경우에만 새로운 값으로 교체하는 연산

## 6.3 잠금 클래스
- Lock 클래스
	- 다양한 유형의 잠금(reader/writer)을 추가
	- 잠금을 블록에 제한하지 않고(한 메서드에서 잠금 획득 및 다른 메서드에서 잠금 해제) 허용한다
	- 스레드가 잠금을 획득할 수 없는 경우(예:다른 스레드가 잠금을 보유한 경우), 스레드가 작업을 중단 또는 취소하거나 진행하거나 다른 작업을 수행할 수 있도록 tryLock()을 허용한다
	- 스레드가 잠금을 시도하고 일정 시간이 지난 후 포기할 수 있도록 허용한다
	- ReentrantLock
		- 기존 자바 동기화 블록에서 사용하는 잠금과 거의 동일하지만 더 유연
	- ReentrantReadWriteLock
		- 많은 리더와 적은 작성자가 있는 경우 성능을 개선할 수 있다
	- ReentrantLock을 사용한 데드락 예제
```Java
private final Lock lock = new ReentrantLock();

public boolean transferTo(SafeAccount other, int amount){
	if(accountId == other.getAccountId()){
		return false;
	}

	var firstLock = accountId < other.getAccountId() ? lock : other.lock;
	var secondLock = firstLock == lock ? other.lock : lock;

	firstLock.lock();
	try {
		secondLock.lock();
		try {
			if (balance >= amount){
				balance = balance: amount;
				other.deposit(amount);
				return true;
			}
			return false;
		}finally{
			secondLock.unlock();
		}
	}finally{
		firstLock.unlock();
	}
}
```

### 6.3.1 조건 객체
- 조건 객체
	- Condition 인터페이스 Lock 인터페이스를 구현한 잠금 객체에서 newCondition() 메서드를 호출해 생성
	- 일부 상황에서 유용할 수 있는 여러 latch, barrier를 동시성 원시 자료로 제공
## 6.4 CountDownLatch
- CountDownLatch
	- 여러 스레드가 조정 지점에 도달하고, 그 지점에서 대기하다가 배리어가 해제될 때까지 기다릴 수 있는 간단한 동시성 원시 도구
	- countDown(), await() 메서드를 사용해 레치 제어
	- countDown() - 카운트를 1만큼 감소 시킴
	- await() - 카운트가 0이 될 때까지 블록되게끔 유지

## 6.5 ConcurrentHashMap
### 6.5.1 간단하게 HashMap 이해하기
- HashMap
	- 해시 함수를 사용해 키-값 쌍을 저장할 버킷을 결정
	- 실제로 키-값 쌍은 해시된 키에 대응하는 버킷에서 시작하는 연결 리스트(hash chain이라고도 함)에 저장된다

![[Pasted image 20241029093827.png]]
- HashMap 이해를 위한 Dictionary class
```Java
public class Dictionary implements Map<String, String> {
	private Node[] table = new Node[8];
	private int size;

	@Override
	public int size(){
		return size;
	}

	@Override
	public boolean isEmpty(){
		return size == 0;
	}

	static class Node implements Map.Entry<String, String> {
		final int hash;
		final String key;
		String value;
		Node next;

		Node(int hash, String key, String value, Node next){
			this.hash = hash;
			this.key = key;
			this.value = value;
			this.next = next;
		}

		public final String getKey() { return key; }
		public final String getValue() { return value; }
		public final String toString() { return key + " = " + value; }

		public final int hashCode(){
			return Objects.hashCode(key) ^ Object.hashCode(value);
		}

		public final String setValue(String newValue){
			String oldValue = value;
			value = newValue;
			return oldValue;
		}
	
		public final boolean equals(Object o){
			if(o == this){
				return true;
			}
			if( o instanceof Node){
				Node e = (Node)o;
				if (Objects.equals(key, e.getKey() 
					&& Object.equals(value, e.getValue()))){
						return true;
				}
			}
			return false;
		}
	}

	@Override
	public String get(Object key){
		if (key == null){
			return null;
		}
		int hash = hash(key);
		for (Node e = table[indexFor(hash, table.length)]; 
			e!= null;
			e = e.next
			){
				Object k = e.key;
				if (e.hash == hash && (k == key || key.equals(k))){
					return e.value;
				}
			}
		return null;
	}

	static final int hash(Object key){
		int h = key.hashCode();
		return h ^ (h >>> 16); // 해시값이 양수인지 확인하기 위한 비트연산
	}

	static int indexFor(int h, int length){
		return h & (length: 1); // 인덱스가 표 크기 내에 있는지 확인하기 위한 비트 단위 연산
	}

	@Override
	public String put(String key, String value){
		if (key == null){
			return null;
		}
		int hash = hash(key.hashCode());
		int i = indexFor(hash, table.length);
		for(Node e = table[i]; e!=null; e=e.next){
			Object k = e.key;
			if (e.hash == hash && (k == key || key.equals(k))){
				String oldValue = e.value;
				e.value = value;
				return oldValue;
			}
		}

		Node e = table[i];
		table[i] = new Node(hash, key, value, e);

		return null;
	}
}
```
### 6.5.2 Dictionary의 한계
- 위의 Dictionary 클래스의 한계
	- 저장되는 요소의 수가 증가함에 따라 table 크기의 조정 필요
		- 해시 데이터 구조의 주 목적 중 하나는 복잡도를 O(N)에서 O(log N)으로 줄이는 것
	- hashCode() 메서드의 구현 방식에 따라 키 충돌 방어 코드

### 6.5.3 동시성 Dictionary에 대한 접근 방식
- 스레드 safety않은 위의 Dictionary 클래스를 safe하게 하는 방법
	- 모든 메서드를 synchronized로 만드는 것
		- Collections 클래스에서 제공하는 synchronizedMap()메서드로 데이터 정의
	- 불변성 활용하기
		- Collections 클래스는 가변성을 기반을 하므로, 자바 Collections API를 준수하면서 불변 데이터 구조를 모델링할 방법은 어려움
			- API를 준수할 경우, 클래스는 가변성을 가진 메서드의 구현도 제공해야함
			- 이를 편법적으로(자바 8이전) UnsupportedOpertaionException을 사용해 처리함
			- 편법 예시
```Java
public final class ImmutableDictionary extends Dictionary {
	private final Dictionary d;

	private ImmutableDictionary(Dictionary delegate){
		d = delegate;
	}

	public static ImmutableDictionary of(Dictionary delegate){
		return new ImmutableDictionary(delegate);
	}

	@Override
	public int size(){
		return d.size();
	}

	@Override
	public String get(Object key){
		return d.get(key);
	}

	@Override
	public String put(String key, String value){
		throw new UnsupportedOperationException();
	}

}
```

### 6.5.4 ConcurrentHashMap 사용하기
- ConcurrentHashMap
	- 여러 스레드가 한 번에 업데이트해도 안전함
	- HashMap의 문제
		- 동시에 수정 시 가장 위험한 동작이 항상 작은 크기로 나타나지 않는다
		- hash의 크기를 늘리면 스레드 Safety하지 않다
	- 잠금 스트라이핑(lock striping)
		- 서로 다른 체인에서 작동하는 경우 여러 스레드가 맵에 액세스할 수 있게 해줌![[Pasted image 20241029100833.png]]
	- 멀티스레드 프로그램이 있고 데이터를 공유해야하는 경우 인터페이스는 Map을 사용하고 구현은 ConcurrentHashMap으로 하면 된다
	- ConcurrentHashMap이 가지고 있는 메소드
		- putIfAbsent()
			- 키가 아직 존재하지 않는 경우 키-값 쌍을 HashMap에 추가
		- remove()
			- 키가 있는 경우 키-값 쌍을 안전하게 제거
		- replace()
			- HashMap에서 안전하게 값을 교체하기 위해 이 메서드의 두 가지 다른 형태를 제공
## 6.6 CopyOnWriteArrayList
- 연결 리스트 경우에도 여러 스레드가 리스트를 수정하려고 시도하면 경합 가능성이 발생
- CopyOnWriteArrayList
	- copy-on-write(복사본에 쓰기) 의미론을 추가해서 스레드가 안전하게 만들어진 표준 ArrayList를 대체
	- 만약 리스트 변경이 읽기 엑세스보다 더 자주 발생한다면, 이 접근 방식은 높은 성능을 보장하지 않음
	- 쓰기 작업을 위한 배열의 복사본 생성![[Pasted image 20241029104541.png]]
		- iterator는 ConcurrentModificationException이 발생하지 않는다는 것을 보장
		- 반복자가 생성된 이후 목록에 대한 추가, 제거, 변경 사항을 반영하지 않고, 리스트 요소는 변경될 수 있다 => **리스트 자체가 아닌 요소들만 변경**
		- iterator 메서드
```Java 
public Iterator<E> iterator(){
	return new COWIterator<E>(getArray(),0);
}
```
		- 변경을 수행하는 메서드들은 항상 delegate array을 복제해 수정된 새로운 배열로 교체
- CopyOnWriteArrayList에서 모니터로 사용되는 내부 락
```Java
/**
	* 모든 변경 작업을 보호하는 락
	* (ReentranLock과 내장 모니터 두 가지 중 어느 것을 사용해도 상관없을 때
	* 내장된 모니터를 선호함)
	*
	*
	
/
	final transient Object lock = new Object();

	private transient volatile Object[] array;
```
	- 이로 인해 일반적인 작업에서 ArrayList보다 효율이 좋지 못함
		- 값을 변경하는 작업의 동기화
		- Volatile 변수 저장 공간
		- ArrayList는 기본 배열의 크기 조정이 필요한 경우에만 메모리를 할당하지만, CopyOnWriteArrayList는 모든 변경 작업마다 메모리를 할당하고 복사한다
- 공유 데이터에 대한 CopyOnWriteArrayList의 접근 방식은 완벽한 동기화보다 빠르고 일관된 데이터 스냅숏이 더 중요할 때 유용할 수 있다
- CopyOnWriteArrayList 와 synchronizedList()의 트레이드오프
	- 모든 작업에 대한 동기화
		- synchronizedList
			- 서로 다른 스레드에서의 읽기 작업이 서로 블로킹
		- CopyOnWriteArrayList
			- 블로킹 불가
	- 배열 복사 발생 상황
		- synchronizedList
			- 백업 배열이 가득 찬 경우에만 복사
		- CopyOnWriteArrayList
			- 변경 사항이 발생할 때마다 복사
## 6.7 블로킹 큐
- BlockingQueue
	- BlockingQueue의 특수한 속성
		- 큐에 put()을 시도할 때 큐가 가득 차면 put을 호출한 스레드는 공간을 사용할 수 있을 때까지 대기한다
		- 큐에서 take()를 시도할 때 큐가 비어 있으면 take를 호출한 스레드가 차단된다
		- 이 두 속성들로 인해 한 스레드(또는 스레드 풀)의 처리 속도가 다른 스레드의 처리 속도를 앞지르면 더 빠른 스레드가 강제로 대기해 전체 시스템을 조절할 수 있음
		- BlockingQueue 메커니즘
		-![[Pasted image 20241029110056.png]]
	- BlockingQueue의 기본 구현
		- LinkedBlockingQueue
			- 특정 상황에서 약간 더 빠름
			- 크기가 사실상 무한대
			- put() 메서드가 이론적으로 블로킹될 수 있지만, 실제론 X => 큐에 쓰기 작업을 수행하는 스레드가 제한 없이 진행될 수 있음
		- ArrayBlockingQueue
			- 큐의 크기에 대한 정확한 한계가 있을 때 매우 효율적
			- 큐의 고정된 크기 지정
			- 큐에 대한 처리가 수신자에 의해 처리되는 것보다 생산자 스레드가 더 빠르게 객체를 넣는 경우, 어느 시점에서 큐가 완전히 가득차고 put()을 호출하는 추가 시도가 블로킹 되며, 생산자 스레드가 작업 생성 속도를 줄여야함
			- 백 프레셔(back pressure)의 한 형태
### 6.7.1 BlockingQueue API 사용
- 삽입 호출의 동작에 대한 가능성
	- 큐에 공간이 확보될 때까지 블로킹 상태가 된다
		- take(), put() 메서드로 실현
	- 실패를 나타내는 특수한 값을 반환한다
		- offer()
			- 실패를 빠르게 감지하고 false를 반환
		- poll()
			- 큐에서 가져오기에 실패하면 null을 반환
		- 예시 코드
```Java
Runnable withdraw = () -> {
	LOOP:
		while(!shutdown){
			try {
				// 타이머가 만료되면, poll()은 null을 반환
				var task = pending.poll(5, TimeUnits.SECONDS);
				if (task == null){
					continute LOOP;
				}
				// 명시적으로 자바의 LOOP 레이블을 사용해 무엇이 계속되는지
				// 명확히 한다
				var sender = task.sender(); 
				if (sender.withdraw(task.amount())){
					forDeposit.put(task);
				}else{
					failed.put(task);
				}
			}catch(InterruptedException e){
				// 문제 시 로그를 기록하고 다음 항목으로 계속 진행
			}
		}
	// 보류 중인 큐를 실패로 비우거나 로그에 기록
}
```
- 예외를 throw 한다
	- 큐 작업이 즉시 완료되지 못할 경우 예외를 던지는 메서드인 add(), remove()는 몇 가지 문제가 있다
		- 실패 시 던지는 예외(IllegalStateException, NoSuchElementException)가 런타임 예외이므로 명시적으로 처리해야함
- 자바의 일반 원칙으로 정상적이지 않은 상황을 처리하는 데 예외를 사용해야함
- 일반적으로 예외는 인스턴스화될 때 스택 추적을 구성하고 예외가 throw 될 때 stack unwindling이 발생해 사용하기에 상당한 비용이 든다
	- 예외를 즉시 throw할 예정이 아닌 경우에는 예외를 생성하지 않는 것이 좋은 관행
	- BlockingQueue API의 예외를 throw하는 형태를 사용하지 않는 것을 권장

### 6.7.2 WorkUnit 사용하기
- WorkUnit
```Java
public class WorkUnit<T> {
	private final T workUnit;

	public T getWork() {
		return workUnit;
	}
	public WorkUnit(T workUnit){
		this.workUnit = workUnit;
	}
}
```
- 추가적인 메타데이터가 도움이 되는 사례
	- 테스트 - 객체에 대한 변경 내역 표시 등
	- 성능 지표 - 완료 예상일, 서비스 품질 등
	- 런타임 시스템 정보 - 해당 인스턴스가 라우팅된 방법 등

## 6.8 퓨처
- Future의 주요 메서드
	- get()
		- 결과를 가져온다. 결과가 아직 사용 가능하지 않은 경우, 결과가 사용 가능할 때까지 블록된다
	- isDone()
		- 호출자가 계산이 완료됐는지를 확인할 수 있다. non-blocking 메서드
	- cancel()
		- 완료되기 전에 계산을 취소할 수 있다
### 6.8.1 CompletableFuture
- CompletableFuture
	- CompletableFuture\<T>  타입의 인스턴스 생성
	- CompletableFuture에 대한 참조를 가진 어떤 스레드든 complete()를 호출해 값을 제공할 수 있다
	- 서로 다른 스레드에서 서로 다른 값을 보게 할 수는 없음
	- Future는 미완료 상태나 완료 상태로 존재하고, 완료된 경우 complete()를 호출한 첫 번째 스레드에서 제공한 값이 해당 Future가 가지는 값 => CompletableFuture는 스레드 간의 값을 일관되게 처리하고 동기화하는 기능을 제공
	- 가변성 상태
	- CompletableFuture의 인스턴스는 서버 측의 역할을 수행 => Future를 충족시키고 값을 제공하는 코드의 실행과 완료를 완전히 제어
	- CompletableFuture.supplyAsync()
		- 전역 스레드 풀 사용
		- 비동기적으로 작업을 실행
		- 값을 반환하는 작업에 사용
		- 작업 결과를 반환받을 수 있습니다
		- 체이닝을 통해 연속된 작업을 구성할 수 있음
```Java
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")
    .thenAccept(System.out::println);
```

## 6.9 작업과 실행
### 6.9.1 작업 모델링하기
- Callable 인터페이스
	- 매우 일반적인 추상화
	- 호출할 수 있으면서 결과를 반환하는 코드 조각
	- 예시 - 람다 표현식
```Java
var out = getSampleObject();
Callable<String> cb = () -> out.toString();
String s = cb.call();
```
- FutureTask 클래스
	- Future 인터페이스의 구현 중 하나이고, Runnable도 구현 => FutureTask를 실행자에게 전달할 수 있다
	- FutureTask의 API는 Future와 Runnable이 결합된 형태
	- FutureTask의 생성자
		- Callable, Runnable 사용
	- 작업을 Callable로 작성하고 Runnable 형태인 FutureTask로 래핑해서 실행자에게 실행을 예약하고 필요한 경우 취소할 수 있다
	- 작업의 상태 모델
		![[Drawing 2024-10-30 09.57.14.excalidraw]]
### 6.9.2 Executor
- Executor
	- SAM(single abstract method) 형식
	- @FunctionInterface 애너테이션 표시가 없음 => 함수형 프로그래밍에서 사용하도록 고안된 것이 아님
	- 실제로 Executor는 자주 사용되지 않고 Executor를 확장하고 submit(), shutdown()과 같은 여러 생명 주기 메서드를 추가한 ExecutorService 인터페이스가 자주 사용됨
	- Executor의 사용되는 메서드
		- newSingleThreadExecutor()
		- newFixedThreadPool(int nThreads)
		- newCachedThreadPool()
		- newScheduledThreadPool(int corePoolSize)
### 6.9.3 단일 스레드 실행자
- 가장 간단한 실행자로 단일 스레드와 작업 큐(블로킹 큐)의 조합으로 구성
- 클라이언트 코드는 submit()을 통해 실행 가능한 작업을 큐에 넣고 단일 실행 스레드가 하나씩 작업을 가져와 완료될 때까지 실행하고, 그 다음 작업을 수행
- 예시
```Java
var pool = Executors.newSingleThreadExecutor();
Runnable hello = () -> System.out.println("Hello World");
pool.submit(hello);
```
	-submit()은 runnable 작업을 실행자의 작업 큐에 넣어 제출하는 역할, non-blocking
	- 메인 스레드가 작업을 실행자에 제출한 후 즉시 종료되면, 제출된 작업이 실행자의 스레드에 의해 수집, 실행되기 전 충분한 시간이 주어지지않고, shutdown() 메서드를 호출해 실행자를 종료하기 전 메인 스레드를 대기시킬 수 있다

### 6.9.4 고정 스레드 풀
- 단일 스레드 실행자의 다중 스레드 일반화 버전
- 생성 시 사용자가 명시적인 스레드 수를 제공하고 해당 수의 스레드로 풀이 생성된다
- 이런 스레드는 여러 작업을 순차적으로 실행하기 위해 재사용된다
- 모든 스레드가 사용 중인 경우, 새로운 작업은 블로킹 큐에 저장돼 스레드가 비어질 때까지 대기
- 작업 흐름이 안정적이고 예측 가능하면서, 제출된 모든 작업이 계산 시간 측면에서 거의 동일한 크기를 갖는 경우 특히 유용
- 예시
```Java
var pool = Executors.newFixedThreadPool(2);
```
	- 두 스레드는 비결정적인 바식으로 큐에서 작업을 교대로 수락
	- 특정 작업을 처리할 스레드가 보장되지 않는다

### 6.9.5 캐시드 스레드 풀
- 제한이 없는 스레드 풀
- 사용 가능한 스레드가 있는 경우 재사용하고 그렇지 않은 경우 들어오는 작업을 처리하기 위해 필요한 만큼 새로운 스레드를 재생성
- 스레드는 1분간 유휴 캐시(idle cache)에 유지되고, 해당 시간 후에도 여전히 유휴 캐시에 존재할 경우 캐시에서 제거되고 파괴
- 예시
```Java
var pool = Executors.newCachedThreadPool();
```
- 실제 작업이 종료되지 않으면, 스레드 풀은 시간이 지남에 따라 점점 더 많은 스레드를 생성하게 된다
### 6.9.6 ScheduledThreadPoolExecutor
>[!note] ScheduledThreadPoolExecutor은 예상보다 뛰어난 실행자에 대한 선택지이며 다양한 상황에서 사용할 수 있다

- 스케줄된 서비스는 일반적인 ExecutorService를 확장하고 새로운 기능을 추가
	- schedule()
		- 정의된 지연 후 일회성 작업을 실행하는 메서드
	- scheduleAtFixedRate(), scheduleWithFixedDelay()
		- 반복 작업을 예약하는 메서드
		- scheduleAtFixedRate()
			- 고정된 시간표에 따라 활성화(이전 작업 완료 여부와 무관)
		- scheduleWithFixedDelay()
			- 이전 인스턴스가 완료되고 지정된 지연 시간이 경과한 후에만 작업 활성화



	