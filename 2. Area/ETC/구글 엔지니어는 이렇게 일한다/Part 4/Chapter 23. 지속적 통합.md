- 지속적 통합(Continuous Integration) : 팀원들이 작업 결과를 자주 통합하는 소프트웨어 개발 방식 , 통합할 때마다 자동 빌드(테스트 포함)해 통합 오류를 빠르게 발견함
- 대규모 개발을 고려한 CI의 재정의
    - CI : 빠르게 진화하는 복잡한 생태계 전체를 지속적으로 조립하고 테스트하는 개발 방식
- 테스트 시각에서 CI가 주는 패러다임
    - 코드(와 다른 요소)가 변경되어 지속적으로 통합되는 개발/릴리스 워크플로에서 무슨 테스트를 언제 실행해야 하는가?
    - 워크플로의 각 테스트 지점에서(적절한 충실성을 갖춘) 테스트 대상 시스템을 (합리적인 비용으로) 어떻게 구성해야 하는가?
    - 많은 선행 연구와 경험 덕에 CI가 엔지니어링 조직과 비지니스에 크게 이롭다는 것은 의심할 여지가 없음
- 지속적 통합이란?
    - 빠른 피드백 루프
        - 편집|컴파일|디버그 → 프리서브밋→ 포스트서브밋→ 릴리스 후보→ 릴리스 후보 승격(스테이징 등 임시 환경) → 최종 릴리스 후보 승격(프로덕션)
        - 루프의 으론쪽 끝 가까이까지 살아남을수록 비용이 커지는 이유
            - 문제의 코드에 익숙하지 않은 엔지니어가 분류
            - 변경 작성자가 무엇을 왜 변경했는지 기억해내고 조사하는 노력이 더 큼
            - 바로 옆 동료부터 최종 사용자까지, 다른 이들에게 부정적인 영향을 줌
        - CI는 빠른 피드백 루프를 이용하도록 유도해 버그 비용을 최소화함
        - 카나리 배포 → 프로덕션에서 일어나는 문제 감소
        - 버전 왜곡 → 분산 시스템에 호환되지 않는 여러 코드, 데이터, 설정 정보가 공존하는 상태
        - 실험 & 기능 Flag도 매우 강력한 피드백 루프
        - 볼 수 있고 조치할 수 있는 피드백
            - CI 테스트가 제공하는 피드백을 조치 가능해야함
    - 자동화
        - CI는 특별히 **빌드 & 릴리스 프로세스 자동화**
        - 지속적 빌드(Continuous Build)
            - 가장 최근의 코드 변경을 헤드에 통합한 다음 자동으로 빌드와 테스트를 수행 → CB에서는 테스트도 빌드의 한 과정으로 보기 때문에 컴파일을 통과하더라도 테스트에 실패 == 빌드 실패
            - True Head : 최신 변경이 커밋된 버전
            - Green Head : CB가 검증한 최신 변경
            - 일반적으로 CB가 검증한 안정된 환경에서 작업하기 위해 녹색 헤드와 동기화
        - 지속적 배포(Continuous Delivery)
            - 1단계 : 릴리스 자동화
                - 릴리스 후보(Release candidate) : 자동화된 프로세스가 만든, 서로 밀접하게 관련된 요소들로 구성된 배포 가능한 단위. 지속적 빌드로 통과한 코드, 설정 정보, 기타 의존성들을 조합해 만든다
                - 릴리스 후보에는 설정 정보까지 포함해야함
                - 정적인 설정 정보들은 모두 릴리스 후보에 묶어 승격시켜서 해당 코드와 함께 테스트되도록 해야함
            - 지속적 배포 : 지속해서 릴리스 후보를 조립한 다음 다양한 환경에 차례로 승격시켜 테스트하는 활동, 프로덕션까지 승격시키는 경우도 있고 그렇지 않은 경우도 있음
            - RC가 운영 환경들에서 단계별로 승격될 때 이상적으로 아티팩트들을 다시 컴파일하거나 빌드하지 않아야함 → 로컬에서 개발할 때부터 도커 같은 컨테이너를 이용해 환경을 옮겨도 RC의 일관성 유지가 쉬워짐
    - 지속적 테스트(Continuous testing)
        - 프리서브밋만으로는 부족한 이유
            - 프리서브밋 때 모든 테스트를 다 돌려보지 않는다 → 비용이 너무 크기 때문 ⇒ 프리서브밋 때 수행할 테스트 선별이 필요함
            - 프리서브밋 테스트가 수행되는 동안에도 리포지터리는 계속 수정이 될 수 있어 장시간 열심히 테스트한 변경과 이미 호환되지 않게 달라져 있을 가능성도 있음
        - 프리서브밋 VS 포스트서브밋
            - 프리서브밋 때는 커버리지를 다소 포기하고, 대신 놓친 문제들을 포스트서브밋 때 잡아내는 전략
            - 릴리스 후보 테스트
                - 지속적 빌드에서 포스트서브밋 때 똑같은 테스트를 수행했더라도 RC는 매 단계에서 철저하게 다시 테스트
                - 이유
                    - 온전성 검사 : 코드를 잘라와 RC용으로 다시 컴파일할 때 예상치 못한 일이 발생하지 않았는지를 재차 확인
                    - 검사 용이성 : RC의 테스트 결과를 확인하려는 엔지니어가 지속적 빌드 로그를 파헤쳐보지 않고도 해당 RC와 관련된 결과를 바로 살펴볼 수 있음
                    - 체리픽 허용 : RC에 Cherry Pick 수정을 반영핟ㄴ다면 지속적 빌드가 테스트한 코드와 달라짐
                    - 비상 배포 : 비상 상황 발생 시, 지속적 배포는 True Head를 잘라와서 배포해도 괜찮겠다는 마음의 안정을 가져다주는 데 꼭 필요한 최소한의 테스트만 실행
            - 프로덕션 테스트
                - RC 때와 동일한 테스트 스위트를 프로덕션 환경에서도 그대로 수행
                - 그의 목적
                    - 프로덕션이 테스트대로 올바르게 동작하는가?
                    - 프로덕션을 검증하기에 적합한 테스트인가?
            - CI의 과제
                - 프리서브밋 최적화 : 앞서 언급한 잠재적인 문제를 고려해 프리서브밋 시 어떤 테스트를 어떻게 수행해야 할까요?
                - 범인 찾기와 실패 격리 : 문제를 일으킨 코드(또는 기타 변경)가 어느 것이고, 어느 시스템에서 문제가 발생했을까요?
                - 자원 제약 : 테스트를 실행하려면 자원이 있어야 하고, 거대한 테스트는 엄청난 자원을 소비함 | 프로세스 단계마다 자동화 테스트를 수행하는 데 필요한 인프라 비용도 상당할 수 있음
                - 실패 관리 : 테스트 실패 시 어떻게 대응해야 하는가
                - 불규칙한 테스트 : 진행을 유지해도 되는지, 동시에 매번 실패하는 게 아니라서 롤백해야 할 변경이 어느 것인지 찾기 어려움
                - 테스트 불안정성 → 테스트를 여러 번 시도하는 방식으로 대처
            - 밀폐 리스트
                - 정의 : 모든 것을 갖춘 테스트 환경에서 수행하는 테스트
                - 특징
                    - 우수한 결정성 → 테스트에 영향을 주는 데이터가 적어도 외부 의존성 때문에 바뀔 일이 없으므로 밀폐 테스트가 실패한다면 최근 수정한 애플리케이션 코드나 테스트가 원인일 가능성이 높음 ⇒ 실패 원인의 범위를 더 쉽게 좁힐 수 있음
                    - 격리 → 프로덕션의 문제가 밀폐 테스트에 영향을 주지 않음