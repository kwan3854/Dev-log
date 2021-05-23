# Transport Layer

- 4계층에 해당한다.
- 아래의 계층들은 모두 잘 동작한다고 가정하고 app to app 연결을 생각한다.

- Transport protocol 은 end system 에서 동작한다.

  - **send 쪽:** app message 들을 segment 들로 쪼갠다.

    그리고 그것들을 네트워크 레이어로 보낸다.

  - **recieve 쪽:** segment 들을 다시 합쳐서 메시지로 만든다.

    만든 메시지를 app layer 로 올려보낸다.

- TCP 와 UDP 가 대표적인 인터넷의 transport protocol 이다.

## Transport layer vs Network layer

| Network layer                            | Transport layer                            |
| ---------------------------------------- | ------------------------------------------ |
| **호스트 사이** 의 논리적인 커뮤니케이션 | **프로세스 사이** 의 논리적인 커뮤니케이션 |

## 인터넷 Transport-layer 프로토콜

- **reliable, in-order delivery (TCP)**

  깨지면 다시 보내고, 다시 받는다. (신뢰성 있음)

  - congestion control
  - flow control
  - connection setup

- **unreliable, unordered delivery (UDP)**

  깨지면 그냥 drop 한다.

  - best-effort

- 이런건 제공하지 않는다

  - 딜레이의 보장
  - bandwidth 의 보장
  - 이것들은 2-3 계층이 담당한다.

---

- 3계층은 보장하지 않는다.
  - 주고 받고는 잘 했다. 그거는 잘 해놓고 갑자기 라우터 죽어버리면?
  - routing 잘못되어서 무한 loop 에 빠지면? -> TTL 0 -> 소멸
- 2계층도 100% 보장된다고 할 수는 없다.
  - 어떤 2계층 프로토콜은 신뢰성 보장이 되지 않는다.
  - 어떤 2계층 사용하는지 위에서는 알 수 없다.

## Multiplexing/Demultiplexing

- sender 쪽에서 multiplexing 을 함
- receiver 쪽에서 demultiplexing 을 함

## How demultiplexing works

1. 호스트가 IP datagram(패킷)을 받는다.
2. 각 datagram 은 source IP 주소, destination IP 주소를 갖고 있다.
3. 각 datagram 은 하나의 transport-layer segment 를 운반한다.
4. 각 segment 는 source 와 destination 포트 번호를 가지고 있다.
5. 호스트는 IP 주소와 포트 번호를 이용해 segment 를 적절한 소켓에 보낸다.

---

- **TCP 는 socket : socket, 1:1로 통신됨.**

  -> connection

- **UDP 는 1:1 이 아니라도 된다.**

  -> connectionless

---

### Connection-oriented demux (TCP)

TCP 소켓은 4개의 tuple 로 구별된다.

- source IP address
- source port number
- dest IP address
- dest port number

receiver 측은 이 4가지 값들을 이용해 segment 를 적절한 소켓으로 보낸다.

---

## UDP: User Datagram Protocol

- 부가기능 없이, 베어본(필수기능)만 제공하는 인터넷 tranport protocol.
- best effort
  - lost 발생 가능
  - out-of-order 로 app 에게 전송될 수 있음
- **connectionless**
  - no handshaking between UDP sender, receiver.
  - 각각의 UDP segment 는 서로 독립적으로 다루어진다.
- UDP 는 어디에 사용되는가
  - 멀티미디어 스트리밍
  - DNS
  - SNMP
- UDP 를 통한 신뢰성있는 전송? -> X
  - 신뢰성은 Application layer 쪽에서 담당함

- 왜 UDP 를 쓰지? 신뢰성 보장도 안되는거?
  - connection 채결 없이 동작된다. -> 딜레이가 비교적 적다.
  - simple 하다. -> connection 필요없음.
  - header size 가 작다.
  - congestion control 이 없다. UDP 는 원하는 만큼 빠를 수 있다.
- UDP 는 checksum 을 사용한다.

## Reliable data transfer

| send side                                       | receive side                                      |
| ----------------------------------------------- | ------------------------------------------------- |
| rdt_send() -> reliable                          | deliver_data()                                    |
| -reliable data transfer protocol(sending side)- | -reliable data transfer protocol(receiving side)- |
| udt_send() -> unreliable (best effort)          | rdt_rcv() -> reliable                             |
| -unreliable channel-                            | -unreliable channel-                              |

- rdt : reliable data transfer protocol
- udt :unreliable data transfer protocol

---

- checksum 을 통해 에러를 detect 한다. (데이터 깨진거만, loss 는 detect 못함)

- 만약 에러가 확인되면 다시 보내달라고 해야 함. 이를 위해 이 두가지를 사용.

  (receiver 가 sender 에게 잘 받았는지 알려주는 용도로)

  - ACKs (acknowledgemnets)

  - NAKs (negative acknowledgements)

    NAK을 sender 가 받게 되면 다시 보내줌.

- 만약 ACK/NAK 이 깨졌으면?

  -> 이런 상황을 대비해, sequence number 를 각 패킷에 매김.

  만약 ACK/NAK 이 깨져서 오면, 일단 다시 보내줌. (NAK 이었을 것이라 가정하고)

  그러면, 받는애는 패킷의 sequence number 를 보고, 이것이 중복된 패킷을 또 받은건지,

  아니면 제대로 못받았던거 받는건지 확인.

- 이 때, sequence number 는 0과 1만 있으면 작동 가능 (stop&wait 기법을 사용해서)

  > stop & wait 기법이란?
  >
  > sender 가 한 패킷을 보내고, receiver 의 response 를 기다린다.
  >
  > 좀있다 다루겠지만, 하나씩 보내면 속도가 느려서 결국 하나씩 보내지 않고
  >
  > 여러 패킷을 한번에 보내는 방법을 사용하게 된다.

- ACK, NAK 구분없이 ACK 만 쓸 수 있을까?

  -> 가능하다. ACK 에도 seq number 가 있으니까.

  만약 0에대한 ACK 기다리는데, 1에대한 ACK 왔다? NAK 과 동일한 의미.

- 그렇다면 loss 는 어떻게 아는가?

  -> Time out 개념을 도입한다.

- Time out 시간은 얼마정도로 잡아야 하는가?

  -> 적어도 (데이터 가는 시간 + 데이터 처리 시간 + 데이터 오는 시간) 보다는 많이 기다려야 한다.

- 만약 충분히 기다렸는데도 답 없으면,

  - 내가 보낸 Data가 loss 되었거나
  - 상대가 보낸 ACK 이 loss 되었거나

---



