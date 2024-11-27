- 테스트 대역 → 실제 구현 대신 사용할 수 있는 객체나 함수
- 테스트 대역이 소프트웨어 개발에 미치는 영향
    - 테스트 용이성
        - 테스트 대역을 사용하려면 코드베이스가 테스트하기 쉽도록 설계되어 있어야한다. 그래야 테스트에서 실제 구현을 테스트 대역으로 교체 가능
    - 적용 가능성
        - 실제로 테스트 대역을 활용하기에 적절하지 않은 경우가 많으니 되도록 실제 구현을 이용하는 것을 권장
    - 충실성
        - 테스트 대역이 실제 구현의 행위와 유사한 정도
        - 테스트에 활용하려면 대역은 일반적으로 실제보다 훨씬 단순해야함
- 테스트 대역 @구글
    - 테스트 대역을 쉽게 만들어주는 모의 객체 프레임워크를 과용하면 위험
- 기본 개념
    - 테스트 대역 예
    - 핵심 코드
    
    ```java
    class PaymentProcessor {
    	private CreditCardService creditCardService;
    	...
    	boolean makePayment(CreditCard creditCard, Money amount){
    		boolean success = creditCardService.chargeCreditCard(creditCard, amount);
    	return success;
    	}
    }
    ```
    
    - 기초적인 테스트 대역
    
    ```java
    class TestDoubleCreditCardService implements CreditCardService {
    	@Override
    	public boolean chargeCreditCard(CreditCard creditCard, Money amount){
    		return true;
    	}
    
    	// 테스트 대역 적용
    	@Test
    	public void cardIsExpired_returnFalse(){
    		boolean success = paymentProcessor.makePayment(EXPIRED_CARD,AMOUNT);
    		assertThat(success).isFalse();
    	}
    }
    ```
    
- 이어주기
    - 정의 : 제품 코드 차원에서 테스트 대역을 활용할 수 있는 길을 터줘서 테스트하기 쉽게끔 만들어주는 것
    - 대표적인 이어주기 기술 → 의존성 주입(DI)
    - 의존성 주입
    
    ```java
    class PaymentProcessor {
    	private CreditCardService creditCardService;
    	
    	PaymentProcessor(CreditCardService creditCardService){
    		this.creditCardService = creditCardService;
    	}
    }
    ```
    
    - 자바 프로젝트의 경우 - 의존성 프레임워크
        - Guice : [https://github.com/google/guice](https://github.com/google/guice)
        - Dagger : [https://github.com/google/dagger](https://github.com/google/dagger)
    - 모의 객체 프레임워크
        - 테스트 대역을 쉽게 만들어주는 소프트웨어 라이브러리
        - 모의 객체(mock) : 구체적인 동작 방식을 테스트가 지정할 수 있는 테스트 대역
        - 모의 객체 프레임워크 - Mockito
        
        ```java
        class PaymentProcessorTest {
        	...
        	PaymentProcessor paymentProcessor;
        
        	// CreditCardService 테스트 대역 생성
        	@Mock
        	CreditCardService mockCreditCardService;
        	
        	@Before
        	public void setUp() {
        		//대상 시스템에 테스트 대역 전달
        		paymentProcessor = new PaymentProcessor(mockCreditCardService);
        	}
        
        	@Test
        	public void chargeCreditCardFails_returnFalse(){
        		/* 테스트 대역에 특정한 행위 지시 
        			 chargeCreditCard() 메서드를 호출하면 무조건 return false 
        				메서드 인수로 any()를 지정했는데, 입력이 무엇이든 상관하지 말라는 의미
            */
        		when(mockCreditCardService.chargeCreditCard(any(), any()).thenReturn(false);
        		boolean success = paymentProcessor.makePayment(CREDIT_CARD,AMOUNT);
        		assertThat(success).isFalse();
        	}
        }
        ```
        
- 테스트 대역 활용 기법
    - 속이기(가짜 객체)
        - fake object → 제품 코드로는 적합하지 않지만 실제 구현과 비슷하게 동작하도록 가볍게 구현한 대역 ex) 인메모리 데이터베이스
        - 간단한 가짜 객체
        
        ```java
        AuthorizationService fakeAuthorizationService = new FakeAuthorizationService();
        AccessManager accessManager = new AccessManager(fakeAuthorizationService);
        
        //모르는 사용자의 ID 접근 불허
        assertFalse(accessManger.userHasAccess(USER_ID));
        
        //사용자 ID를 인증 서비스에 등록한 다음에는 접근을 허용합니다
        fakeAuthorizationService.addAuthorizedUser(new User(USER_ID));
        assertThat(accessManager.userHasAccess(USER_ID)).isTrue();
        
        ```
    - 뭉개기(스텁)
        - 스텁 : 원래는 없던 행위를 부여하는 과정
        - 스텁 예시
        
        ```java
        AccessManager accessManager = new AccessManager(fakeAuthorizationService);
        
        when(mockAuthorizationService.lookupUser(USER_ID)).thenReturn(null);
        assertThat(accessManager.userHasAccess(USER_ID)).isFalse();
        
        //null 이 아니면 접근 허용
        when(mockAuthorizationService.lookupUser(USER_ID)).thenReturn(USER);
        assertThat(accessManager.userHasAccess(USER_ID)).isTrue();
        ```
        
    - 상호작용 테스트하기
        - 상호작용 테스트 → 대상 함수를 실제로 호출하지 않고도 그 함수가 어떻게 호출되는지 검증하는 기법
- 실제 구현
    - 제품 코드가 사용하는 것과 똑같은 구현체 사용
    - 고전적 테스트 : 실제 구현을 선호하는 테스트 방식
    - 모의 객체 중심주의 테스트 : 모의 객체 프레임워크를 선호하는 방식
    - 격리보다 현실성을 우선하자
        - 현실적인 테스트는 대상 시스템이 올바르게 동작한다는 확신을 높여준다
    - 실제 구현을 사용할지 결정하기
        - 빠르고 결정적이고 의존성 구조가 간단하다면 실제 구현을 사용하는 것을 권장 → ex) 값 객체라면 실체 구현
        - 실행 시간
            - 속도는 단위 테스트에서 가장 중요한 특징 중 하나
            - 테스트 병렬화 → 실행 시간을 줄이는데 효과적
        - 결정성
            - 결정적 테스트 : 같은 버전의 시스템을 대상으로 실행하면 언제든 똑같은 결과를 내어주는 테스트
            - 비결정적 테스트 : 대상 시스템은 그대로인데 결과가 달라지는 테스트
            - 실제 구현은 테스트 대역보다 훨씬 복잡할 수 있어서 비결정적일 여지가 많음 → 테스트가 통제할 수 없는 외부 서비스에 의존하는 코드는 비결정성의 주범 ex) HTTP 서버로부터 웹 페이지를 읽어 내용을 확인하는 테스트는 서버가 과부하 상태이거나 웹 페이지의 내용이 변하면 실패
        - 의존성 생성
            - 테스트 대역은 생성이 쉽지만, 보통은 실제 구현을 이용할 때의 장점이 더 큼
- 속이기(가짜 객체)
    - 가짜 파일시스템
    
    ```java
    //FileSystem 인터페이스를 구현하는 가짜 객체
    public class FakeFileSystem implements FileSystem {
    	// 파일 이름과 파일 내용의 매핑 정보 저장
    	private Map<String, String> files = new HashMap<>();
    	
    	@Override
    	public void writeFile(String fileName,String contents){
    		files.add(fileName, contents);
    	}
    	@Override
    	public String readFile(String fileName){
    		String contents = files.get(fileName);
    		if (contents == null) {
    			throw new FileNotFoundException(fileName);
    		}
    		return contents;
    	}
    }
    ```
    
    - 가짜 객체가 중요한 이유
        - 가짜 객체는 테스트를 도와주는 강력한 도구
    - 가짜 객체를 작성해야 할 때
        - 가짜 객체는 실제 구현과 비슷하게 동작하기 때문에 만들려면 노력도 더 들고 도메인 지식도 더 필요함
        - 실제 객체의 행위가 변경될 때마다 발맞춰서 갱신해야 하므로 유지보수도 신경써야함
    - 가짜 객체의 충실성
        - 실제 구현과 가짜 객체가 다르게 동작한다면 가짜 객체를 이용하는 테스트들은 쓸모 없어짐 → 충실성이 중요
    - 가짜 객체도 테스트해야
        - 실제 구현의 API 명세를 만족하는지 확인하려면 가짜 객체에도 **고유한 테스트**가 딸려 있어야함
        - 실제 구현과 가짜 객체 둘 다를 대상으로 하는 공개 인터페이스 검증 테스트 작성을 통해 가짜 객체용 테스트를 작성해본다
    - 가짜 객체를 이용할 수 없다면
        - 직접 작성하기
            - API를 감싸는 클래스를 하나 만들어서 모든 API 호출이 이 클래스를 거쳐가도록 만들고 인터페이스는 똑같지만 실제 API를 이용하지는 않는 클래스(가짜 객체)를 하나 더 준비
- 뭉개기(스텁)
    - 스텁을 이용한 뭉개기 : 원래는 없는 행위를 테스트가 함수에 덧씌우는 방법
    - 응답을 시뮬레이션하기 위해 스텁 사용
    
    ```java
    @Test
    public void getTransactionCount() {
    	transactionCounter = new TransactionCounter(mockCreditCardServer);
    	// 스텁을 이용해 트랜잭션 반환
    	when(mockCreditCardServer.getTransactions()).thenReturn(
    		newList(TRANSACTION_1,TRANSACTION_2,TRANSACTION_3));
    	assertThat(transactionCounter.getTransactionCount()).isEqualTo(3);
    }
    ```
    
    - 스텁 과용의 위험성
        - 불명확해진다
            - 스텁을 이용하려면 대상 함수에 행위를 덧씌우는 코드를 추가로 작성해야한다 → 추가 코드는 읽는 이의 눈을 어지럽혀 테스트의 의도를 파악하기 어렵게 한다
        - 깨지기 쉬워진다
            - 스텁을 이용하면 대상 시스템의 내부 구현 방식이 테스트에 드러난다 → 제품의 내부가 다르게 구현되면 테스트 코드도 함께 수정해야한다→ 좋은 테스트는 사용자에게 영향을 주는 공개 API가 아닌 한, 내부가 어떻게 달라지든 영향을 받지 않아야함
        - 테스트 효과가 감소한다
            - 실제 구현의 명세에 의존하는 시스템이면 스텁을 사용하지 않는 것이 좋음 → 명세의 세부사항을 스텁이 복제해야만 하는데, 그 명세가 올바른 지 보장할 방법 X
            - 스텁을 이용하면 상태를 저장할 방법이 사라져서 대상 시스템의 특성 일부를 테스트 하기 어려울 수 있음
        - 스텁을 과용한 예
        
        ```java
        @Test
        public void creditCardIsCharged() {
        	paymentProcessor = new PaymentProcessor(
        									mockCreditCardServer, mockTransactionProcessor);
        	//테스트 대역들이 함수를 스텁해 뭉개기
        	when(mockCreditCardServer.isServerAvailable()).thenReturn(true);
        	when(mockTransactionProcessor.beginTransaction()).thenReturn(transaction);
        	when(mockCreditCardServer.initRansaction(transaction)).thenReturn(true);
        	when(mockCreditCardServer.pay(transaction, creditCard,500)).thenReturn(false);
        	when(mockCreditCardServer.endTransaction()).thenReturn(true);
        	//대상 시스템 호출
        	paymentProcessor.processPayment(creditCard, Money.dollars(500));
        	//pay() 메서드가 거래 내역으로 실제 전달 됐는 지 확인 불가
        	verify(mockCreditCardServer).pay(transaction, creditCard, 500);
        
        }
        ```
        
        - 스텁을 사용하지 않도록 리팩토링
        
        ```java
        @Test
        public void creditCardIsCharged() {
        	paymentProcessor = new PaymentProcessor(creditCardServer, transactionProcessor);
        	//대상 시스템 호출
        	paymentProcessor.processPayment(creditCard, Money.dollars(500));
          // 신용카드 서버의 상태를 조회해 지불 결과가 잘 반영 됐는지 확인
        	assertThat(creditCardServer.getMostRecentCharge(creditCard)).isEqualTo(500);
        }
        ```
        
    - 스텁이 적합한 경우
        - 실제 구현을 포괄적으로 대체하기보다 특정 함수가 특정 값을 반환하도록 해 대상 시스템을 원하는 상태로 변경하려 할 때 사용
        - 테스트들이 대체로 적은 수의 함수만 스텁으로 대체
- 상호작용 테스트하기
    - 정의 : 대상 함수의 구현을 **호출하지 않으면서** 그 함수가 어떻게 호출되는지를 검증하는 기법
    - 상호작용 테스트보다 상태 테스트를 우선하자
        - 상태 테스트 : 대상 시스템을 호출해 올바른 값을 반환하는지, 혹은 대상 시스템의 상태가 올바르게 변경되었는지 검증하는 테스트
        - 상태 테스트
        
        ```java
        @Test
        public void sortNumbers() {
        	NumberSorter numberSorter = new NumberSorter(quicksort,bubbleSort);
        	// 대상 시스템 호출
        	List sortedList = numberSorter.sortNumbers(newList(3,1,2));
        	// 반환된 리스트가 정렬되어 있는지 검증
        	assertThat(sortedList).isEqualTo(newList(1,2,3));
        }
        ```
        
        - 상호작용 테스트 문제1 → 대상 시스템이 특정 함수가 호출되었는지만 알려줄 뿐 올바르게 작동하는지 알려주지 못한다는 점 ⇒ **해당 코드가 올바르게 동작한다고 추측해야함**
        - 상호작용 테스트 문제2 → 대상 시스템의 상세 구현 방식을 활용한다는 점 → 특정 함수가 호출되는지 검증하려면 대상 시스템이 그 함수를 호출할 것임을 테스트가 알아야함 → 제품 코드의 구현 방식이 변경되면 테스트가 깨지게 됨
    - 상호작용 테스트가 적합한 경우
        - 실제 구현이나 가짜 객체를 이용할 수 없어서 상태 테스트가 불가능한 경우 → 상호작용 테스트로 특정 함수가 호출되는지 검증
        - 함수 호출 횟수나 호출 순서가 달라지면 기대와 다르게 동작하는 경우 → 상태 테스트로는 검증이 어려우므로 상호작용 테스트가 제 역할을 할 수 있음
    - 상호작용 테스트 모범 사례
        - 상태 변경 함수일 경우에만 상호작용 테스트를 우선 고려하자
            - 시스템이 의존 객체의 함수를 호출하면 두 경우 중 하나 발생
                - 상태 변경 - create , update, delete
                    - 함수가 대상 시스템 바깥 세상에 부수효과를 남긴다 예시) sendEmail(), saveRecord()
                - 상태 유지 - select
                    - 부수 효과가 없느 함수 → 시스템 바깥에 대한 정보를 반환하지만 변하는 건 아무것도 없음 ex) getUser(), findResults(), readFile()
            - 상태를 변경하는 상호작용이라면 **작성자의 코드가 다른 어딘가의 상태를 변경한다는** 유의미한 일을 한다는 의미
            - 상태 변경과 상태 유지 상호작용
            
            ```java
            @Test
            public void grantUserPermission(){
            	UserAuthorizer userAuthorizer = 
            		new UserAuthorizer(mockUserService, mockPermissionDatabase);
            	when(mockPermissionService.getPermission(FAKE_USER)).thenReturn(EMPTY);
            	
            	//대상 시스템 호출
            	userAuthorizer.grantPermission(USER_ACCESS);
            	//addPermission() -> 상태 변경 함수
            	// 호출 여부를 검증하는 상호작용 테스트 하기에 적합
            	verify(mockPermissionDatabase).addPermission(FAKE_USER,USER_ACCESS);
            	//getPermission()는 상태 유지 함수 -> 상태 테스트가 좋음
            	//verify(mockPermissionDatabase).getPermission(FAKE_USER);
            }
            ```
            - 너무 상세한 테스트는 피하자
                - 상호작용 테스트에서도 테스트 하나가 여러 행위를 검증하려는 시도하는 것보다 대상 메서드나 클래스가 제공하는 행위 하나만 검증하는데 집중해야한다
                - 어떤 함수들이 어떤 인수들을 받아 호출되는지를 너무 세세하게 검증하지 않는 것이 좋음
                - 과하게 상세한 상호작용 테스트
                
                ```java
                @Test
                public void displayGreeting_renderUserName(){
                	when(mockUserService.getUserName()).thenReturn("Fake User");
                  // 대상 시스템 호출
                	userGreeter.displayGreeting(); 
                
                	//setText()에 건네는 인수 중 하나라도 변경되면 테스트 실패
                	verify(userPrompt).setText("Fake User", "Good morning", "Version 2.1");
                	//setIcon() 호출은 이 테스트와 관련없는 부수적인 동작이지만
                  // 호출되지 않으면 테스트 실패
                	verify(userPrompt).setIcon(IMAGE_SUNSHINE);
                }
                ```
                - 적당하게 상세한 상호작용 테스트
                
                ```java
                @Test
                public void displayGreeting_renderUserName(){
                	when(mockUserService.getUserName()).thenReturn("Fake User");
                	userGreeter.displayGreeting(); 
                	verify(userPrompter).setText(eq("Fake User"), any(), any());
                }
                
                @Test
                public void displayGreeting_timeIsMorning_use_MorningSettings(){
                	setTimeOfDay(TIME_MORNING);
                	userGreeter.displayGreeting(); 
                	verify(userPrompt).setText(eq("Good Morning"), any(), any());
                		verify(userPrompt).setIcon(IMAGE_SUNSHINE);
                }
                
                ```