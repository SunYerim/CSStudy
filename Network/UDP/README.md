# UDP 



> UDP는 OSI 7계층 중 Transport Layer에 속한다

UDP (User Datagram Protocol)

Transport Layer는 멀티프로세스 환경의 등장에 따라서 프로세스 간 통신을 위하여 고안되었음



## Prerequisite

### Transport Layer의 담당 역할

* Source, Destination 까지 패킷이 제대로 전달될 수 있도록 함.

  * 이 때의 단위는 Port 가 된다.
  * Port는 프로세스를 구분하는 identifier가 됨

* Network 계층에서 들어오는 패킷들을 Multiplexing, Demultiplexing 하는 역할을 수행

  * 여러 개의 프로세스들이 Transport Layer로 데이터를 전달, 이러한 데이터를 하나의 채널로 전송하기 위해서 Multiplexing이 필요 (***송신***)
  * Network Layer에서 전달되는 패킷을 프로세스 단위로 분할하여 전송하기 위해서 Demultiplexing이 필요 (***수신***)

  <img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/CN_Multiplexing-4.jpg" alt="CN_Multiplexing-4" style="zoom:67%;" />

---



## UDP 특징 

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-07-25%20at%203.58.33%E2%80%AFAM.png" alt="Screenshot 2024-07-25 at 3.58.33 AM" style="zoom:50%;" />

> 프로토콜을 선정할 때의 그 프로토콜의 구조 또한 학습하여, MTU 등을 적절하게 고려하여 선정



1. 별도의 오류 제어, 혼잡 제어 메커니즘이 존재하지 않는다.

   * 송신자 $\rightarrow$ 수신자 사이의 packet 유실등에 대한 재전송 메커니즘 X

2. Internet Protocol을 기반으로하여 동작하는 Transport Layer의 Protocol 중 가장 단순한 형태

   * 기본적인 Transport layer의 역할만 수행 (mux, demux, light error checking)

   * 단방향 데이터 전송을 위해서 고안됨
   * IP Packet을 Wrapping 하는 용도로만 사용
     * source port (optional)
     * destination port (required)

3. 송신자, 수신자 사이의 Handshaking 과정이 존재하지 않음

   * *Connectionless* 라 불리는 이유
   * session을 만드는 오버헤드가 존재하지 않아서 비교적 가벼움



### 그러면 UDP는 왜 쓰이는가?

* 데이터 유실을 신경쓰지 않아도 큰 문제가 없고 실시간성이 중요할 경우
  * ex) `VoIP`, `DNS`
* 전송되는 데이터의 크기가 크지 않을 때
  * TCP일 경우는 헤더의 최소 크기가 20byte
  * 이 보다, 작은 크기의 데이터를 전송하기 위해서 TCP를 쓰는 것은 비효율적
* End point Application에서 오류 제어, 혼잡 제어 메커니즘이 구현되어 있을 때
  * 구글에서는 `HTTP3 + QUIC` 프로토콜을 활용, 여기서는 UDP를 사용
* End point Application에서 `상태를 유지할 필요가 없을 경우` 
  * stateless한 시스템일 경우, 송신자가 응답이 일정 시간 이상 오지 않으면 재전송하는 메커니즘으로 서버의 부담을 줄일 수 있음.



### 용례

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-07-25%20at%204.07.59%E2%80%AFAM.png" alt="Screenshot 2024-07-25 at 4.07.59 AM" style="zoom:80%;" />





## 면접 예상 질문

* TCP와 UDP의 차이점에 대해서 알려주세요
  * 연결 유지
  * 오류 제어
  * 혼잡 제어
* 어느 상황에 UDP를 사용하는 것이 적절할까요?
  * stateless
  * liveliness
  * connection 유지 비용이 부담될 때
  * 전송되는 데이터의 크기가 작을 때 (전송 오류 확률 감소, UDP에서는 segmentation이 이루어지지 않음)
* UDP를 사용한다면 무조건 `신뢰할 수 없는 전송`이 될까요?
  * 아니오! End point에서 오류 제어, 혼잡 제어를 담당하는 기능이 있을 경우 TCP의 무거운 오류 제어, 혼잡 제어 메커니즘을 서비스의 특성에 맞게 커스터마이징하여 신뢰성과 실시간성을 trade-off 할 수 있습니다. 그 대표적인 예로 HTTP/3 + QUIC 프로토콜을 활용한 구글을 들 수 있겠습니다.

