>[!important]

>소프트웨어 개체는 확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다.

  

## 사고 실험
![[Pasted image 20250112200901.png]]
- Controller, Interactor, Database, Presenter,View 컴포넌트로 분리
- Interactor 입장에서는 Controller는 부수적이지만, Controller는 Presenter & View 에 비해 중시먹인 문제를 담당
	- **보호 계층구조가 Level(수준)이라는 개념을 바탕으로 생성**
- 모든 의존성이 소스 코드 의존성을 나타낸다
- 모든 컴포넌트 고나계는 단방향으로 이루어진다
![[Pasted image 20250112201024.png]]

## 방향성 제어
- 
  

## 정보 은닉
- Controller에서 발생한 변경으로부터 Interactor를 보호하는 일의 우선순위가 가장 높지만, Interactor에서 발생한 변경으로부터 Controller도 보호되기를 바란다. 이를 위해 Interactor 내부를 은닉