- 코딩 스타일 가이드
    - 코드의 지속 가능성을 높이기 위한 방법
- 규칙이 필요한 이유
    - 확립한 규칙과 지침은 조직이 커지더라도 일관적 통용되는 공통의 코딩 어휘가 되어줌
    - 규칙은 일상의 개발 패턴을 조직이 원하는 방향으로 넛지해주는 역할로 활용
- 규칙 만들기
    - 기본 원칙 안내
        - 개발 환경의 복잡도를 관리하고 엔지니어들의 생산성을 희생하지 않는 선에서 코드베이스를 관리 가능하게 끔 유지하는 것
        - 유연성 희생 < 일관성 & 의견 대립 감소
        - 중요한 원칙
            - 규칙의 양을 최소화
                - 너무 자명한 규칙은 의도적으로 배제
            - 읽는 사람에게 맞추기
                - 코드 작성자보다 읽는 사람에게 최적화하기
                - 현 위치에서 추론하기 → 다른 코드를 찾아보거나 참조할 필요 없이, 함수의 구현부를 들여다보지 않고 호출 지점에서 무슨 일이 벌어지는지를 명확히 이해할 수 있도록 하는 것
                - 문서화 주석 → 해당 파일, 클래스, 함수의 앞에 추가되는 블록 주석
                - 구현 주석 → 주의점, 까다로운 부분의 주석 로직 설명, 중요 부분 강조
            - 일관되어야한다
                - 코드 일관성을 통해 엔지니어들이 상당히 빠르게 작업
                - 프로젝트에 사용하는 도구, 기법, 라이브러리는 동일
            - 일관성이 안겨주는 이점
                - 코드베이스의 스타일, 기준이 일관되면 작성하는 엔지니어와 읽는 이들을 **어떻게** 표현햐느냐가 아닌 **무엇을** 수행하는가에 집중
                - 코드 분석,코드 모듈화,코드 중복 확인, 코드 확장 **용이**함
                - 시간 관점에서 탄력성 보장 → 코드 & 엔지니어가 거의 제약없이 재배치 가능 → 유지보수 프로세스 간소화
            - 표준 정하기
                - 바깥세상과 일관되게 표준 정하기
            - 오류를 내기 쉽거나 예상과 다르게 동작할 여지가 있는 구조는 피하자
                - 이해하기 쉽고 유지보수하기 쉬운 명료하고 직관적인 코드
            - 실용적 측면을 인정하자
                - 꼭 필요하다면 최적화나 실용성을 위해 예외를 허용
                    - 일관성과 가독성을 희생하더라도 성능을 끌어올려야할 경우
    - 스타일 가이드
        - 위험 회피하기
            
            - 정적 멤버와 변수, 람다식, 예외 처리, 스레드와 접근 제어, 클래스 상속 등의 사용법을 설명하는 규칙
            - 어떤 언어 특성은 사용하고 어떤 구조는 피해야 하는지를 설명하는 규칙
        - 모범 사례 강제하기
            
            - 주석을 어디에 어떻게 작성해야하는가
                
            - 콘텐츠가 읽는 사람이 기대하는 순서로 배치되도록 하기 위해 소스 파일의 구조도 규칙으로 상세하게 정의
                
            - 패키지, 클래스, 함수, 변수 등의 이름을 짓는 규칙도 있음
                
            - 수직, 수평 공백도 명시
                
            - 자동 포맷팅 도구도 사용
                
            - 구글 스타일 가이드
                
                [Google Style Guides](https://google.github.io/styleguide/)
                
        - 일관성 구축하기
            
            - 명명 규칙, 들여쓰기 공백 수, 임포트문 순서 등 일관성 구축
- 규칙 수정하기
    - 프로세스
        - 해법을 중심으로 하는 관점에서 작성
    - 스타일 중재자
        - 프로그래밍 언어별로 경험이 많은 전문가 그룹이 스타일 가이드를 소유하고 결정함
        - 개인 취향은 무시하고 트레이드오프 평가에 기초해 판단
    - 예외
        - 특정 상황에선 일부 규칙을 적용하지 않아도 되게끔 예외를 허용
        - 규칙 < 예외의 이득이 크다고 판단될 경우에만 예외 허용
- 지침
    - 사람들이 자주 하는 실수 혹은 아직 익숙하지 않은 새로운 주제라서 혼란스러워 하는 것에 집중
    - 예시
        - 올바른 구현하기 어려운 주제에 관한 언어별 조언(ex. 동시성 & 해싱)
        - 언어의 최신 버전에서 소개된 새로운 기능의 상세 설명과 코드베이스에 적용하는 방법
- 규칙 적용하기
    - 규칙 숙달을 위한 교육 → 코드 리뷰 문화
    - 실제 적용 확인은 사람보다 되도록 자동화 도구 활용
    - 도구를 활용하면 규칙을 미묘하게 다르게 해석하거나 적용하는 일을 최소한으로
    - 사회적 문제를 기술적 시각으로 해결하는 것은 좋지 않음
    - 오류 검사기
        - 코드 분석기를 개발 워크플로와 긴밀하게 통합
    - 코드 포맷터
        - 코드 일관성을 위해 자동 스타일 검사기와 포맷터를 적극 활용
        - presubmit check로 포맷터를 반드시 사용
        - ex) 파이썬 → YAPF ([https://github.com/google/yapf](https://github.com/google/yapf))