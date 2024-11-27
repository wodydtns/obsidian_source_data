## 9.1 GUI는 왜 단일 스레드로 동작하는가?

- GUI 프레임웍에서 여러 개의 스레드를 사용하고자 하는 시도는 많았으나, 대부분 경쟁 조건과 데드락 등의 문제가 계속 발생
- 대부분의 프레임웍은 이벤트 처리용 전담 스레드를 만들고, 전담 스레드는 큐에 쌓여 있는 이벤트를 가져와 애플리케이션에 준비돼 있는 이벤트 처리 메소드를 호출해 기능을 동작시키는 단일 스레드 이벤트 큐 모델에 정착
- 멀티스레드를 사용하는 GUI 프레임웍에서 데드락이 발생하는 이유
    - 입력된 이벤트를 처리하는 과정과 GUI 컴포넌트를 구성한 객체 지향적인 구조가 어긋나는 경우가 많음
        - 예를 들면 특정 컴포넌트의 배경 색을 변경하는 동작이라면, 먼저 애플리케이션에서 동작을 취하고, 그 동작이 해당 컴포넌트를 거쳐 OS로 넘어가 색을 칠함 ⇒ GUI 애플리케이션의 작업은 대부분 하나의 GUI 컴포넌트를 놓고 양방향으로 움직이는 과정을 거치는데, 이 과정에 속한 객체가 스레드 안전하도록 동기화시키다 보니 락이 배치되는 순서가 적절하지 않은 경우가 많아진다
    - MVC 패턴
        - MVC 패턴을 구현하는 과정에서 락의 순서가 올바르지 않게 배치될 가능성이 높다

### 9.1.1 순차적 이벤트 처리

- GUI 이벤트를 처리하는 스레드는 단 하나밖에 없기 때문에 이벤트가 항상 순차적으로 처리
- 작업을 순차적으로 처리할 경우 특정 작업 실행이 오래 걸리면 그 이후에 실행할 작업은 이전 작업이 끝날 때까지 오랜 시간 대기해야함

### 9.1.2 스윙의 스레드 한정

- 스윙 이벤트 스레드는 이벤트 큐에 쌓여 있는 작업을 순차적으로 처리하는 단일 스레드 Executor

## 9.2 짧게 실행되는 GUI 작업

- 버튼을 클릭했을 때의 흐름
    - 마우스 클릭 ⇒ 이벤트 발생 ⇒ 이벤트 리스너 ⇒ 배경색 지정
- 모델과 뷰 객체가 분리됐을 때의 작업 흐름
    - 마우스 클릭 ⇒ 이벤트 발생 ⇒ 이벤트 리스너 ⇒ 테이블 모델 업데이트 ⇒ 테이블 변경 이벤트 ⇒ 테이블 변경 ⇒ 테이블 뷰 업데이트

## 9.3 장시간 실행되는 GUI 작업

- 애플리케이션에서 실행하는 모든 작업이 금방 끝나는 작업이면서 GUI와 무관하면 애플리케이션의 모든 작업을 이벤트 스레드에서 실행해도 무관
- GUI 작업에서 사용자가 기다리는 시간이 길어질 수 있는 작업을 하는 경우도 많음
- 화면에서 이벤트가 발생했을 때 장시간 실행되는 작업을 시작하는 방법
    
    ```Java
    ExecutorService backgroundExec = Executors.newCachedThreadPool();
    ...
    button.addActionListener(new ActionListener() {
    	public void actionPerformed(ActionEvent e) {
    		backgroundExec.execute(new Runnable() {
    			public void run() { doBigComputation(); }
    		})
    	}
    }
    ```
    
- 장시간 실행되는 작업의 결과를 화면에 표시하는 코드
    
    ```Java
    button.addActionListener(new ActionListener() {
    	public void actionPerformed(ActionEvent e) {
    		button.setEnabled(false);
    		label.setText("busy");
    		backgroundExec.execute(new Runnable() {
    			public void run() {
    				try {
    					doBigComputation();
    				} finally{
    					GuiExecutor.instance().execute(new Runnable() {
    						public void run() {
    							button.setEnabled(true);
    							label.setText("idle");
    						}
    					})
    				}
    			}
    		});
    	}
    }
    ```
    

### 9.3.1 작업 중단

- 장시간 실행되는 작업 중단하기
    
    ```Java
    Future<?> runningTask = null;
    ...
    startButton.addActionListner(new ActionListener() {
    	public void actionPerformed(ActionEvent e ) {
    		if (runningTask == null) {
    			runningTask = backgroundExec.submit(new Runnable() {
    				public void run() {
    					while(moreWrok()) {
    						if (Thread.interrupted()) {
    							cleanUPPartialWork();
    							break;
    						}
    						doSomeWork();
    					}
    				}
    			});
    		}
    	}
    }));
    cancelBuuton..addActionListner(new ActionListener() {
    	public void actionPerformed(ActionEvent e ) {
    		if (runningTask != null) {
    			runningTask.cancel(true);
    		}
    	}
    }));
    ```
    

## 9.4 데이터 공유 모델

- 일반적인 간단한 GUI 애플리케이션에서는 변경 가능한 상태 변수가 모두 화면 표시 객체에 담겨 있고 이벤트 스레드를 제외하고 프로그램이 실행된 메인 스레드만 동작하는 경우가 많다

### 9.4.1 스레드 안전한 데이터 모델

- 동기화된 부분에서 스레드가 대기하는 상황때문에 GUI의 응답 속도가 형편없이 떨어지는 수준이 아니라면, 특정 데이터를 놓고 여러 스레드가 동시에 동작하는 상황은 스레드에 안전한 데이터 모델을 사용해 쉽게 해결할 수 있다
- 이 경우 특정 시점에 어떤 값을 갖고 있었는지를 안정적으로 알기는 어렵다

### 9.4.2 분할 데이터 모델

- 분할 데이터 모델
    - persentation-domain(화면 표시 부분) 과 application-domain(애플리케이션 부분)의 데이터 모델을 구분해 사용하는 형태
    - 화면 표시 부분의 데이터 모델은 항상 이벤트 스레드 내부에 제한돼 있고, 공유 모델(shared model)이라는 애플리케이션 부분의 모델은 이벤트 스레드와 애플리케이션 스레드에서 동시 사용 가능해 스레드 안전성을 고려한 구조를 가지고 있다
    - 데이터 모델을 여러 개의 스레드에서 공유해야 하는 경우라면 분할 데이터 모델을 사용하는 방법을 고려해보자. 스레드 안전하게 공유할 수 있는 데이터 모델을 만들어 사용하고자 할 때 대기 상태에 들어가는 블로킹 문제, 일관성 문제, 구현하기에 복잡하다는 있다면 스레드 안전한 모델 대신 분할 데이터 모델을 추천한다