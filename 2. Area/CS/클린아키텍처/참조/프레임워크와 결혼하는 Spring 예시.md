# Spring 의존성 주입 예시
## 지양해야 할 방식: 업무 객체에 `@Autowired` 산재
업무 객체에 `@Autowired` 어노테이션이 여러 곳에 산재하면 다음과 같은 문제가 발생합니다:

```java
// 비즈니스 로직 클래스에 @Autowired가 산재한 나쁜 예시
@Service
public class OrderService {
    
    @Autowired
    private CustomerRepository customerRepository;
    
    @Autowired
    private ProductRepository productRepository;
    
    @Autowired
    private PaymentGateway paymentGateway;
    
    @Autowired
    private NotificationService notificationService;
    
    public void processOrder(Order order) {
        // 비즈니스 로직
        Customer customer = customerRepository.findById(order.getCustomerId());
        Product product = productRepository.findById(order.getProductId());
        paymentGateway.processPayment(order.getAmount());
        notificationService.sendOrderConfirmation(customer, order);
    }
}
```
이 방식의 문제점:
- 업무 객체가 스프링 프레임워크에 강하게 결합됨
- 단위 테스트가 어려워짐
- 코드의 재사용성 저하
- 의존성 관계가 명확하지 않음

## 권장 방식: 메인 컴포넌트에서 의존성 주입

메인 컴포넌트(구성 클래스)에서 의존성을 주입하고, 생성자 주입을 활용하는 것이 좋습니다:
```java
// 비즈니스 로직 클래스 - 프레임워크 의존성 없음
public class OrderService {
    
    private final CustomerRepository customerRepository;
    private final ProductRepository productRepository;
    private final PaymentGateway paymentGateway;
    private final NotificationService notificationService;
    
    // 생성자를 통한 의존성 주입
    public OrderService(
            CustomerRepository customerRepository,
            ProductRepository productRepository,
            PaymentGateway paymentGateway,
            NotificationService notificationService) {
        this.customerRepository = customerRepository;
        this.productRepository = productRepository;
        this.paymentGateway = paymentGateway;
        this.notificationService = notificationService;
    }
    
    public void processOrder(Order order) {
        // 비즈니스 로직
        Customer customer = customerRepository.findById(order.getCustomerId());
        Product product = productRepository.findById(order.getProductId());
        paymentGateway.processPayment(order.getAmount());
        notificationService.sendOrderConfirmation(customer, order);
    }
}
```
```java
// 메인 컴포넌트(Configuration 클래스)에서 의존성 설정
@Configuration
public class AppConfig {
    
    @Bean
    public OrderService orderService(
            CustomerRepository customerRepository,
            ProductRepository productRepository,
            PaymentGateway paymentGateway,
            NotificationService notificationService) {
        return new OrderService(
                customerRepository,
                productRepository,
                paymentGateway,
                notificationService);
    }
    
    // 다른 빈 정의들...
}
```
이 방식의 장점:

- 업무 객체가 스프링 프레임워크에 독립적임
- 단위 테스트가 용이함 (모의 객체 주입 가능)
- 의존성 관계가 명시적으로 드러남
- 코드의 재사용성 향상
- 관심사의 분리가 잘 이루어짐

# Spring 의존성 주입 방식의 문제점 분석

메인 컴포넌트에서 의존성을 주입하고 업무 객체에서 `@Autowired` 사용을 제한하는 접근 방식에도 몇 가지 문제점이 있습니다.

## 1. 구성 코드 증가

```java
@Configuration
public class AppConfig {
    @Bean
    public ServiceA serviceA(RepositoryA repoA, RepositoryB repoB) {
        return new ServiceA(repoA, repoB);
    }
    
    @Bean
    public ServiceB serviceB(RepositoryC repoC, ServiceA serviceA) {
        return new ServiceB(repoC, serviceA);
    }
    
    @Bean
    public ServiceC serviceC(RepositoryD repoD, ServiceB serviceB, UtilityService utilService) {
        return new ServiceC(repoD, serviceB, utilService);
    }
    
    // 계속 늘어나는 Bean 정의들...
}
```
**문제점**:

- 애플리케이션이 커질수록 Configuration 클래스가 비대해짐
- 모든 의존성 관계를 수동으로 관리해야 함
- 코드 중복이 발생할 가능성 높음

## 2. 의존성 그래프 관리 복잡성
```java
// 복잡한 의존성 그래프
@Configuration
public class ComplexAppConfig {
    @Bean
    public FinalService finalService(
            ServiceA serviceA, 
            ServiceB serviceB, 
            ServiceC serviceC, 
            ServiceD serviceD, 
            ServiceE serviceE) {
        return new FinalService(serviceA, serviceB, serviceC, serviceD, serviceE);
    }
    
    // 각 서비스마다 여러 의존성이 있는 경우...
}
```

**문제점**:
- 의존성 관계가 복잡해질수록 수동 관리가 어려워짐
- 순환 참조 문제 발견이 늦어질 수 있음
- 의존성 변경 시 여러 곳을 수정해야 함

## 3. 개발 생산성 저하

**문제점**:

- 새로운 의존성 추가 시 항상 Configuration 클래스 수정 필요
- 자동 주입 기능의 편리함을 포기하게 됨
- 작은 변경에도 더 많은 코드 수정이 필요함
## 4. 하이브리드 접근의 어려움
```java
// 일부는 자동 주입, 일부는 수동 구성이 필요한 경우
@Service
public class HybridService {
    private final MandatoryDependency mandatoryDependency;
    
    // 이 생성자는 Configuration에서 호출됨
    public HybridService(MandatoryDependency mandatoryDependency) {
        this.mandatoryDependency = mandatoryDependency;
    }
    
    // 그런데 내부에서 다른 의존성이 필요하다면?
    // @Autowired를 사용하지 않으면 어떻게 주입받을까?
}
```
**문제점**:

- 일부 컴포넌트만 수동 구성하는 혼합 접근 방식은 일관성 부족
- 어떤 의존성이 어디서 주입되는지 추적하기 어려워짐

## 5. 스프링의 기능 활용 제한

**문제점**:

- 스프링의 AOP, 트랜잭션 관리 등 일부 기능 활용이 제한적
- 스프링의 자동 구성 기능의 이점을 충분히 활용하지 못함
- 프로파일, 조건부 빈 등 스프링의 고급 기능 사용이 복잡해짐
## 6. 테스트 복잡성
```java
// 테스트 시 모든 의존성을 수동으로 모의 객체화 해야 함
@Test
public void testOrderService() {
    CustomerRepository mockCustomerRepo = mock(CustomerRepository.class);
    ProductRepository mockProductRepo = mock(ProductRepository.class);
    PaymentGateway mockPaymentGateway = mock(PaymentGateway.class);
    NotificationService mockNotificationService = mock(NotificationService.class);
    
    OrderService orderService = new OrderService(
            mockCustomerRepo, mockProductRepo, 
            mockPaymentGateway, mockNotificationService);
    
    // 테스트 코드...
}
```
**문제점**:

- 통합 테스트 구성이 더 복잡해질 수 있음
- 스프링의 테스트 지원 기능을 활용하기 어려움

## 결론

이상적인 접근법은 상황에 맞게 균형을 찾는 것입니다:
1. **핵심 업무 객체**는 프레임워크 독립적으로 유지
2. **생성자 주입** 방식을 우선적으로 사용 (`@Autowired` 없이도 가능)
3. **적절한 추상화 계층** 도입으로 의존성 관리
4. **모듈화**를 통해 Configuration 클래스 분리
5. **스프링의 컴포넌트 스캔**과 **명시적 구성**을 상황에 맞게 혼합