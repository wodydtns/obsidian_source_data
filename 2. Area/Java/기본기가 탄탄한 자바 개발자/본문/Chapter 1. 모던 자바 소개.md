1.1 언어와 플랫폼

> [!important]  
> 오늘날 JVM은 실제로 프로그램을 실행하는 데 있어 범용적이며 언어에 구애를 받지 않는다. 이것이 사양을 분리한 이유 중 하나다  

- 자바 언어
    - 정적 타입의 객체지향 언어
- 자바 플랫폼
    - 플랫폼은 소프트웨어가 실행될 수 있는 환경을 제공하는 것
    - 자바 플랫폼에서 클래스 파일 형태로 제공된 코드를 링크하고 실행하는 JVM
- 자바 소스 코드 실행과정
    
    ![[image.png]]
    
- 가장 널리사용되는 많은 자바 프레임워크가 클래스를 로드할 때 클래스를 변환해서 instrumentation이나 자바 모듈 식별(alternative lookup)과 같은 동적 동작을 주입
- javac는 gcc와 같은 컴파일러가 아니라 자바 소스 코드를 위한 클래스 파일 생성기에 불과하고 자바 생태계에서 실제 컴파일러는 JIT 컴파일러이다

## 1.2 새로운 자바 릴리스 모델

- OpenJDK의 mainline 개발 모델
    - 새로운 기능은 브랜치에서 개발되며 코드가 완성되 경우에만 병합된다
    - 릴리스는 엄격한 시간 주기에 따라 발생할 수 있다
    - 기능이 늦으면 릴리스를 지연시키지 않고 다음 릴리스로 이월된다
    - 현재 트렁크의 최신 상태는 이론상 항상 배포 가능해야 한다
    - 필요한 경우 긴급 수정안을 마련해서 언제든지 내보낼 수 있다
    - 별도의 OpenJDK 프로젝트가 장기적인 미래 방향을 탐험하고 연구하는 데 사용된다

## 1.3 향상된 타입 추론(var 키워드)

- 다이아몬드 구문(diamond syntax) - 자바 7
    
    ```Java
    Map<Integer, Map<String, String>> userList = new HashMap<>();
    ```
    
- 자바 8의 람다 표현식 지원을 위한 타입추론 추가
    
    ```Java
    Function<String, Integer> lengthFn = s -> s.length();
    ```
    
- Local Variable Type Inference(LVTI, 로컬 변수 타입 추론) - 자바 10
    
    ```Java
    var names = new ArrayList<String>();
    ```
    
- var의 의도는 자바 코드의 장황함을 줄이고 다른 언어에서 자바로 오는 프로그래머에게 숙함을 주기 위해서이다. 동적 타이핑을 도입한 것이 아니고, 자바 변수들은 항상 정적 타입을 유지한다. 단지 모든 경우에 명시적으로 작성할 필요가 없을 뿐이다
- 자바에서의 타입 추론은 지역적이며, var의 경우 알고리즘은 로컬 변수의 선언만 검사한다. **이는 필드, 메서드 인수, 반환 타입에는 사용할 수 없다는 것을 의미**
- 컴파일러는 일종의 제약 조건 해결 알고리즘(form of constraint solving algorithm)을 적용해 작성한 코드의 모든 요구 사항을 충족할 수 있는 타입이 존재하는지 여부를 결정
    - 컴파일러가 타입을 추론할 수 있으려면 프로그래머가 제약 조건 방정식을 풀 수 있도록 충분한 정보를 제공해야 한다. ⇒
    - 예시
        - 컴파일 되지 않는 코드
            
            ```Java
            var fn = s -> s.length()
            ```
            
        - 컴파일러가 해결할 수 없는 코드 - 과소결정 연립방정식
            
            - 추론자가 해결해야 하는 타입 제약 조건 방정식
            
            ```Java
            var n = null;
            ```
            
- 타입 추론 단점
    - 복잡성이 높다는 것은 컴파일 시간이 길어지고 추론이 실패할 수 있는 원인이 다양해진다는 것을 의미
    - 즉, 프로그래머가 비지역적 타입의 추론을 올바르게 사용하기 위해 더 복잡한 직관력을 개발해야 한다는 것을 의미
- 로컬 변수 타입 추론은 복잡하고 긴 상용구 텍스트와 장황함을 줄이는 데 유용한 기술이지만, 코드를 더 명확하게 만들기 위해 필요한 경우에만 사용해야 하며, 가능한 모든 경우에 사용하는 무딘 도구(골든 해머 안티패턴)로 사용해서는 안된다
- 로컬 변수 타입 추론 사용 시의 간단한 지침
    - 단순한 초기화에서, 오른쪽이 생성자 또는 정적 팩토리 메서드에 대한 호출인 경우
    - 명시적인 타입을 제거하면 반복되거나 중복된 정보가 삭제되는 경우
    - 변수 이름만으로도 타입을 알 수 있는 경우
    - 로컬 변수의 범위와 사용법이 짧고 간단한 경우
- 표현 불가능한 타입(nondenotable type)
    - Java 소스 코드에서 직접 작성할 수 없는 타입
    - 예시
        - 익명 클래스의 타입
        - 교차 타입(Intersection types)
        - 캡처된 와일드카드 타입
    - `var`와 nondenotable type의 관계
        - `var`를 사용하면 nondenotable type을 가진 변수를 선언할 수 있습니다.
        - 이를 통해 프로그래머는 직접 표현할 수 없는 복잡한 타입을 간단히 사용할 수 있게 됩니다.
    - 예제 코드
        
        ```Java
        var anonymousClassInstance = new Runnable() {
            @Override
            public void run() {
                System.out.println("This is an anonymous class");
            }
        };
        ```
        
        - `anonymousClassInstance`의 실제 타입은 `Runnable`을 구현한 익명 클래스의 타입입니다. 이 타입은 nondenotable type이므로 `var` 없이는 직접 선언하기 어렵습니다.

## 1.4 언어 및 플랫폼 변경

### 1.4.1 설탕 뿌리기

- Syntatic sugar
    - 언어에 이미 존재하는 기능임에도 인간이 작업하기 쉬운 형식으로 제공되는 것
    - 예시
        - Switch 문 사용 시 자바 7부터 String 값을 조건으로 확인할 수 있게 된 것

### 1.4.2 언어 변경

- 모든 변경에 수행해야 하는 작업 세트
    - JLS 업데이트
    - 소스 컴파일러에서 프로토타입 구현
    - 변경에 필수적인 라이브러리 지원 추가
    - 테스트 및 예제 작성
    - 문서 업데이트
- 변경 사항이 JVM이나 플랫폼에 영향을 미치는 경우 수행해야 하는 작업
    - VMSpec 업데이트
    - JVM 변경 사항 구현
    - 클래스 파일 및 JVM 도구에 지원 추가
    - 리플렉션에 미치는 영향 평가
    - 직렬화에 미치는 영향 평가
    - 자바 네이티브 인터페이스와 같은 네이티브 코드 구성 요소에 미치는 영향 평가

### 1.4.3 JSRs와 JEPs

- 자바 플랫폼을 변경하는 데 두 가지 주요 메커니즘
    - Java Community process(JCP)
    - Java Specification request(JSR)
- JEP(JDK Enhancement Proposal)
    - JCP, JSR 대안으로 개발한 빠르고 더 작은 단위로 구현하는 대안

### 1.4.4 인큐베이팅과 프리뷰 기능

- 인큐베이팅 기능
    - 새로운 API와 해당 구현
    - 가장 단순한 형태로 독립적인 모듈로 제공하는 새로운 API
    - 예시 - HTTP/2
    - 이 접근 방식의 주요 장점은 인큐베이팅 기능이 하나의 네임스페이스로 격리될 수 있다는 것
    - 개발자들이 빠르게 기능을 사용해볼 수 있고, 기능이 표준화되면 일부 코드를 수정, 재컴파일, 재링크하면 프로덕션 코드에서도 사용 가능
- 프리뷰 기능
    - 지원이 필요한 부분
        - javac 컴파일러
        - 바이트코드 형식
        - 클래스 파일과 클래스 로딩
        - 실제 프로덕션용으로 사용 불가
            - 최종 완성되지 않은 클래스 파일 형식의 버전으로 표현되고, 자바 프로덕션 버전에서는 지원되지 않을 수도 있음
        - 실험, 개발자 테스트, 체험용에만 적합

### 1.5 자바 11에서의 작은 변경 사항

### 1.5.1 컬렉션 팩토리

- 컬렉션 리터럴(collection literal)
    - list또는 map 과 같은 객체의 단순 컬렉션)을 선언하는 간단하고 편리한 방법
    - 아무리 일반적이라도 구체적인 구현과 직접 결합하는 새로운 구문이 있다면 의도한 설계 원칙과 반대된다는 것
    - 코드 예시
        
        ```Java
        Set<String> set = Set.of("a","b","c");
        var list = List.of("x","y");
        ```
        
    - 일반적인 경우 해당 요소들을 나열하는 메서드가 제공, 요소가 10개보다 더 많은 경우를 위해 가변 인수(varargs) 형태로 제공
        
        ```Java
        List<E> List<E>.<E>of(E e1, E e2, E e3);
        ...
        List<E> List<E>.<E>of(E e1, E e2, E e3,E e4,E e5, .. E e10)
        List<E> List<E>.<E>of(E... elements)
        ```
        
    - Map의 경우
        
        ```Java
        var m1 = Map.of(k1, v1);
        var m2 = Map.of(k1, v1,k2,v2);
        ```
        
    - 팩토리 메서드는 immutable 타입의 인스턴스를 생성

### 1.5.2 엔터프라이즈 모듈 제거

- 제거된 패키지 - Java SE 에서 제거된 패키지
    - 해당 패키지를 사용하기 위해선 외부 라이브러리를 빌드에 포함해야함
    - java.activation
    - java.corba
    - java.transaction
    - java.xml.bind
    - java.xml.ws
    - java.xml.ws.annotation
    - java.se.ee
    - jdk.xml.ws
    - jdk.xml.bind

### 1.5.3 HTTP/2(자바11)

- HTTP 1.1 의 이슈
    - 헤드 오브 라인 블로킹
        - 하나의 TCP 연결에서 요청 & 응답을 순차적으로 처리해, 첫 번째 요청에 대한 응답이 지연되면 그 뒤의 모든 요청들도 대기해야하는 문제 발생 ⇒ 페이지 로딩 시간이 길어지고 전체적인 성능 저하
        - asset을 다운로드 시 브라우저 렌더링 지연 발생
    - 단일 사이트로의 제한된 연결
        - 브라우저는 일반적으로 HTTP 1.1 에서 도메인당 동시 연결 수를 제한(6~8개)
        - 많은 리소스가 필요한 웹사이트의 경우, 이 제한으로 인해 로딩 속도가 감소
        - 이 문제를 해결하기 위해 도메인 샤딩 같은 기법을 사용하기도 함
        - 도메인 샤딩 -
    - HTTP 제어 헤더의 성능 오버헤드
        - HTTP 1.1은 각 요청마다 많은 헤더 정보를 반복해서 전송하는데, 이 헤더들은 이전 요청과 동일한 정보를 담고 있는 경우가 있어 불필요한 데이터 전송을 야기함
        - 특히 쿠기가 큰 경우, 오버헤드가 상당할 수 있음
- HTTP/2
    - 프로토콜의 전송 계층(transport level)에 대한 업데이트
    - 헤드 오브 라인 블로킹
        - 기존 HTTP 1.1
            - 여러 요청이 소켓을 공유하는 경우에도 요청이 순서대로 반환하도록 함(파이프라이닝)
                
                ![[image 1.png]]
                
        - HTTP2는 클라이언트와 서버 간 다중 스트림(multiple stream)이 상시 지원
        - 단일 요청의 헤더와 본문을 별도로 수신
            
            ![[image 2.png]]
            
        - 제한된 연결
            - 각 연결을 효과적으로 사용해 원하는 만큼의 동시 요청을 수행가능
            - 브라우저는 특정 도메인에 대해 하나의 연결만 열지만 동일한 연결을 통해 동시에 많은 요청을 수행 가능
        - HTTP 헤더 성능
            - HTTP 1.1의 페이로드는 클라이언트와 서버가 압축 알고리즘에 대해 압축할 수 있지만, 헤더는 압축되지 않음
            - HTTP2는 헤더의 새로운 이진 형식을 통해 이 문제를 해결
    - TLS의 모든 것
        - HTTP2은 TLS 암호화만 지원
    - 기타 고려 사항
        - HTTP/2는 바이너리 전용인데, 불투명한 형식으로 작업하는 것이 어렵다
        - 로드 밸런서, 방화벽, 디버깅 도구와 같은 HTTP 계층의 제품은 HTTP/2를 지원하도록 업데이트 해야함
        - 성능 향상은 주로 브라우저 기반 HTTP 사용을 대상으로 하고 있으며, HTTP를 통해 작동하는 백엔드 서비스는 업데이트에 대한 이점이 적을 수 있다
    - 자바 11에서 HTTP/2
        
        - HttpURLConnection을 대체(HttpURLConnection를 제거하진 않음)하면서도 사용 가능한 HTTP API를 그대로 제공하는 것을 목표로 함
        - java.net.http에 담음
        - 새로운 API
            - HTTP 1.1 & HTTP/2 를 모두 지원하며, 호출된 서버가 HTTP/2를 지원하지 않는 경우, HTTP1.1로 폴백
            - HttpRequest, HttpClient타입으로 상호작용하고, 빌더를 통해 인스턴스화
            - 예시 코드
                
                ```Java
                var client = HttpClient.newBuilder().build();
                
                var uri = new URI("https://google.com");
                var request = HttpRequest.newBuilder(uri).build();
                
                var response = client.send(
                		// 응답 본문을 처리하는 핸들러 설정
                		request, HttpResponse.BodyHandler.ofString(
                				Charest.defaultCharset()));
                ```
                
                - HttpResponse.BodyHandlers는 응답을 바이트 배열, 문자열, 파일로 받을 수 있는 기본 핸들러 제공
        - HTTP/2의 장점
            - 내장된 멀티플렉싱
            - 예시 코드
                
                ```Java
                var client = HttpClient.newBuilder().build();
                
                var uri = new URI("https://google.com");
                var request = HttpRequest.newBuilder(uri).build();
                var handler = HttpResponse.BodyHandler.ofString();
                CompletableFuture.allOf(
                		client.sendAsync(request, handler)
                				.thenAccept((resp) ->
                											System.out.println(resp.body()),
                		client.sendAsync(request, handler)
                				.thenAccept((resp) ->
                											System.out.println(resp.body()),
                	client.sendAsync(request, handler)
                					.thenAccept((resp) ->
                												System.out.println(resp.body())
                	).join();
                		
                ```
                
                - CompletableFuture.allOf가 세 개의 future를 결합하므로 하나의 join으로 모두 완료될 때까지 기다릴 수 있다
        
        ### 1.5.4 단일 소스 코드 프로그램
        
        - 자바 11에서는 소스 코드는 메모리에서 컴파일된 다음, 디스크에 .class 파일을 생성하지 않고 인터프리터로 실행할 수 있다
            
            ![[image 3.png]]
            
        - 이 기능의 제약사항
            - 단일 소스 파일에 있는 코드로 제한된다
            - 동일한 실행에서 추가적인 소스 컴파일을 할 수 없다
            - 소스 파일에 여러 클래스를 포함할 수 있다
            - 소스 파일에서 첫 번째 클래스를 진입점으로 선언해야 한다
            - 진입점 클래스에서 main 메서드를 정의해야 한다
