## CPU Scheduling

---

CPU를 잘 사용하기 위해 프로세스를 잘 배정하기 <br>
`오버헤드 ↓ 사용률 ↑ 기아 현상 ↓`

**목표**✨✨<br>

- **Batch System** : 가능하면 많은 일을 수행 (수행 시간 보다 처리량 중요)
- **Interactive System** : 빠른 응답 시간. 적은 대기 시간
- **Real-time System** : 기한 맞추기

<br>

<img src="https://github.com/user-attachments/assets/79b4f015-75f9-45b9-88a9-585b9c77ad53">

<br>

**프로세스의 상태 전이** <br><br>
✅ **승인(Admitted)** : 프로세스 생성이 가능하여 승인됨 <br>
✅ **스케줄러 디스패치(Scheduler Dispatch)** : 준비 상태에 있는 프로세스 중 하나를 선택하여 실행시키는 것 <br>
✅ **인터럽트(Interrupt)** : 예외, 입출력, 이벤트 등이 발생하여 현재 실행 중인 프로세스를 준비 상태로 바꾸고 해당 작업을 먼저 처리하는 것 <br>
✅ **입출력 또는 이벤트 대기 (I/O or Event wait)** : 입출력/이벤트가 끝난 프로세스를 준비 상태로 전환하여 스케줄러에 의해 선택될 수 있도록 만드는 것

<br><br>

### 선점 / 비선점 스케줄링

---

**선점** <br>
하나의 프로세스가 cpu를 차지하고 있을 때, **우선순위가 높은 다른 프로세스가 현재 프로세스를 중단**시키고 cpu를 점유하는 스케줄링 방식 (처리 시간 예측 어려움) <br>

<br>

**비선점** <br>
한 프로세스가 cpu를 할당받으면 작업 종료 후 cpu 반환 시까지 다른 프로세스는 cpu 점유가 불가능 (처리 시간 예측 용이)

- FCFS (First Come First Served) <br>
  - 큐에 도착한 순서대로 CPU 할당
  - 실행 시간이 짧은 게 뒤로 가면 평균 대기 시간이 길어짐
- SJF (Shortest Job First)
  - 수행 시간이 가장 짧다고 판단되는 작업을 먼저 수행
  - FCFS 보다 평균 대기 시간 감소, 짧은 작업에 유리
- HRN (Hightest Response-ratio Next)
  - 우선순위를 계산하여 점유 불평등을 보완한 방법(SJF의 단점 보완)
  - 우선순위 = (대기 시간 + 실행 시간) / (실행 시간)
