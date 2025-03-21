## 4.1 성능 테스트 유형
### 4.1.1 지연 테스트
- 가장 일반적인 성능 테스트
- **종단 트랜잭션에 걸리는 시간은?**

### 4.1.2 처리율 테스트
- **현재 시스템이 처리 가능한 동시 트랜잭션 개수는?**
- 지연 분포가 갑자기 변하는 시점 ⇒ **“최대 처리율” ⇒ 최대 처리율 수치를 측정하는 것**

### 4.1.3 부하 테스트
- **특정 부하를 시스템이 감당할 수 있는가?**
- 애플리케이션 트래픽이 상당할 것으로 예상되는 특정 비지니스 이벤트에 대비하기 위해 부하 테스트 수행

### 4.1.4 스트레스 테스트
- **이 시스템의 한계점은 어디까지 인가?**
- 일정한 수준의 트랜잭션, 즉 특정 처리율을 시스템에 걸고 측정값이 나빠지기 시작하기 직전의 값을 측정

### 4.1.5 내구성 테스트
- **시스템을 장시간 실행할 경우 성능 이상 증상이 나타나는가?**
- memory leak, cache polluation, memory fragmentation(메모리 단편화) 등 시간이 지난 후 나타나는 문제점 파악
- 평균 또는 그보다 높은 사용률로 시스템에 일정 부하를 계속 주며 모니터링하다가 갑자기 리소스가 고갈되거나 시스템이 깨지는 지점을 찾음

### 4.1.6 용량 계획 테스트(capacity planning test)
- **리소스를 추가할 만큼 시스템이 확장되는가?**
- 업그레이드한 시스템이 어느 정도 부하를 감당할 수 있을지 미리 확인하느 ㄴ것

### 4.1.7 저하 테스트(Degradation)
- **시스템이 부분적으로 실패할 경우 어떤 일이 벌어지나?**
- 부분 실패 테스트라고도 함
- 페일오버 및 복원 테스트
- 트랜잭션 지연 분포와 처리율을 확인

## 4.2 기본 베스트 프랙티스
- 성능 튜닝 시 주안점
    1. 나의 관심사가 무엇인지 식별하고 그 측정 방법을 고민한다
    2. 최적화하기 용이한 부분이 아니라, 중요한 부분을 최적화한다
    3. 중요한 관심사를 먼저 다룬다

### 4.2.1 하향식 성능
- 하향식 성능(top-down performance) : 전체 애플리케이션의 성능 양상부터 알아보는 접근 방식
- 하향식 성능 접근 방식으로 성과를 극대화하려면, 먼저 테스트팀이 테스트 환경을 구축한 다음 무엇을 측정하고 최적화 할지, 또 성능 활동을 전체 소프트웨어 개발 주기에 어떻게 병행해야하는지 모두 이해하고 있어야함

### 4.2.2 테스트 환경 구축
- 테스트 환경은 가급적 운영 환경과 동일해야함
- 애플리케이션 서버, cpu 수, OS, 자바 런타임 버전, 웹 서버, DB, 로드 밸런서, 네트워크 방화벽도 동일한 것이 좋음

### 4.2.3 성능 요건 식별
- 성능비기능 요건(NonFunctional Requirement) - 성능 평가 지표
    - 예시
        - 95% 백분위 트랜잭션 시간을 100밀리초 줄인다
        - 기존 하드웨어 처리율을 5배 높일 수 있게 시스템을 개선한다
        - 평균 응답 시간을 30% 줄인다
        - 일반 고객을 서비스하는 리소스 비용을 50% 줄인다
        - 애플리케이션 클러스터 성능이 50% 떨어져도 시스템이 응답 목표를 25% 이내로 유지한다
        - 고객 이탈율을 25밀리초 지연당 25% 낮춘다

### 4.2.4 자바에 특정한 이슈
- JIT 컴파일을 수행하지 않는 메서드
    - **JIT 컴파일할 정도로 자주 실행되는 메서드가 아니다**
    - **메서드가 너무 크고 복잡해서 도저히 컴파일 분석할 수 없다**
- JVM 기반 애플리케이션에서 성능 활동을 시작하는 첫 단추는, **“어떤 메서드가 컴파일 중인지 로그를 남겨 살피고 핵심 코드 경로상의 중요 메서드가 잘 컴파일되고 있는지 확인하는 것”**

### 4.2.5 SDLC 일부로 성능 테스트 수행하기
- 수준 높은 팀일수록 성능 테스트를 전체 SDLC(Software Development LifeCycle<소프트웨어 개발 수명주기>)의 일부로 수행하며, 특히 성능 회귀 테스트(performance regression testing)를 상시 수행

## 4.3 성능 안티패턴 개요
- 안티패턴 : 소프트웨어 프로젝트 또는 팀의 안좋은 패턴
- 성능 튜닝은 항상 초기 기획 단계부터 구체적으로 목표를 정해놓고 시작하는 목표 지향형 프로세스로 접근해야함

### 4.3.1 지루함
- 개발자의 지루함은 프로젝트에 여러 가지로 해악을 끼칠 수 있다
- 지금까지 알려지지 않은 기술로 컴포넌트를 제작하거나, 맞지도 않은 유스케이스에 억지로 기술을 욱여넣는 등 여러 가지 방법으로 지루함을 표출하기도 함

### 4.3.2 이력서 부풀리기