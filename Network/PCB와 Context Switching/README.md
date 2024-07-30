# PCB와 Context Switching
## Prerequisites

* multi-tasking의 등장

  * multi-programming에서 이전 작업의 처리 시간이 길어질 경우, 전체 처리 시간이 길어지는 문제점을 보완
    * 이전 작업이 완료되기 전까지는, 현재 작업을 진행하지 않기 때문
  * time-slicing 방식을 채택하여, 일정 주기(=`time quantum, time slice`)마다 다른 프로세스에게 processor의 점유권을 전달하는 방식 $$\rightarrow$$ `multi-tasking` 
    * `라운드 로빈` 프로세스 스케쥴링 방식 등장
    * 동시성 (`concurrency`) 증가 효과 기대
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-07-30%20at%209.35.15%E2%80%AFPM.png" alt="Screenshot 2024-07-30 at 9.35.15 PM" style="zoom:67%;" />

* 현대의 OS는 최소 실행 흐름인 `Thread` 가 등장하면서, Thread 별로 스케쥴링 정책을 실시

  * 이 경우 Thread의 state를 표현하는 `TCB(Task Control Block)` 단위를 사용
  * Process에 Thread가 속해있는 형식이므로, TCB또한 내부적으로 PCB를 참조하고 있음
  * 같은 Process 내부에 존재하는 Thread는 Process의 address space를 공유하므로, TCB에는 상태 저장을 위한 최소한의 레지스터 셋만 표현되어있음
    * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-07-30%20at%2010.51.13%E2%80%AFPM.png" alt="Screenshot 2024-07-30 at 10.51.13 PM" style="zoom:50%;" />

  

## Challenges

* 처리되지 못한 프로세스에 대해서, 프로세서가 처리한 부분부터 다음에 처리할 수 있게끔 어떻게 구현할 수 있을까?
  * 프로세스의 state를 표현하는 정보들이 필요하다.
  * 이를 `PCB(Process Control Block)` 이라고 한다.
  * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-07-30%20at%2010.58.35%E2%80%AFPM.png" alt="Screenshot 2024-07-30 at 10.58.35 PM" style="zoom:67%;" />
* time slice를 무조건 짧게 둘수록, 반응성은 증가하지만 작업 전체의 수행 시간은 늘어난다.
  * process context switching에 드는 오버헤드가 有
  * 적절한 time slice를 어떻게 두는 것이 좋을까?
    * 리눅스에서는 CFS 스케쥴러에서 time slice를 유동적으로 결정



## Context Switching 이란?

* 현재 프로세스의 실행 상태를 PCB에 저장하고 wait queue에 넣고, 스케쥴링되어 pop된 프로세스의 PCB를 참조하여 프로세서가 실행할 수 있는 환경으로 만드는 일련의 과정

  <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/img1.daumcdn.jpg" alt="img1.daumcdn" style="zoom:75%;" />

  * 주로, address space 전환에 많은 오버헤드가 든다.
    * TLB 와 cache를 flush 해주어야함
    * [참고](https://yohda.tistory.com/entry/%EB%A6%AC%EB%88%85%EC%8A%A4-%EC%BB%A4%EB%84%90-Scheduler-TLB)
  * 같은 process내의 thread간의 switching은 아주 빠르게 이루어진다.
    * CPU내의 레지스터만 변경, address space 전환 X

* 참고

  * TLB(Translation Lookaside Buffer)
    * n-level 페이지테이블에서 물리 메모리 접근을 위해서 $$n$$번의 메모리 참조가 필요 (페이지 테이블 참조 횟수)
    * 일종의 캐시 역할을 하여, page table 참조 시에 $$n$$번의 접근 메모리 대신, (가상 주소 $$\rightarrow$$ 물리 주소) 정보에 대한 값을 TLB에서 참조할 수 있게끔 함
    * <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/TLB.jpg" alt="TLB" style="zoom:50%;" />



### Context Switching의 고질적인 문제점

1. Process 간 전환시의 Address Space 정보를 바꾸기 위한 오버헤드

   * TLB 캐시를 모두 flush 해야 함
     * 현대에는 ASID(Address Space Identifier)를 TLB에 추가적으로 둬서 TLB Flush를 최소화
   * MMU가 주소를 변환하기 위해서 물리 주소의 base register 값도 변환해주어야함

2. 캐시 오염

   <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/cache-pollusion.jpg" alt="cache-pollusion" style="zoom:67%;" />

   * 이전의 프로세스에서 캐싱해두었던 값들은 더 이상 의미가 없음

   * context switching 이후에 cache miss가 빈번하게 발생하는 현상을 의미함

     



