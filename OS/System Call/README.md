# System Call

## Prerequisites

* CPU Instruction은 2가지 명령어 타입이 존재

  * 특권 명령 (privileged instruction)
  * 일반 명령

  이 중에서 특권 명령은 `kernel mode` 에서만 동작한다. 

  

* CPU의 mode bit를 통해서, 현재 CPU가 커널 모드로 동작하는지 유저 모드로 동작하는지 알 수 있음

  * user process는 이러한 mode bit에 접근할 수 없음
  * [참고](https://stackoverflow.com/questions/17045169/how-does-kernel-protect-mode-bits-psw-from-being-modified-by-user-program)

  

* 일부 유저 프로세스에서는 특권 명령을 사용해야할 필요가 있음

  * 대부분의 경우 직접 시스템 콜을 이용하기 보다는, high-level의 API를 사용함. 간접적으로 API에서 System call을 호출하는 형식
    * e.g.)
    * `printf()`  ➡️ API 
    * `write()` ➡️ System Call
  
  이 경우에 Kernel Mode로 진입하기 위한 Interface로 System call이 존재한다.
  
  
  
  

## System Call의 구조

  

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/image-20240801230837815.png" alt="image-20240801230837815" style="zoom:80%;" />



System Call은 user application이 kernel mode의 특권 명령을 실행하기 위해서 반드시 직.간접적으로 거쳐야하는 interface이다.



위 diagram을 설명하자면 아래와 같다.

1. User Application의 상태를 저장
2. Interrupt Vector Table에서 Interrupt Service Routine의 주소를 찾는다.
3. Interrupt Service Routine(일종의 Interrupt Handler) 실행



### Under the hood

#### System call 발생 시작 ~ Interrupt Vector Table 까지

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/image-20240801231720418.png" alt="image-20240801231720418" style="zoom:80%;" />

#### Interrupt Service Routine 실행 ~ 이 후



<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/image-20240801232538063.png" alt="image-20240801232538063" style="zoom:80%;" />



* 일반적으로 인터럽트 처리 이후에 프로세스의 실행 흐름을 이어나가기 위하여 ISR에서는 아래와 같은 과정을 수행
  * 인터럽트 내부에서 또 다른 인터럽트 금지
  * 프로세스의 상태 저장 (`SAVE ALL`)
  * 인터럽트 처리
    * system call과 같이 parameter를 필요로 할 경우에는 아래와 같은 방법들로 넘길 수 있음
      1. 레지스터
      2. 메모리 블록 할당 이후, 시작 주소 레지스터에 넣어서 전달
      3. 스택 이용
  * 프로세스 상태 복구
  * 인터럽트 허용
* kernel space로 진입할 때, 자동으로 하드웨어적으로 `mode bit` 가 1(User mode)에서 0(kernel mode)로 전환되어 ISR 실행



## 면접 예상 질문
* System Call은 무엇인가요?
	* System call은 user mode로 동작하는 일반적인 application에서 파일 생성, 프로세스 생성등과 같은 특권 명령을 수행하기 위해서 커널 모드로 진입하도록 도와주는 인터페이스 입니다.
* System Call을 사용하지 않고 특권 명령을 실행할 수는 없나요?
  * 네, 현대의 OS에서는 치명적인 결과를 초래할 수 있는 명령어들에 대해서 하드웨어적 도움을 받아 실행할 수 있는 명령어를 특권 명령과 일반 명령으로 구분합니다. CPU Architecture마다 특권 명령을 단계별로 [구분](https://en.wikipedia.org/wiki/Protection_ring) 하여 제공하기도 합니다.


