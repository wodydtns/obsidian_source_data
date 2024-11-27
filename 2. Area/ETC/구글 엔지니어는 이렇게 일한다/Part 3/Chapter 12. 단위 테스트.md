- 작은 테스트는 빠르고 결정적이어서 개발자들이 수시로 수행하며 즉각적인 피드백을 얻을 수 있음
- 단위 테스트는 대체로 대상 코드와 동시에 작성할 수 있을 만큼 작성이 쉬움 → 엔지니어들이 커다란 시스템을 설정하거나 이해할 필요 없이 작성 중인 코드를 검증하는데 집중 
- 빠르게 작성할 수 있으므로 테스트 커버리지를 높이기 좋음
- 각각의 테스트는 개념적으로 간단하고 시스템의 특정 부분에 집중하므로 실패 시 원인 파악이 수월
- 대상 시스템의 사용법과 의도한 동작 방식을 알려주는 문서자료 | 예제 코드 역할
- 유지보수하기 쉬워야한다
- 깨지기 쉬운 테스트 예방하기
    - 정의 : 실제로는 버그가 없음에도, 심지어 검증 대상 코드와는 관련조차 없는 변경 때문에 실패하는 테스트
    - 변하지 않는 테스트로 만들기 위해 노력하자
        - 기본적인 변경 유형
            - 순수 리팩터링
                - 외부 인터페이스는 놔두고 내부만 리팩터링한다면 테스트 변경 X
                - 성능 최적화, 코드 가독성 개선
                - 리팩터링 과정에서 테스트를 변경하는 이유
                    - 시스템의 행위 변경 → 순수 리팩토링이 아닌것
                    - 테스트의 추상화 수준이 적절하지 않음
            - 새로운 기능 추가
                - 새로운 기능이나 행위를 추가할 때 기존 행위들에 영향을 주지 X
                - 새 기능을 검증할 새로운 테스트를 작성해야하며, 기존 테스트들은 변경되지 않아야한다
            - 버그 수정
                - 버그 수정은 새로운 기능 추가와 비슷
                - 버그가 있는 것 → 기존 테스트 스위트에 빠진 게 있다는 신호
                - 버그 수정과 동시에 누락됐던 테스트를 추가해야함
            - 행위 변경
                - 시스템의 기존 행위를 변경하는 경우 → 기존 테스트 역시 변경
                - 행위를 변경하려면 혼란에 빠지거나 업무가 중단되는 사용자가 없도록 조치 필요
        - 공개 API를 이용해 테스트하자
            - **테스트도 시스템을 다른 사용자 코드와 똑같은 방식으로 호출하기**
            - 예시
                - 은행 거래 API
                
                ```java
                public void processTransaction(Transaction transaction){
                	if (isValid(transaction)){
                		saveToDatabase(transaction);
                	}
                }
                
                private boolean isValid(Transaction t){
                	return t.getAmount() < t.getSender().getBalance();
                }
                
                private void saveToDatabase(Transaction t){
                	String s = to.getSender() + "," + t.getRecipient() + "," + t.getAmount();
                	database.put(t.getId(), s);
                }
                
                public void setAccountBalance(String accountName, int balance){
                	// 잔고를 DB에 직접 기록
                }
                
                public void getAccountBalance(String AccountName){
                	// 계좌 잔고를 확인하기 위해 DB로 부터 거래 정보 읽어오기
                }
                ```
                
                - 공개 API로 테스트 → 시스템을 사용자와 똑같은 방식으로 사용
                
                ```java
                @Test
                public void shouldTransferFunds(){
                	processor.setAccountBalance("me", 150);
                	processor.setAccountBalance("you", 20);
                
                	processor.processTransaction(newTransaction()
                					.setSender("me")
                					.setRecipient("you")
                					.setAmount(100));
                	assertThat(processor.getAccountBalance("me")).isEqualTo(50);
                	assertThat(processor.getAccountBalance("you")).isEqualTo(120);
                }
                
                @Test
                public void shouldNotPerformInvalidTransactions(){
                	processor.setAccountBalance("me", 50);
                	processor.setAccountBalance("you", 20);
                
                	processor.processTransaction(newTransaction()
                					.setSender("me")
                					.setRecipient("you")
                					.setAmount(100));
                	assertThat(processor.getAccountBalance("me")).isEqualTo(50);
                	assertThat(processor.getAccountBalance("you")).isEqualTo(20);
                }
                ```
                - 단위는 개별 함수처럼 작은 것을 가리킬 수도 있고 서로 관련된 여러 패키지나 모듈의 묶음처럼 넒은 것도 지칭
                - 경험법칙
                    - 소수의 다른 클래스를 보조하는 용도가 다인 메서드나 클래스라면 독립된 단위로 생각하지 않는 것이 좋음 → 이런 메서드나 클래스는 직접 테스트하지 말고 이들이 보조하는 클래스를 통해 우회적으로 테스트
                    - 소유자의 통제 없이 누구든 접근할 수 있게 설계된 패키지나 클래스라면 거의 예외 없이 직접 테스트해야 하는 단위로 취급
                    - 소유자만이 접근할 수 있지만 다방면으로 유용한 기능을 제공하도록 설계된 패키지나 클래스 역시 직접 테스트해야 하는 단위로 설정
            - 상호작용이 아니라 상태를 테스트하자
                - state test → 메서드 호출 후 시스템 자체 관찰
                - interaction test → 호출을 처리하는 과정에서 시스템이 다른 모듈들과 헙력해 일련의 동작을 수행하는지를 확인
                - 대체로 상호작용 테스트는 상태 테스트보다 깨지기 쉬움
                - 깨지기 쉬운 테스트(상호작용 테스트)
                    
                    ```java
                    @Test
                    public void shouldWriteToDatabase(){
                    	accounts.createUser("foobar");
                      // DB의 put() 메소드 호출 여부 확인
                    	verify(database).put("foobar");
                    }
                    ```
                - 위의 코드에서 설계 의도와 다른 시나리오
                    - 시스템에 버그가 있어서 레코드가 쓰인 직후 삭제돼도 테스트는 성공하는 경우 ⇒ 실패해야하는 상황
                    - 시스템을 리팩터링해 같은 기능을 다른 API를 호출해 수행하도록 바꿨다면 이 테스틑 실패 ⇒ 성공해야하는 상황
                - 상태를 확인하는 테스트

                ```java
                @Test
                public void shouldCreateUser(){
                	accounts.createUser("foobar");
                	assertThat(accounts.getUser("foobar")).isNotNull();	
                }
                ```
                
        - 명확한 테스트 작성하기
            - 테스트가 실패하는 이유
                - 대상 시스템에 문제가 있거나 불완전 | 실패 이유가 이것이라면 버그를 고치라는 경고
                - 테스트 자체에 결함 | 대상 시스템에는 문제가 없으며 깨지 쉬운 테스트라는 뜻
            - 명확한 테스트
                - 존재 이유와 실패 원인을 엔지니어가 곧바로 알아차릴 수 있는 테스트
            - 완전하고 간결하게 만들자
                - 완전한 테스트 → 결과에 도달하기까지의 논리를 읽는 이가 이해하는 데 필요한 모든 정보를 본문에 담고 있는 테스트
                - 간결한 테스트 → 코드가 산만하지 않고, 관련 없는 정보는 포함하지 않은 테스트
                - 불완전하고 산만한 테스트
                
                ```java
                @Test
                public void shouldPerformAddition(){
                	Calculator calulator = new Calculator(new RoundingStrategy(),
                	"unused", ENABLE_COSINE_FEATURE,0.01,calculusEngine,false);
                  //newTestCalculation 는 뭐지?
                	int result = calulator.calculate(newTestCalculation());
                  // 5는 무슨 의미인가?
                	assertThat(result).isEqualTo(5); 
                }
                ```
                
                - 완전하고 간결한 테스트
                ```java
                @Test
                public void shouldPerformAddition(){
                	Calculator calulator = new Calculator();
                	int result = calulator.calculate(newCalculation(2, Operation.PLUS, 3));
                	assertThat(result).isEqualTo(5));
                }
                ```
            - 메서드가 아니라 행위를 테스트하자
                - 메서드 중심 테스트
                    
                    ```java
                    @Test
                    public void testDisplayTransactionResults(){
                    	transactionProcessor.displayTransactionResults(
                    		newUserWithBalance(
                    			LOW_BALANCE_THRESHOLD.plus(dollars(2))),
                    		new Transaction("물품",dollars(3)));
                    	
                    	assertThat(ui.getText()).contains("물품을 구입하셨습니다");
                    	assertThat(ui.getText()).contains("잔고가 부족합니다");
                    }
                    ```
                    
                - 메서드 하나의 전반을 검사하다 보면 자연스럽게 불명확한 테스트로 진행 → 메서드 하나가 몇 가지 일을 하는 경우도 있으며, 까다롭고 예외적인 상황도 포함하기 때문
                - 행위 주도 테스트
                
                ```java
                @Test
                public void displayTransactionResult_showsItemName(){
                	transactionProcessor.displayTransactionResults(
                		new User(), new Transaction("물품"));
                	assertThat(ui.getText()).contains("물품을 구입하셨습니다");
                }
                
                @Test
                public void displayTransactionResults_showsLowBalanceWarning(){
                	transactionProcessor.displayTransactionResults(
                		newUserWithBalance(
                			LOW_BALANCE_THRESHOLD.plus(dollars(2))),
                		new Transaction("물품", dollars(3)));
                	assertThat(ui.getText()).contains("잔고가 부족합니다");
                }
                
                ```
                - 테스트를 분리해서 코드 길이는 늘어났으나, 각 테스트가 훨신 명확해짐
                - 행위 주도 테스트가 메서드 중심 테스트보다 나은 이유
                    - 자연어에 더 가깝게 읽히기 때문에 힘들여 분석하지 않아도 자연스럽게 이해
                    - 테스트 각각이 더 좁은 범위를 검사하기 때문에 원인과 결과가 명확
                    - 각 테스트가 짧고 서술적이라서 이미 검사한 기능이 무엇인지 더 쉽게 확인
                - 테스트의 구조는 행위가 부각되도록 구성하자
                    - Given 요소 : 시스템의 설정을 정의
                    - When 요소 : 시스템이 수행할 작업을 정의
                    - Then 요소 : 결과를 검증
                    - 잘 구조화된 테스트
                    
                    ```java
                    @Test
                    public void transferFundsShouldMoveMoneyBetweenAccounts(){
                    	//Given : 두 개의 계좌, 각각의 잔고는 $150 & $20
                    	Account account1 = newAccountWithBalance(usd(150));
                    	Account account2 = newAccountWithBalance(usd(20));
                    
                    	//When : 첫 번째 계좌에서 두 번째 계좌로 $100 이체
                    	bank.transfetFunds(account1,account2,usd(100));
                    	
                    	//Then : 각 계좌 잔고에 이체 결과가 반영됨
                    	assertThat(account1.getBalance()).isEqualTo(use(50));
                    	assertThat(account2.getBalance()).isEqualTo(use(120));
                    }
                    ```
                    
                    - 코드를 이해하는 단계
                        - 먼저 테스트 메서드의 이름을 보고 검사하려는 행위를 간략하게 파악할 수 있음
                        - 메서드 이름으로 충분하지 않다면 행위를 형식화해 given|when|then 주석을 읽음
                        - 마지막으로 주석의 설명이 실제 코드로는 정확히 어떻게 표현됐는지 살펴볼 수 있음
                    - 복잡한 행위 주도 테스트 - when|then 블록을 교대로 배치한 테스트
                    
                    ```java
                    @Test
                    public void shouldTimeOutconnections(){
                    	//Given : 사용자는 두명
                    	User user1 = new User();
                    	User user2 = new User();
                    
                    	//And : 빈 연결 풀(타임아웃은 10분)
                    	Pool pool = new Pool(Duration.minutes(10));
                    
                    	//When : 두 사용자 모두 풀에 연결
                    	pool.connect(user1);
                    	pool.connect(user2);
                    
                    	//Then : 풀의 연결 수는 2
                    	assertThat(pool.getConnections()).hasSize(2);
                    
                    	//When :  20분 경과
                    	clock.advance(Duration.minutes(20));
                    
                    	//Then : 풀의 연결 수는 0
                    	assertThat(pool.getConnections()).isEmpty();
                    
                    	// And : 두 사용자 모두 연결이 종료
                    	assertThat(user1.isConnected()).isFalse();	
                    	assertThat(user2.isConnected()).isFalse();
                    }
                    ```
                    
                - 테스트 이름은 검사하는 행위에 어울리게 짓자
                    
                    - 행위 주도 테스트의 메소드는 이름 짓기가 더 자유로움
                    - 좋은 전략이 떠오르지 않으면 **should**로 시작하는 이름 사용
                - 테스트에 논리를 넣지 말자
                    
                    - 복잡성은 대체로 논리라는 형태로 나타남 → 논리는 명령형 요소(연산자, 반복문, 조건문 등)을 이용해 표현
                    - 테스트 코드에서는 스마트한 로직보다 직설적인 코드를 고집
                    - 서술적이고 의미있는 테스트를 위해 중복을 허용하는 것도 좋음
                - 실패 메시지를 명확하게 작성하자
                    
                    - 좋은 실패 메시지라면 기대한 상태와 실제 상태를 명확히 구분해주고 결과가 만들어진 맥락 정보도 더 제공해야한다
                    - 예시
                    
                    ```java
                    Expected an account in state CLOSED, but got account:
                    	<{name: "my-account", state:"OPEN"}
                    ```
                    
- 테스트와 코드 공유 : DRY가 아니라 DAMP!
    - DRY(Don’t Repeat Yourself)
        - 코드 중복을 최소화
        - 기능을 변경해야할 때 단 한 곳의 코드만 수정하면 끝
        - 참조에 참조를 따라가므로 코드 명확성이 떨어짐
        - 테스트 코드에는 DRY가 주는 혜택이 그리 크지 않음
    - DAMP(Descriptive And Meaningful Phrase) - 서술적이고 의미 있는 문구
        - 테스트는 DAMP를 따라야한다
        
        ```java
        @Test
        public void shouldAllowMultipleUsers(){
        	User user1 = new User().setState(State.NORMAL).build();
        	User user2 = new User().setState(State.NORMAL).build();
        
        	Forum forum = new Forum();
        	forum.register(user1);
        	forum.register(user2);
        
        	assertThat(forum.hasRegisteredUser(user1)).isTrue();
        	assertThat(forum.hasRegisteredUser(user2)).isTrue();
        }
        
        @Test
        public void shouldNotRegisterBannedUsers(){
        	User user = new User().setState(State.BANNED).build();
        
        	Forum forum = new Forum();
        	try {
        		forum.register(user);
        	}catch(BannedUserException ignored){}
        
        	assertThat(forum.hasRegisteredUser(user)).isFalse();
        }
        ```
        
	- DAMP가 DRY를 보완하는 개념
    - 공유 값
        - 이름이 모호한 공유 값
        
        ```java
        private static final Account ACCOUNT_1 = Account.newBuilder()
        				.setState(AccountState.OPEN).setBalance(50).build();
        
        private static final Account ACCOUNT_2 = Account.newBuilder()
        				.setState(AccountState.CLOSED).setBalance(0).build();
        private static final Item ITEM = Item.newBuilder()
        				.setName("치즈버거").setPrice(100).build();
        
        /* 수백 줄의 다른 테스트 */
        
        @Test
        public void canBuyItem_returnsFalseForCloasedAccounts(){
        	assertThat(store.canBuyItem(ITEM,ACCOUNT_1)).isFalse();
        }
        
        @Test
        public void canBuyItem_returnsFalseWhenBalanceInsufficient(){
        	assertThat(store.canBuyItem(ITEM,ACCOUNT_2)).isFalse();
        }
        							
        ```
        - 테스트 스위트가 커질 수록 문제 소지가 커짐
            - 각 테스트에서 왜 그 값을 선택했는지를 이해하기 어려움 → ACCOUNT_1, ACCOUNT_2는 무슨 의미인지?
        - 도우미 메서드를 사용해 값을 공유하는 예
        
        ```python
        # 도우미 메서드는 각 매개변수에 임의의 기본값을 정의해 생성자를 래핑
        def newContact(
        		firstName="Grace", lastName="Hopper", phoneNumber="555-123-4567"):
        		return Contact(firstName,lastName,phoneNumber)
        
        # 테스트는 필요한 매개변수의 값만 설정해 도우미를 호출
        def test_fullNameShouldCombineFirstAndLastNames(self):
        	def contact = newContact(firstName="에이다", lastName="러브레이스")
        	self.assertEqual(contact.fullName(), "에이다 러브레이스")
        
        # 자바처럼 이름 있는 매개변수를 지원하지 않는 언어에서는 
        # 빌더 객체를 반환하는 식으로 이름 있는 매개변수를 흉내
        private static Contact.Builder newContact(){
        	return Contact.newBuilder()
        		.setFirstName("그레이스")
        		.setLastName("호퍼")
        		.setPhoneNumber("555-123-4567");
        }
        
        # 테스트는 빌더의 메서드들을 호출해 필요한 매개변수만 덮어쓴 다음
        # build()를 호출해 최종 값을 얻음
        @Test
        public void fullNameShouldCombineFirstAndLastNames(){
        	Contact contact = newContact()
        			.setFirstName("에이다")
        			.setLastName("러브레이스")
        			.build();
        	assertThat(contact.getFullName()).isEqualTo("에이다 러브레이스");
        }
        ```
        
    - 공유 셋업
        - 테스트 스위트에 속한 테스트 각각을 수행하기 직전에 실행되는 메서드를 정의하는 것
        - 셋업 메서드는 대상 객체와 협력 객체들을 생성하는 데 매우 유용
        - 셋업 메서드에서 사용한 값을 덮어씀
        
        ```java
        private NameService nameService;
        private UserStore userStore;
        
        @Before
        public void setUp(){
        	nameService = new nameService ();
        	nameService.set("user1", "도널드 커누스");
        	userStore = new UserStore(nameService);
        }
        
        @Test
        public void shouldReturnNameFromService(){
        	nameService.set("user1","마거릿 해밀턴");
        	UserDetails user = userStore.get("user1");
        	assertThat(user.getName()).isEqualTo("마거릿 해밀턴"); 
        }
        ```
        
    - 공유 도우미 메서드와 공유 검증 메서드
        - 개념적으로 단순한 테스트
        
        ```java
        private void assertUserHasAccessToAccount(User user, Account account){
        	for (long userId : account.getUsersWithAccess()){
        		if (user.getId() == userId){
        			return;
        		}
        	}
        fail(user.getName() + " cannot access " + account.getName());
        }
        ```
        
    - 테스트 인프라 정의하기
        - 정의 : 다른 테스트 스위트와도 코드를 공유하는 방식
        - 테스트 인프라는 많은 곳에서 호출되는 만큼 의존 코드도 많음→ 테스트 인프라는 독립된 제품 대우를 해야한다