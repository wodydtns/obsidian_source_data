  
> [!important]
>  SRP 정의
>  단일 모듈은 변경의 이유가 하나, 오직 하나뿐이어야 한다
>  하나의 모듈은 하나의, 오직 하나의 사용자 또는 이해관계자에 대해서만 책임져야 한다
>  하나의 모듈은 하나의, 오직 하나의 액터에 대해서만 책임져야 한다

- SRP 는 단일 액터를 책임지는 코드를 함께 묶어주는 힘 - 응집성을 말한다
  
## 징후 1: 우발적 중복
- 예시 1
- Employee Class의 메서드들
	- calculatePay()
		- 회계팀에서 기능 정의하며, CFO 보고를 위해 사용
	- reportHours()
		- 인사팀에서 기능을 정의하고 사용하며, COO 보고를 위해 사용
	- save()
		- DBA가 기능을 정의하고 CTO 보고를 위해 사용
	- 여기서 calculatePay와 reportHours() 가 업무 시간을 계산하는 알고리즘을 공유하며, 개발자가 코드 중복을 피하기 위해 이 알고리즘을 regularHours() 메서드에 적용한 경우
	- CFO 에서 해당 로직을 결정하면, COO의 팀에서 수치는 엉망이 된다
- **SRP는 서로 다른 액터가 의존하는 코드를 서로 분리해야한다고 말한다**
## 징후 2 : 병합

- 위의 경우에서 Employee 테이블의 스키마 수정 & 인사 담당자가 속한 COO 팀에서의 reportHours() 메서드의 보고서 포맷 변경 시
- 병합 시 문제 발생
## 해결책

- 데이터와 메서드를 분리
- 우연한 중복을 회피할 수 있음
- 하지만 세 가지 클래스를 인스턴스화하고 추적해야하는 문제가 있음
- 퍼사드 패턴

## 퍼사드 패턴이란?
복잡한 서브시스템들의 집합에 대해 간단하고 통합된 인터페이스를 제공하는 구조적 디자인 패턴입니다.

## 특징
1. **단순화된 인터페이스**: 복잡한 시스템을 감추고 간단한 인터페이스 제공
2. **결합도 감소**: 클라이언트와 서브시스템 간의 결합도를 낮춤
3. **캡슐화**: 서브시스템의 복잡성을 캡슐화

## 구조
```java
// 서브시스템 클래스들
class CPU {
    public void freeze() { /* ... */ }
    public void jump(long position) { /* ... */ }
    public void execute() { /* ... */ }
}

class Memory {
    public void load(long position, byte[] data) { /* ... */ }
}

class HardDrive {
    public byte[] read(long lba, int size) { /* ... */ }
}

// 퍼사드 클래스
class ComputerFacade {
    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;
    
    public ComputerFacade() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }
    
    public void start() {
        cpu.freeze();
        memory.load(BOOT_ADDRESS, hardDrive.read(BOOT_SECTOR, SECTOR_SIZE));
        cpu.jump(BOOT_ADDRESS);
        cpu.execute();
    }
}

// 클라이언트 코드
public class Client {
    public static void main(String[] args) {
        ComputerFacade computer = new ComputerFacade();
        computer.start();
    }
}
```

## 실제 사용 예제

### 1. 온라인 쇼핑 시스템
```java
// 서브시스템들
class InventorySystem {
    public boolean checkStock(String productId) { return true; }
}

class PaymentSystem {
    public boolean processPayment(String orderId, double amount) { return true; }
}

class ShippingSystem {
    public void scheduleDelivery(String orderId, String address) { }
}

class NotificationSystem {
    public void sendConfirmation(String email) { }
}

// 퍼사드
public class OrderFacade {
    private InventorySystem inventory;
    private PaymentSystem payment;
    private ShippingSystem shipping;
    private NotificationSystem notification;
    
    public OrderFacade() {
        this.inventory = new InventorySystem();
        this.payment = new PaymentSystem();
        this.shipping = new ShippingSystem();
        this.notification = new NotificationSystem();
    }
    
    public boolean placeOrder(Order order) {
        if (!inventory.checkStock(order.getProductId())) {
            return false;
        }
        
        if (!payment.processPayment(order.getId(), order.getAmount())) {
            return false;
        }
        
        shipping.scheduleDelivery(order.getId(), order.getAddress());
        notification.sendConfirmation(order.getEmail());
        
        return true;
    }
}
```

### 2. 멀티미디어 플레이어
```java
// 서브시스템들
class AudioSystem {
    public void initialize() { }
    public void play(String audio) { }
}

class VideoSystem {
    public void initialize() { }
    public void play(String video) { }
}

class SubtitleSystem {
    public void load(String language) { }
    public void display() { }
}

// 퍼사드
public class MediaPlayerFacade {
    private AudioSystem audio;
    private VideoSystem video;
    private SubtitleSystem subtitle;
    
    public MediaPlayerFacade() {
        this.audio = new AudioSystem();
        this.video = new VideoSystem();
        this.subtitle = new SubtitleSystem();
    }
    
    public void playMovie(String movie, String language) {
        audio.initialize();
        video.initialize();
        subtitle.load(language);
        
        video.play(movie);
        audio.play(movie);
        subtitle.display();
    }
}
```

## 장점
1. **단순화**
   - 복잡한 시스템을 단순한 인터페이스로 제공
   - 클라이언트 코드 간소화

2. **유지보수성**
   - 서브시스템 변경이 클라이언트에 영향을 미치지 않음
   - 시스템 구성 요소를 쉽게 변경 가능

3. **느슨한 결합**
   - 클라이언트와 서브시스템 간의 의존성 감소
   - 시스템 확장성 향상

## 단점
1. **추가 계층**
   - 새로운 추상화 계층이 추가됨
   - 약간의 성능 오버헤드 발생 가능

2. **퍼사드 클래스 비대화**
   - 여러 서브시스템을 통합하면서 퍼사드 클래스가 복잡해질 수 있음

## 사용해야 할 때
1. 복잡한 서브시스템에 대한 단순한 인터페이스가 필요할 때
2. 서브시스템을 계층화하고 싶을 때
3. 시스템의 결합도를 낮추고 싶을 때

## 주의사항
1. 퍼사드는 서브시스템의 모든 기능을 노출할 필요는 없음
2. 필요한 기능만 선별적으로 제공
3. 퍼사드 클래스가 너무 복잡해지지 않도록 주의

퍼사드 패턴은 복잡한 시스템을 다룰 때 매우 유용한 디자인 패턴이며, 특히 레거시 시스템을 현대화하거나 복잡한 서브시스템들을 통합할 때 자주 사용됩니다.