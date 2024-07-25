# TCP/IP 흐름제어, 혼잡제어 방식



## Prerequisite

* TCP 프로토콜은 **IP프로토콜을 보조**하여, 혼잡 제어 & 에러 제어를 수행
  * 두 개 모두 sender에게 throttling을 거는 것은 유사하지만, 다른 용어

* Network Layer는 `routing 과  packet forwarding` 역할을 충실하게 수행

* Transport Layer는 `reliable transmission` 을 담당

---



## TCP 특징

TCP/IP 모델은 연결지향적인 신뢰성 있는 패킷 전달을 위한 모델



## Challenge

* 패킷 손실
  * 네트워크 파티션이 일어난다면 패킷이 손실될 수 있음 
* 순서 바뀜
  * packet switched network에서 패킷이 도달하는 순서는 다를 수 있음
  * TCP에서는 데이터를 Segment단위로 쪼개서 network layer에 전송, 이 때 segment에는 data의 sequence number가 존재하여, 수신 측 Transport layer에서 이를 조립하여 합친다.
  * 누락된 패킷이 도착할 때까지 해당 패킷을 유지하고 처리를 지연
* 혼잡
  * 수신 버퍼에 데이터가 적재되는 속도가 application이 읽는 속도보다 빠르다면, 데이터의 유실 가능성이 존재한다.
* 에러 제어
  * checksum을 이용한 오류 검출
  * ack를 이용한 패킷 전송 성공 여부 확인
  * timer를 이용하여 time out 시간을 계측하여 일정 시간이 지나도 ack가 오지 않으면, 재전송하는 방식을 채택



## 흐름제어

* 등장 배경

  * Stop and Wait 방식으로 매번 ACK를 기다리는 방식은 비효율적
  * 수신 버퍼를 두어, 한 번에 받을 수 있는 데이터의 크기를 지정 (**슬라이딩 윈도우 방식**)
    * 수신 버퍼는 application의 처리속도가 전송 속도보다 느릴 때, 또는 in-order delivery가 아니라고 판단할 경우, 버퍼링 용도로 사용

* **수신 클라이언트**가 수행, 자신의 `receive window` 의 크기를 송신 클라이언트에게 알려줌

* receive window의 유휴 공간은 동적으로 변화하는데, 하나의 TCP Connection에서 아래와 같은 조건을 만족함

  1. $$LastByteRcvd - LastByteRead \leq RcvBuffer$$
  2. $$rwnd = RcvBuffer - [LastByteRcvd - LastByteRead]$$

  

  이 때 송신자는 `LastByteSend` - `LastByteAcked` 정보를 유지하므로서, 즉 `ack 응답을 받지 못한 데이터의 크기` 를 $$rwnd$$ 크기보다 적게 유지하므로서 (<u>송신 윈도우[*advertised window*] 크기 조절</u>) 오버플로우를 방지할 수 있음

<img src="https://raw.githubusercontent.com/joonamin/UpicImageRepo/master/uPic/Screenshot%202024-07-26%20at%2012.48.56%E2%80%AFAM.png" alt="Screenshot 2024-07-26 at 12.48.56 AM" style="zoom:80%;" />

> 흐름 제어는 송신 클라이언트와 수신 클라이언트 간의 처리 속도와 관련된 제어 방식

{: .prompt-info}

## 혼잡제어

* 흐름제어만으로는 **네트워크 혼잡**을 해결할 수 없다.

* 네트워크의 혼잡을 방지하기 위한 기법
  * 엄청난 수의 패킷이 전달되고, 해당 패킷들이 특정한 이유로 drop될 경우 재전송을 수행하는데  이 과정에서 네트워크에 엄청난 혼잡을 야기할 수 있음

* **송신 클라이언트**가 수행해주는 제어 기법
  * 2개의 윈도우를 사용 (`advertised window`, `congestion window`)
  * 그 중, congestion window는 세그먼트를 전송할 때 ACK를 받지 않고 연속해서 보낼 수 있는 세그먼트의 양을 결정.
    * TCP는 Awnd 만큼 연속해서 세그먼트를 보낼 수 있지만, 혼잡제어로 인해서 Awnd만큼 전송할 수 없고 Cwnd만큼 전송하게 됨

> 혼잡 제어는 재전송 메커니즘으로 인한 네트워크 혼잡과 관련된 제어 방식

{: .prompt-info}

## 면접 예상 질문

* TCP의 흐름제어에 대해서 설명해주세요
  * 송신 클라이언트와 수신 클라이언트 사이의 데이터 처리 속도에 따른 오버플로우를 제어하기 위한 기법입니다. 수신 클라이언트는 자신의 수신 버퍼에서 `가용 크기 이내의 한번에 받을 수 있는 세그먼트의 크기` 를  송신 클라이언트에게 알려주고, 이를 통해서 송신 클라이언트는 `송신 윈도우 크기`를 조절하여  흐름제어를 수행합니다.
* TCP의 혼잡제어에 대해서 설명해주세요
  * 재전송 메커니즘으로 인한 네트워크 혼잡을 제어하기 위한 기법입니다. 네트워크 혼잡을 예상하여 송신 클라이언트는 송신 속도를 조절하여 혼잡으로 인한 붕괴 현상을 예방합니다.

