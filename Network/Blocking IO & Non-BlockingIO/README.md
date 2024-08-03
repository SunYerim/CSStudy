## Blocking I/O & non-Blocking I/O
I/O 작업은 Kernel level에서만 수행할 수 있다. 따라서 Process, Thread는 커널에게 I/O를 요청해야 한다

### 1. Blocking I/O
I/O Blocking 형태의 작업은 

(1) Process(Thread)가 Kernel에게 I/O를 요청하는 함수를 호출
<br/>
(2) Kernel이 작업을 완료하면 작업 결과를 반환 받음

<br/>

- **특징**
    - I/O 작업이 진행되는 동안 user Process(Thread)는 자신의 작업을 중단한 채 대기
    - Resource 낭비가 심함 (I/O 작업이 CPU 자원을 거의 쓰지 않으므로)

<br/>

```여러 Client가 접속하는 서버를 Blocking 방식으로 구현하는 경우``` <br/>
I/O 작업을 진행하는 작업 중지 → 다른 Client가 진행 중인 작업을 중지하면 안되므로, client 별로 별도의 thread를 생성 → 접속자 수 매우 많아짐

🚨 ***이로 인해, 많아진 threads로 컨텍스트 스위칭 횟수가 증가함 (비효율적인 동작 방식)***

<br/>

### 2. Non-Blocking I/O
I/O 작업이 진행되는 동안 User Process의 작업을 중단하지 않음
> A 함수가 I/O 작업을 호출했을 때 I/O 작업이 완료될 때까지 A 함수의 작업을 중단하지 않고 I/O 호출에 대해 즉시 리턴하고, A 함수가 이어서 다른 일을 수행할 수 있도록 하는 방식을 의미한다.

<br/>

- **진행 순서**
1. user porcess가 recvfrom 함수(입력 함수) 호출(커널에게 해당 socket으로부터 data를 받고 싶다고 요청함)
2. kernel은 이 요청에 대해서 곧바로 recvBuffer를 채워서 보내지 못하므로, "EWOULDBLOCK"을 return함
3. blocking 방식과 달리 user process는 다른 작업을 진행할 수 있음
4. recvBuffer에 user가 받을 수 있는 데이터가 있는 경우, buffer로부터 데이터를 복사하여 받아옴
5. recvfrom 함수는 빠른 속도로 data를 복사한 후, 복사한 data의 길이와 함께 반환

<br/><br/>