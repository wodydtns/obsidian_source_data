# 마이크로서비스 아키텍처 주요 개념

### **1. 서비스 디스커버리 (Service Discovery)**
서비스 디스커버리는 네트워크상에서 실행되는 서비스 인스턴스의 위치를 자동으로 탐지하고 관리하는 프로세스입니다. 마이크로서비스 환경에서 서비스들은 동적으로 변할 수 있으며, 이들 서비스의 IP 주소와 포트 번호가 자주 변경될 수 있습니다. 서비스 디스커버리 도구는 이러한 서비스들의 위치 정보를 중앙에서 관리하고, 필요할 때 각 서비스를 찾을 수 있도록 도와줍니다. 이를 통해 서비스 간의 통신이 원활하게 이루어질 수 있습니다.
### **2. 구성 관리 (Configuration Management)**
구성 관리는 애플리케이션의 설정을 중앙에서 관리하고, 애플리케이션의 실행 환경에 따라 이 설정을 동적으로 조정하는 프로세스입니다. 마이크로서비스 아키텍처에서 각 서비스는 독립적으로 배포되고 관리될 수 있어야 하므로, 각 서비스의 설정 정보도 중앙에서 쉽게 변경하고 배포할 수 있어야 합니다. 구성 관리 도구는 이러한 설정 정보를 저장, 관리하고 필요한 서비스에 적시에 제공하여 서비스가 현재 환경에 적합하게 동작하도록 지원합니다.
### **3. API 게이트웨이 (API Gateway)**
API 게이트웨이는 마이크로서비스 아키텍처에서 클라이언트의 요청을 적절한 서비스로 라우팅하는 역할을 합니다. 이는 사용자 인증, 요청 속도 제한, 로깅, SSL 종료, 프로토콜 번역 등의 크로스-커팅 관심사(cross-cutting concerns)를 처리함으로써 각 마이크로서비스가 비즈니스 로직에 집중할 수 있게 도와줍니다. 또한, API 게이트웨이는 모든 마이크로서비스 앞에 위치하여 외부 요청을 적절히 분산시키고, 서비스 간의 복잡한 통신 패턴을 추상화합니다.

### **4. 메시지 큐 (Message Queue)**

메시지 큐는 서비스 간에 메시지를 비동기적으로 교환할 수 있는 시스템을 말합니다. 이는 서비스들이 직접적으로 통신하는 것을 방지하며, 대신 메시지 큐를 통해 메시지를 보내고 받습니다. 이를 통해 시스템의 결합도를 낮추고, 부하가 높은 작업을 비동기적으로 처리할 수 있게 해줍니다. 또한, 메시지 큐를 사용함으로써 서비스 장애가 다른 서비스에게 전파되는 것을 방지할 수 있습니다.

### **5. 로드 밸런싱 (Load Balancing)**

로드 밸런싱은 네트워크 트래픽이나 요청을 여러 서버나 서비스 인스턴스에 균등하게 분산시키는 기술입니다. 이는 시스템의 가용성과 확장성을 향상시키는 데 중요한 역할을 합니다. 로드 밸런서는 들어오는 요청을 여러 서비스 인스턴스에 동적으로 할당하여, 모든 서비스가 고르게 부하를 받도록 하며, 과부하 발생 시 추가 리소스를 자동으로 할당하거나 회수할 수 있습니다.

### **6. 서킷 브레이커 (Circuit Breaker)**

서킷 브레이커 패턴은 연속적인 실패가 발생하는 외부 서비스 호출을 감지하고, 장애가 해결될 때까지 해당 서비스 호출을 일시적으로 차단하는 기능을 제공합니다. 이 패턴은 시스템 전체의 실패를 방지하고, 실패가 빈번한 서비스에 계속적으로 요청을 보내는 것을 방지함으로써 시스템의 안정성을 유지하는 데 도움을 줍니다.

## 언어별 라이브러리
# Java
### 1. 서비스 디스커버리

- **Eureka**: Netflix에서 개발한 서비스 디스커버리 툴로, 서비스 간의 위치를 자동으로 찾고 등록하는 데 사용됩니다

### 2. 구성 관리

- **Spring Cloud Config**: 외부 구성 관리를 중앙에서 제공하는 도구로, 애플리케이션 설정을 중앙에서 관리하고 갱신할 수 있습니다.

### 3. API 게이트웨이

- **Spring Cloud Gateway**: API 라우팅, 보안, 모니터링 등을 포함한 풍부한 기능을 제공하는 강력한 API 게이트웨이입니다.

### 4. 메시지 큐

- Spring Cloud Stream : 다양한 메시지 브로커를 쉽게 연동할 수 있게 해주는 프레임워크입니다. Kafka, RabbitMQ 등과의 통합을 지원하여 메시지 기반 통신을 간소화합니다

### 5. 로드 밸런싱

- Netflix Ribbon : 클라이언트 측 로드 밸런싱을 제공하는 라이브러리로, 서비스 간 호출 시 부하를 분산시키는 기능을 제공합니다

### 6. 서킷 브레이커

- **Hystrix**: 서비스 호출 시 서킷 브레이커 패턴을 구현하여, 오류가 발생할 경우 자동으로 요청을 중단하고 폴백 메커니즘을 활성화합니다.

## Python

### 1. 서비스 디스커버리

- **Nameko**: 서비스 디스커버리를 비롯한 여러 마이크로서비스 지원 기능을 내장한 프레임워크입니다.

### 2. 구성 관리

- **Dynaconf**: 다양한 소스(환경 변수, INI, JSON, YAML 파일 등)에서 설정을 로드할 수 있는 구성 라이브러리입니다.

### 3. API 게이트웨이

- **FastAPI**: 자체적으로 API 게이트웨이 기능을 포함하여 높은 성능과 쉬운 사용법을 제공합니다. 또한 Starlette과 같은 ASGI 프레임워크와도 잘 연동됩니다.

### 4. 메시지 큐

- **Celery**: Python에서 널리 사용되는 비동기 작업 큐/메시지 큐 라이브러리로, RabbitMQ, Redis 등의 메시지 브로커와 통합하여 마이크로서비스 간 비동기 처리를 용이하게 합니다.

### 5. 로드 밸런싱

- **Locust**: 오픈소스 부하 테스팅 도구로, Python으로 작성되어 스크립트를 통해 사용자 정의 부하 테스트와 로드 밸런싱 시뮬레이션을 수행할 수 있습니다.

### 6. 서킷 브레이커

- **PyBreaker**: Python 기반의 서킷 브레이커 라이브러리로, 시스템 부하를 줄이고 장애 발생 시 빠르게 회복할 수 있게 도와줍니다.

## Nodejs

### 1. 서비스 디스커버리

- **Consul**: HashiCorp에서 개발한 도구로 Node.js에서도 쉽게 사용할 수 있으며, 서비스 디스커버리와 건강 검사 기능을 제공합니다.

### 2. 구성 관리

- **node-config**: 환경에 따라 설정을 관리할 수 있는 Node.js 라이브러리로, 애플리케이션 구성을 쉽게 관리할 수 있습니다.

### 3. API 게이트웨이

- **Express Gateway**: Express.js 기반의 경량 API 게이트웨이로, API 키 관리, 속도 제한, OAuth2 등을 지원합니다.

### 4. 메시지 큐

- **RabbitMQ Node.js Client**: RabbitMQ 공식 Node.js 클라이언트를 사용하여 메시지 큐 기능을 구현할 수 있으며, 높은 효율과 안정성을 제공합니다.

### 5. 로드 밸런싱

- **PM2**: Node.js 애플리케이션을 위한 프로세스 관리자로, 로드 밸런서 기능을 포함하고 있어 여러 인스턴스를 자동으로 스케일링하고 관리할 수 있습니다.

### 6. 서킷 브레이커

- **Node.js**
    - **Opossum**: Node.js를 위한 서킷 브레이커 라이브러리로, API 호출이나 데이터베이스 요청과 같은 외부 리소스 접근 시 실패에 대비할 수 있습니다.