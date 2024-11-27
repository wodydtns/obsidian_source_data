InitiatingHeapOccupancyPercent(IHOP) 또는 IOHP는 G1 GC(Garbage-First Garbage Collector)에서 사용되는 중요한 임계값 매개변수

1. 기본 개념
- 전체 힙 사용률의 임계값을 지정하는 매개변수
- 이 임계값에 도달하면 동시 마킹 사이클(concurrent marking cycle)이 시작됨
- 기본값은 45% (JVM 버전에 따라 다를 수 있음)

2. 주요 기능
- GC 시작 시점 결정
- 메모리 회수 시기 최적화
- Full GC 방지를 위한 선제적 조치

3. 설정 방법
```bash
-XX:InitiatingHeapOccupancyPercent=<n>
```
- n은 0에서 100 사이의 값
- 낮은 값: 더 일찍, 더 자주 GC 발생
- 높은 값: 더 늦게, 덜 빈번한 GC 발생

4. 동적 조정
- Adaptive IHOP: JVM이 자동으로 값을 조정
- 실행 중인 애플리케이션의 동작 패턴에 따라 최적화
- -XX:+G1UseAdaptiveIHOP 옵션으로 활성화 (기본적으로 활성화됨)

5. 영향을 미치는 요소
- 애플리케이션의 객체 할당 속도
- Old 영역으로의 승격 비율
- GC 일시 중지 시간 목표
- 전체 힙 크기

6. 최적화 전략
낮은 값 설정이 좋은 경우:
- 메모리 요구사항이 급격히 변하는 경우
- Full GC 위험을 최소화하고 싶은 경우
- 일시 중지 시간에 민감한 애플리케이션

높은 값 설정이 좋은 경우:
- 메모리 사용이 안정적인 경우
- GC 오버헤드를 최소화하고 싶은 경우
- 처리량이 중요한 애플리케이션

7. 모니터링 및 튜닝
확인해야 할 지표:
- Full GC 발생 빈도
- GC 일시 중지 시간
- 힙 사용률 패턴
- Old 영역 점유율

튜닝 접근 방법:
1. 기본값으로 시작
2. GC 로그 분석
3. 성능 지표 모니터링
4. 점진적인 값 조정
5. 결과 검증

8. 주의사항
- 너무 낮은 값: 불필요한 GC 오버헤드
- 너무 높은 값: Full GC 위험 증가
- 워크로드 특성 고려 필요
- Adaptive IHOP과의 상호작용 고려

9. 관련 매개변수들
- -XX:G1HeapWastePercent
- -XX:G1MixedGCLiveThresholdPercent
- -XX:G1MaxNewSizePercent
- -XX:G1ReservePercent
