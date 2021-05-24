# Transport Layer: TCP

### TCP 에 대해 간단히

- point-to-point

  - 하나의 sender 와 하나의 receiver 간의 연결

- reliable, in-order byte(패킷) stream

- pipelined

  윈도우 사이즈를 가변적으로 조절하면서

  TCP 의 congestion 과 flow control 이 이루어진다.

  >  예를들어
  >
  > congestion 이 발생하면 윈도우 사이즈 N 을 줄인다.
  >
  > 만약 congestion 없이 잘 가면 N 을 늘릴 수 있다.

- full deplex data

  - 데이터가 오고 가고 동시에 가능

    (cf. half duplex 오거나 가거나 둘중 하나만 한번에 가능)

  - MSS: Maximum Segment Size

    > 이름은 segment 이지만, application data 들어가는 곳의 크기를 말하는 것이다.
    >
    > 즉, segment 에서 header 제외한 크기

    Application 쪽에서 data 가 오면, segment 단위로 잘라서 쓰는데,

    그 크기의 Max 값을 의미한다.

  - Connection-oriented

    - handshaking

      데이터 교환 전에 sender 와 receiver 가 준비 필요

  - Flow controlled

    sender 는 receiver 를 압도해서는 안된다.

    받는 애의 buffer 크기에 맞춰서 보내는 애가 보내는 속도 조절해줘야 한다.

## TCP 의 seq number 와 ACKs

TCP 는 기본적으로 Go Back N 과 selective repeat 의 장점을 취합해서 사용한다.

### sequence number

sequence number 는 패킷단위로 매겨지는게 아니라,

byte 단위로 매겨진다.

> 예를들어,
>
> sender 가 30byte 부터시작해서 총 20byte 를 보내면 (31~50byte 를 보낸다)
>
> receiver 는 ack 51을 보낸다. (51부터 받을 차례라는 뜻)

### Acknowledgements

- 다음으로 받기를 기대되는 seq number 를 보낸다.
- cumulative ACK

### receiver 쪽은 잘못된 segment 받으면 어떻게 처리?

TCP 의 스펙에서는 이것을 딱 정해놓지는 않았다.

TCP 구현자에게 알아서 하게 맡겨놓았다.

그러나, 보통 selective repeat 방법을 사용한다. (discard 안하고 저장해 놓는 방식)

### data의 전송과 ack 을 동시에

data의 전송과 ack을 동시에 할 수 있다.

data를 넣고 헤더를 붙이는데, 그 헤더에 ack 이 들어가기 때문.

1. **Host A**    `Seq = 42, ACK = 79, data = 'C'` -> **Host B**
2. **Host A** <- `Seq = 79, ACK = 43, data = 'C'`    **Host B**
3. **Host A**     `Seq = 43, ACK = 80` ->                        **Host B**

## TCP 의 round trip time, timeout

### timeout 값은 얼마로 정해야 할까?

- RTT 시간보다는 길어야 한다.

  적어도 왕복시간은 기다려 줘야 한다.

  (RTT 는 어떻게 알지? 그때 그때 다를텐데?)

- 너무 짧으면

  premature timeout

  불필요한 재전송이 이루어진다.

- 너무 길면

  slow reaction

  segment loss 에 대한 반응이 느려진다.

### RTT 는 어떻게 추정하지?

- SampleRTT

  샘플을 수시로 보내서 Round Trip Time 을 측정한다.

- 최근 측정치에 가중치를 둬서, 기존 측정치들과 평균을 내서 SampleRTT 를 구한다.

### RTT 의 계산법

`EstimatedRTT = (1 - a) * EstimatedRTT + a * SampleRTT`

- exponential weighted moving average

  최근값의 반영비중이 크다.

  그리고 그 영향력은 기하급수적으로 감소한다.

- 보통 `a = 0.125` 이다.

### timeout 의 인터벌

- `EstimatedRTT + "safety margin"`

  EstinatedRTT가 바리에션이 크면

  -> safety margin 이 커져야 한다.

- EstimatedRTT 로부터, SampleRTT 의 편차 추정하기

  > 이 역시 최근값의 비중을 많이 반영하기 위해 Moving average 사용

  `DevRTT = (1-B) * DevRTT + B * |SampleRTT-EstimatedRTT|`

  (보통, B = 0.25)

- 최종적으로

  `TimeoutInterval = EstimatedRTT + 4*DevRTT `

  (4*DevRTT = safety margin)

