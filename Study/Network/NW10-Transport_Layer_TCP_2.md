# Transport Layer: TCP

## 1. TCP의 Reliable data transfer

### 우선 이전에 봤던 rdt를 다시 리마인드

- stop&wait

  하나씩 보낸다.

  너무 느려서 여러개를 보내는 시도를 하게 된다.

- pipelined segment

  - Go back N

    cumulatibe ack 을 사용해 최대 N개의 재전송이 이루어진다.

  - Selective Repeat

    잘못된것만 다시 보낸다.

### TCP 는 GBN 과 SR 의 장점을 취합

**TCP는 rdt service(reliable data transfer)를 IP 의 unreliable 한 서비스 위에 구축했다.**

- pipelined segments 

  여러개를 한번에 보낸다.

- cumulative acks

  각각에 ack을 하나씩 다 보내는게 아니라, 모아서 하나로 보냄

- single retransmission timer

  타이머를 하나만 돌려서 재전송한다.

**retransmission 은 다음의 것들에 의해 일어난다.**

- timeout events

  타이머는 가장 오래된 unacked 패킷 하나만 측정

- duplicate acks

  중복된 ack이 발생할 수 있다.

### 처음엔 다음과 같이 단순하게 가정해보자

- duplicate acks 이 안 일어난다고 생각하자
- flow control, congestion control 을 생각하지 말자.

### TCP 의 sender

- **data를 app으로 부터 받으면**

  - segment 를 seq# 를 붙여서 만든다.

  - seq# 는 세그먼트에서 첫번째 데이터 바이트의 숫자이다. (패킷단위 아님)

  - 만약 타이머가 안돌고 있었으면 타이머를 시작한다.

    - 타이머는 가장 오래된 unacked segment 를 기준으로 작동

    - 타이머 시간이 다 되는 인터벌을

      TimeOutInterval 이라고 하자.

- **Timeout 되면**

  - timeout 을 발생시킨 segment 를 재전송한다.

    타이머가 하나뿐이라서 unacked 중에서 가장 오래된 것 하나만 재전송된다.

  - 타이머를 재시작한다.

- **ack을 받으면**

  - 만약 이전에 unacked 이었던 것의 ack 이 온 것이면
    - window 를 이동한다. (ack 으로 업데이트해서)
    - 만약 아직 unacked 된 게 있으면 타이머를 다시 시작한다.

### retransmission 은 언제 일어날까?

- lost ACK

  ack 이 오다가 lost 되면 타임아웃되어서 재전송하게 된다.

- premature timeout

  ACK을 보냈는데, ACK이 오고있는 도중에 벌써 타임아웃되어서 재전송됨.

  ACK 이 늦었거나, 타임아웃이 빨랐거나.

- cumulative ACK 이기 때문에, 괜찮은 경우

  100에 대한 ACK 이 오다가 lost 되었다.

  그런데, 120에 대한 ACK 이 100의 타이머가 타임아웃되기 전에

  도착해서, 재전송 없이 잘 동작되는 경우.

## 2. TCP 의 fast retransmit

타임아웃이 연속되게 일어나면, 2배씩 타임아웃시간이 길어지는데(2배씩 더 기다려줌), 이 때문에 속도문제가 생긴다.

-  타임아웃의 시간은 길다.
  - 타임아웃이 될때 까지 기다렸다 재전송되면, lost 패킷에 대한 반응이 느리다. (delay)
- lost segment 를 duplicate ACKs 로 감지할 수 있다.
  - sender 는 보통 많은 세그멘트들을 뒤이어서 보낸다.
  - 만약 세그멘트가 lost 되었다면, 많은 duplicate ACKs 가 일어날 것이다.

### 도대체 무슨말인가?

**핵심은 TCP 는 ACK 을 이전과 다른 방식으로 사용한다는 것이다.**

TCP 는 "잘 받았어" 라는 의미로 ACK 을 쓰는 대신,

**"앞에 이 번호가 안왔어 그것 때문에 내가 app layer 로 못올라가고 있어"** 라는 의미로 사용한다.

예를들어

sender 가 100, 101, 102, 103 을 보냈는데, 100이 제대로 도착하지 못했다고 해보자.

원래대로라면, receiver 는 100번을 다시 받기위해 sender 의 타임아웃을 기다렸어야 한다.

101을 받았을 때, 100에 대한 ACK 을 보낸다. 앞에 100번이 아직 도착하지 않았다는 의미로.

마찬가지로, 102, 103 도 100번 ACK 을 보낸다.

sender 는 이렇게 3개의 ACK 을 같은 번호로 받으면, (이것이 바로 duplicate ACKs 이다.)

아직 타임아웃이 일어나지 않았더라도, 무언가 잘못되었다고 판단해서 재전송을 하게 된다.

**즉, 3개나 ACK 이 올 정도면, lost 되었을 것이라고 가정하는 것.**

## 3. TCP 의 flow control

### 주의: 이 두 가지를 구분하자

이 두 가지가 하고자 하는 일은 같다. 바로 sender 의 속도 조절이다.

그러나 **왜 조절 하는가** 는 다르다.

- **flow control**

  receiver 의 buffer overflow 를 막기 위해

  비교적 간단하다.

- **congestion control**

  중간의 Network 들의 traffic 양을 조절하기 위해

  비교적 복잡, 어렵다. network 전체 상황을 보아야 하기 때문.

### flow control

receiver 쪽에서 sender 를 컨트롤하는 것이다.

receiver 쪽에서 sender 에게 자신의 버퍼의 크기를 알려준다.

(TCP header 에 buffer 의 크기를 적는 부분이 있다.)

이 정보를 통해 sender 의 전송 속도를 조절한다.

### receiver 의 버퍼 오버플로우가 나는 이유?

TCP 소켓에는 receiver buffer 가 있다.

이 buffer 에 있는 데이터를 application 쪽에서 가져가서 사용한다.

하지만 app 이 데이터를 읽어가는 속도가 TCP 쪽에서 데이터를 넣는 속도보다 느리면?

버퍼에 데이터가 쌓이기 시작한다.

이것이 계속되어서 버퍼가 꽉찼는데도 데이터가 들어오게 되면, 데이터는 버려진다. (overflow)

이것을 방지하기 위해 flow control 이 동작한다.

### flow control 의 동작과정을 자세히 보자

> RcvBuffer: receiver 쪽 버퍼의 총 크기 (보통 8KB, 책에서는 보통 4KB 라고 함)
>
> rwnd: free buffer space. (즉, RcvBuffer에서 buffered data 를 뺀 값)

- receiver 는 **rwnd(receiver window)** 값을 receiver-to-sender segment 의 TCP header 에 넣는다.

  이를통해 자신의 free buffer space 를 알린다.

  - **RcvBuffer** 의 size 는 socket 옵션에 따라 결정된다.
  - 많은 운영체제가 자동으로 RcvBuffer 를 조절한다.

- sender 는 unakced data 의 수를 제한해서 속도를 조절한다. (윈도우 사이즈 조절)

- receive buffer 가 오버플로우 나지 않는것을 보장한다.

## 3. Connection management

데이터를 교환하기 전에, sender 와 receiver 는 **handshake** 를 한다.

> handshake 의 두 가지
>
> - 설정: "여보세요"
>
>   ​         "여보세요"
>
> - 해제: "이제 끊을게"
>
>   ​         "어 끊어"

- **connection state: ESTAB**

  소켓의 상태가 서로 연결되면 Establish 상태로 바뀜

- **connection variables: seq #**

  - client-to-server
  - server-to-client

  seq 번호가 0부터 시작하지 않는다.

  random으로 시작번호가 정해진다.

  그래서 연결할 때 그 번호를 알려줘야 한다.

- **rcvBuffer size at server, client**

  receiver buffer 의 크기를 서로 알려준다.

  flow control 을 하기 위해서.

### TCP 연결: 3-way handshake

| client                                       |                                                  | Server       |
| -------------------------------------------- | ------------------------------------------------ | ------------ |
| SYN 보냄                                     | SYNbit = 1, Seq = x ->                           |              |
|                                              | <- SYNbit = 1, Seq = y, ACKbit = 1; ACKnum = x+1 | SYN ACK 보냄 |
| SYN ACK의 ACK 보냄 여기부터 데이터 전송 가능 | ACKbit = 1, ACKnum = y+1 ->                      |              |

> 왜 2-way 아니고 3-way 인가?
>
> -> 확실하게 하기 위해
>
> 만약 SYN ACK을 못받으면? (중간에 lost 되거나 해서)
>
> Server 는 계속 포트를 닫지 못하고 열고 있게 된다.
>
> 그래서 SYN ACK 의 ACK 도 필요하게 된 것.

### TCP closing a connection: 4-way handshake

| client                                                    |                             | Server                                                       |
| --------------------------------------------------------- | --------------------------- | ------------------------------------------------------------ |
| close() 한 후 FIN 보냄 ("야 끝내자")                      | FINbit = 1, Seq = x ->      |                                                              |
|                                                           | <- ACKbit = 1; ACKnum = x+1 | ACK 보냄 ("OK 너 끝내고 싶구나") 그리고 할 일을 마감하고 close() |
|                                                           | <- FINbit = 1, seq = y      | close 했다고 알려줌                                          |
| 타이머 켜서 타이머 기다림. 만약 내가 보낸 ACK 분실될까봐. | ACKbit = 1, ACKnum = y+1 -> |                                                              |

---

