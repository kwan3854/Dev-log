# TCP: Principles of congestion control

### Congestion 이란?

- network 가 감당할 수 없을 정도로 너무 많은 source 들이 너무 많은 데이터를 너무 빨리 전송하는 것.

- flow control 과는 다르다!

  - flow control:

    - receiver 의 버퍼 오버플로우를 막기위해 sender 의 속도조절
    - rwnd (receiver window)

  - congestion control:

    - 네트워크의 congestion 때문에 sender 의 속도조절
    - cwnd (congestion window)

  - rwnd 와 cwnd 라는 두 가지 기준 중, 어느 기준에 맞춰서 전송 속도를 결정?

    -> 둘 중 더 작은 것 기준. min(rwnd, cwnd)

- 일어날 수 있는 일

  - lost packets

    라우터에서 버퍼 오버플로우가 생기면 lost 될 수 있다.

  - long delays

    queue 의 끝쪽, 버퍼의 마지막 부분에 들어온 패킷들은 버퍼에서 나가기까지

    시간이 많이 걸린다. 즉 delay 가 커진다. (앞에 줄이 기니까)

## 1. Congestion 의 원인과 비용 : 시나리오 1

### 가정

- 두개의 sender, 두 개의 receiver
- output link 의 capacity: R
- (비현실적 가정) 하나의 라우터, 라우터의 버퍼는 무한하다고 가정.
- (비현실적 가정) 재전송 없음 no retransmission

> 이렇게 비현실적인 가정을 넣었음에도 불구하고 문제는 발생할 수 있다.

### 설명

- capacity R 을 두 개의 링크가 공유하기 때문에 각각 R/2 씩 사용가능
-  λ(in)을 R/2 까지 올리면, 올리는 만큼 그대로  λ(out)도 증가.
-  λ(in) 이 R/2 을 넘어가면 버퍼에 패킷이 쌓이기 시작하며,  λ(out) 속도는 R/2 이상으로 안올라감.
-  λ(in) 의 증가에 따라 delay 는 기하급수적으로 증가하다가 R/2 이 되면 무한이 됨.
  - delay 는 왜 발생하는가?
    1. 라우터의 처리능력은 유한하다.
    2. 만약 router 가 한 패킷을 처리하는데 5초가 걸린다고 하면?
    3. 5초동안 queue 뒤에 패킷이 쌓인다.
    4. 즉, 버퍼에 쌓이는 패킷의 양이 기하급수적으로 증가한다.
    5. 줄이 길어지면, 줄 맨 끝에 있는 패킷의 대기시간은 기하급수적으로 증가한다.

## 2. Congestion 의 원인과 비용: 시나리오 2

### 가정

- 하나의 라우터, 라우터의 버퍼는 유한.

- sender 는 타임아웃된 패킷을 재전송.

  - λ(in): application-layer input
  - λ'(in): transport-layer input (재전송 포함)
  - λ(out): application-layer output 
  - λ(in) = λ(out)
  - λ'(in) >= λ(in)

- (비현실적 가정) Router 에 free buffer 가 있을 때에만 sender 가 보낸다고 가정

  (buffer overflow 로 버려지는 데이터 없다)

  (실제로는 free buffer 가 있는지 확인할 방법이 없으므로 비현실적)

- (비현실적 가정) 패킷이 진짜 loss 되었을 때만 재전송한다.

  전송이 늦어져서 timeout 되는 경우는 없다고 생각.

### 설명

- 이제는 λ(in) 으로 계산하지 않고 λ'(in) 으로 계산한다. (재전송 고려해야 하니까)

- 시나리오1에서는 λ(out) 이 R/2 까지 올라갔었지만,

  이제는 R/3 까지밖에 올라가지 않는다.

  즉, Congestion 이 Throughput 에 영향을 준다.

  - 왜 이런 갭이 생기는가?

    이 갭은 재전송에 사용되었다.

### 가정 (현실적으로 수정)

- duplicates 를 고려하자.

  실제로는 제대로 갔는데, ACK 이 늦어져서 timeout 된 경우 재전송이 일어나 duplicate 가 일어날 수 있다.

  이런 쓸데없는 재전송을 이제부터 고려한다.

### 설명

- Throughput 은 더욱 나빠진다.
  - 기존에는 R/2 까지 올라갔었다.
  - loss 로 인한 재전송을 고려하니 R/3 까지 떨어졌다.
  - premature timeout 으로인한 쓸데없는 재전송까지 고려하니 R/4 까지 떨어진다.
- 물론 end to end delay 도 더욱 나파르게 나빠진다.

### Cost of congestion

- 같은 output 을 내기 위해 더 많은 work(retrans) 가 필요하다.
- 불필요한 재전송때문에 goodput이 감소한다.

## 3. Congestion 의 원인과 비용: 시나리오 3

### 가정

- 4 senders
- multihop path (중요한 요소이다)
- timeout/retransmit (시나리오 2와 동일)

### 의문

Q: λ(in) 과 λ'(in) 이 증가함에 따라 무슨 일이 발생하겠는가?

A:

1. 특정 호스트의 λ'(in) 이 증가함에 따라 그 호스트가 지나가는 라우터의 버퍼가 넘치게된다.
2. 그 라우터를 같이 공유하던 다른 호스트가 패킷 loss 가 생기면서 재전송이 늘어난다.
3. 그 재전송이 또 다른 라우터에 영향을 주게 된다.

### Congestion 으로 인한 또 다른 cost

어느 한 지점의 congestion 으로 인해 만약 패킷이 drop 되면, 

retransmit 이 발생하게 되고, 이 retransmit 때문에 앞의 다른 router 들의

capacity 가 낭비될 가능성이 증가한다.

## 4. Congestion control 에 어떻게 접근해야 하는가

두 가지의 큰 접근법이 있다.

- **end-end congestion control**

  Host 스스로 congestion 을 감지, 판단 한다.

  TCP 가 이 방법을 사용한다.

- **network-assisted congestion control**

  네트워크(라우터들)에서 congestion 이 발생하면 알려준다

  -> 일반적인 라우터들은 이런 기능이 없다.

