## 3.1 Text 블록

- Text 블록 프로젝트
    - 여러 줄에 걸쳐 확장되는 문자열 리터럴을 허용해 자바 구문에서 문자열 개념을 확장하는 것을 목표로 함
    - Text 블록의 구체적인 목표는 자바 프로그래머가 과도한 문자 이스케이프 처리의 번거로움에서 벗어날 수 있도록 도와주고, 자바는 아니지만 자바 프로그램에 포함해야 하는 코드 문자열을 읽을 수 있도록 하는 것
    - 자바 17 이후로 가능한 java SQL 문
        
        ```Java
        String query = """
        		SELECT "ORDER_ID", "QUANTITY", "CURRENT_PAIR" FROM "ORDERS"
        		WHERE "CLIENT_ID" = ?
        		ORDER BY "DATE_TIME", "STATUS" LIMIT 100;
        		""";
        ```
        

## 3.2 switch 표현식

- 자바 17 이상이 제공하는 switch 표현식
    
    ```Java
    String message = switch(month) {
    	case 1, 2, 12 -> "Winter, brrr";
    	case 3, 4, 5 -> "Spring has Sprung!";
    	case 6, 7, 8 -> "Summer is here!";
    	case 9, 10, 11 -> "Fall hasdescended";
    	default -> {
    		throw new IllegalArgumentException("Oops, that's not a month");
    	}
    }
    ```
    
    ```Java
    String message = switch(month) {
    	case JANUARY, FEBRUARY, DECEMBER -> "Winter, brrr";
    	case MARCH, APRIL, MAY -> "Spring has Sprung!";
    	case JUNE, JULY, AUGUST -> "Summer is here!";
    	case SEPTEMBER, OCTOBER, NOVEMBER -> "Fall hasdescended";
    	default -> {
    		throw new IllegalArgumentException("Oops, that's not a month");
    	}
    }
    ```

## 3.3 record

- record가 수행하는 것
    - 데이터 전용 집계 모델링을 위한 최고 수준의 수단 제공
    - 자바의 타입 시스템에서 발생할 수 있는 격차 해소
    - 공통 프로그래밍 패턴을 위한 언어 수준 문법 제공
    - 클래스 상용구 감소
- record가 없었을 때
    - toString(), hashCode(), equals(), Getter 메서드, 공개 생성자를 직접 구현해야 함
- record 예시
    
    ```Java
    public record FXOrder(int units, CurrencyPair pair, Side side,
    											double price, LocalDateTime sentAt, int ttl) {}
    ```
    
    - 해당과 같이 선언할 경우 상용구들(toString(), hashCode(), equals(), Getter 메서드, 공개 생성자) 들이 자동으로 생성됨
    - 모든 레코드 클래스의 슈퍼타입으로 새로운 클래스인 java.lang.Record가 있고, 이는 추상 클래스이며, equals(), hashCode(), toString()을 추상 메서드로 선언
- 자바 레코드는 최소한의 구문으로 패턴(데이터 캐리어, just holds fields)을 구현한 특수한 형태의 클래스
- record의 최종 디자인 결정은 named tuple

### 3.3.1 명목적 타이핑

- 명목적 타이핑
    - 클래스와 인터페이스를 통한 타입 정의
        
        ```Java
        // UserId와 ProductId는 내부 구조가 동일하지만, 다른 타입으로 취급
        public class UserId {
            private final int value;
        
            public UserId(int value) {
                this.value = value;
            }
        
            public int getValue() {
                return value;
            }
        }
        
        public class ProductId {
            private final int value;
        
            public ProductId(int value) {
                this.value = value;
            }
        
            public int getValue() {
                return value;
            }
        }
        ```
        
    - 타입 안정성
        
        ```Java
        public class UserService {
            public User getUser(UserId id) {
                // 사용자 조회 로직
            }
        }
        
        public class ProductService {
            public Product getProduct(ProductId id) {
                // 제품 조회 로직
            }
        }
        
        public static void main(String[] args) {
            UserService userService = new UserService();
            ProductService productService = new ProductService();
        
            UserId userId = new UserId(123);
            ProductId productId = new ProductId(456);
        
            User user = userService.getUser(userId);  // 정상 동작
            Product product = productService.getProduct(productId);  // 정상 동작
        
            // 컴파일 에러: ProductId를 UserId 대신 사용할 수 없음
            // User invalidUser = userService.getUser(productId);
        }
        ```
        
    - 인터페이스를 통한 계약
        
        ```Java
        public interface Identifiable {
            int getId();
        }
        
        public class User implements Identifiable {
            private final int id;
        
            public User(int id) {
                this.id = id;
            }
        
            @Override
            public int getId() {
                return id;
            }
        }
        
        public class Product implements Identifiable {
            private final int id;
        
            public Product(int id) {
                this.id = id;
            }
        
            @Override
            public int getId() {
                return id;
            }
        }
        ```
        
        - `User`와 `Product`는 같은 `Identifiable` 인터페이스를 구현하지만, 여전히 다른 타입으로 취급
    - 제네릭을 통한 타입 안정성 강화
        
        ```Java
        public class Repository<T extends Identifiable> {
            public T findById(int id) {
                // 조회 로직
            }
        }
        
        Repository<User> userRepo = new Repository<>();
        Repository<Product> productRepo = new Repository<>();
        
        User user = userRepo.findById(1);  // 정상 동작
        Product product = productRepo.findById(1);  // 정상 동작
        
        // 컴파일 에러: User와 Product는 다른 타입
        // User invalidUser = productRepo.findById(1);
        ```
        
    - reocrd가 현재 자바 빈을 사용하는 기존 코드를 대체하는 데 꼭 필요한 것은 아님
    - record는 실제 클래스이기 때문에 단순한 한 줄 선언 형식 이상의 추가적인 유연성을 제공
    - 개발자는 자동 생성된 기본값 외에 추가적인 메소드, 생성자, 정적 필드를 정의할 수 있다
    - 레코드는 정적 팩토리 메서드 있음

### 3.3.2 콤팩트 레코드 생성자

- 자바 레코드가 다른 언어에서 볼 수 있는 익명 튜플에 비해 갖는 한 가지 장점은 레코드 생성자 본문에서 레코드가 생성될 때 코드를 실행할 수 있음
- 정적 팩토리를 포함시켜 디폴트 매개변수로 생성하는 예시
    
    ```Java
    public static FXOrder of(CurrentPair pair, Side side, double price) {
    	var now = LocalDateTime.now();
    	return new FXOrder(1, pair, side, price, now, 1000);
    }
    ```
    
- record는 논리적이고 일관된 방식으로 자바의 기존 타입 시스템에 어울리는 튜플 버전인 단순한 데이터 캐리어로 설계

## 3.4 Sealed 타입

- Sealed 타입
    - Sealed로 선언된 경우, 해당 클래스는 현재의 컴파일 유닛 내에서만 확장될 수 있다
    - 하위 클래스는 현재 클래스 또는 소스 파일의 비공유 클래스 내에 중첩돼야 한다
    - 타입 X는 Y 또는 Z 중 하나(IS-EITHER-A)를 나타냄
        - 이전 자바 객체지향 모델은 타입 간의 관계에서 ‘타입 X는 Y의 일종(IS-A)’ 과 ‘타입 X는 Y를 ㄱ가지고 있음(HAS-A)’
    - final 클래스와 open 클래스 사이의 중간 지점
    - 인스턴스 대신 타입에 적용된 열거형 패턴으로 생각할 수도 있다
    - 허용된 모든 타입이 확장하는 베이스 클래스(또는 허용된 모든 타입이 구현해야 하는 공통의 인터페이스)가 있어야 한다는 것

## 3.5 instanceof의 새로운 형식

- instanceof
    - x instance of y는 값 x를 y 유형의 변수에 할당할 수 있으면 true를 반환하고, 그렇지 않으면 false를 반환
    - 형 변환 없이 사용할 수 있음(java 17버전)
        
        ```Java
        if ( o instanceof String s) {
        	System.out.println(s.length());
        }else{
        		System.out.println("Not a String");
        }
        ```
        
    - 패턴 - instanceof에 대한 패턴 매칭
        - 값에 적용될 술어(일명 테스트)
        - 값에서 추출할 로컬 변수 집합(패턴 변수라고 함)
        - **술어가 값에 적용된 경우에만 패턴 변수(pattern variable)가 추출**

## 3.6 패턴 매칭과 프리뷰 기능

- instanceof에서 switch로 패턴 매칭을 확장하는 프리뷰 기능
    
    ```Java
    var msg = switch(o) {
    	case String s -> "String of length:" + s.length();
    	case Integer i -> "Integer:" + i;
    	case null, default -> "Not a String of Integer";
    }
    System.out.println(msg);
    ```
    
- 가드 패턴(guard pattern)
    - 패턴의 술어와 가드가 모두 참인 경우에만 전체 패턴에 일치
        
        ```Java
        case FXFill f && f.units() < 100 -> f.orderId() + " Small Fill";
        case FXFill f                    -> f.orderId() + " Fill" + f.units();
        ```
        
