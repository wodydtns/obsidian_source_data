|      | 차수  | 실행 시간(ms) | CAS와 Lock의 시간 차이 |
| ---- | --- | --------- | ---------------- |
| CAS  | 1차  | 46        | 106ms            |
| Lock | 1차  | 152       | 106ms            |
| CAS  | 2차  | 46        | 108ms            |
| Lock | 2차  | 154       | 108ms            |
