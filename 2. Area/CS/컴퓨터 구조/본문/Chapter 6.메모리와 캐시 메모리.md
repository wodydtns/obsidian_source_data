1. RAM의 특징과 종류
    - RAM의 특징
        - 휘발성 저장 장치 : 전원이 꺼지면 저장된 내용이 사라지는 저장 장치
        - 비휘발성 저장 장치 : 전원이 꺼져도 저장된 내용이 유지되는 저장 장치
        - RAM은 **실행할 대상**을 저장
    - RAM의 용량과 성능
        - RAM용량이 크면 많은 프로그램들을 동시에 빠르게 실행하는 데 유리
    - RAM의 종류
        - DRAM
            - Dynamic RAM
            - 저장된 데이터가 동적으로 변하는 RAM
            - 시간이 지나면 저장된 데이터가 점차 사라지는 RAM ⇒ DRAM은 데이터의 소멸을 막기 위해 일정 주기로 데이터를 재활성화(다시 저장)해야함
            - 소비 전력이 비교적 낮고, 저렴하고, 집적도가 높음 ⇒ 대용량 설계 용이
        - SRAM
            - Static RAM
            - 시간이 지나도 저장된 데이터가 사라지지 않음
            - 주기적인 데이터 재활성화가 필요 없음
            - SRAM이 DRAM보다 일반적으로 속도도 더 빠름
            - 집적도가 낮고, 소비 전력도 크고, 가격이 비싸 대용량으로 만들어질 필요는 없지만 속도가 빨라야하는 저장 장치에 사용 ex) 캐시 메모리
        - SDRAM
            - Synchronous Dynamic RAM
            - 클럭 신호와 동기화된, 발전된 형태의 DRAM
            - SDRAM은 클럭에 맞춰 동작하며 클럭마다 CPU와 정보를 주고받을 수 있는 DRAM
        - DDR SDRAM
            - Double Data Rate SDRAM
            - 대역폭을 넓혀 속도를 빠르게 만든 SDRAM
            - 대역폭(data rate) : 데이터를 주고받는 길의 너비
2. 메모리의 주소 공간
    - 물리 주소 : 메모리 하드웨어가 사용하는 주소
    - 논리 주소 : CPU와 실행 중인 프로그램이 사용하는 주소
    - 물리 주소와 논리 주소
        - 메모리에 새롭게 실행되는 프로그램이 시시때때로 적재되고, 실행이 끝난 프로그램은 삭제되며, 같은 프로그램을 실행하더라도 실행하는 적재되는 주소가 달라지므로 ⇒ CPU와 실행 중인 프로그램의 메모리 주소는 알 수 없음
3. 캐시 메모리
    - 저장 장치 계층 구조
        - CPU에 얼마나 가까운가를 기준으로 저장 장치의 계층을 나눈 것
            
            ![[Untitled 97.png|Untitled 97.png]]
            
    - 저장 장치 세부 구조
        
        ![[Untitled 1 3.png|Untitled 1 3.png]]
        
    - 캐시 메모리
        - CPU와 메모리 사이에 위치
        - CPU의 연산 속도와 메모리 접근 속도의 차이를 줄이기 위해 탄생
        - CPU가 매번 메모리에 접근하는 것은 시간이 오래 걸리니, 메모리에서 CPU가 사용할 일부 데이터를 미리 가져오는 것
        - 캐시 메모리 종류
            - L1(level 1) 캐시
            - L2(level 2) 캐시
            - L3(level 3) 캐시
            - 캐시 용량 : L1 < L2 < L3
            - 가격 : L1 > L2 > L3
        - 캐시 구조
            
            ![[Untitled 2 3.png|Untitled 2 3.png]]
            
    - 참조 지역성 원리
        - 캐시 히트(cache hit) : 자주 사용될 것으로 예측되는 데이터가 실제로 맞아 캐시 메모리 내 데이터가 CPU에서 활용될 경우
        - 캐시 미스(cache miss) : 자주 사용될 것으로 예측해 캐시 메모리에 저장했지만, 예측이 틀려 메모리에서 피룡한 데이터를 직접 가져와야 하는 경우
        - 캐시 적중률
            - 캐시 히트 횟수 / (캐시 히트 횟수 + 캐시 미스 횟수)
        - 참조 지역성의 원리
            - 시간 지역성 - temporal locality
                - 최근에 접근했던 메모리 공간에 다시 접근하려는 경향
            - 공간 지역성 - spatial locality
                - 접근한 메모리 공간 근처를 접근하려는 경향
