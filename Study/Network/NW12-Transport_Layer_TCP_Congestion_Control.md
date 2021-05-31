# Transport Layer - TCP Congestion Control

Sender 쪽 TCP 에서는

- **rwnd:** flow control 을 위해
- **cwnd:** congestion control 을 위해

를 가지고 있다. (둘 중 작은 값으로 크기를 정해서 보낸다)

## 1. Addictive Increase Multiplicative Decrease (AIMD)

### 접근법

sender 는 transmission rate 를 loss 가 발생할 때 까지 증가시킨다.

- **addictive increase:** cwnd 를 loss가 감지될 때 까지 매번 1MSS 씩 증가시킨다.
- **multiplicative decrease:** cwnd를 loss 이후에 절반으로 깎는다.

### 디테일

- sender 는 transmission 을 제한한다.

  `LastByteSent - LastByteAcked <= cwnd`

- TCP sending rate:

  1. cwnd byte 를 보낸다.
  2. RTT 동안 ACKs 을 기다린다.
  3. 더 많은 byte 를 보낸다.

  `rate = cwnd/RTT (bytes/sec)`

- 간단 요약

  - traffic 이 증가하면 cwnd 를 줄인다.
  - traffic 이 감소하면 cwnd 를 늘린다.

## 2. TCP Slow Start

처음에 connection 이 시작될 때는, linear 하게 증가시키는게 아니라, exponential 하게 증가시킨다.

예) 1 -> 2 -> 4 -> 8 -> 16 ...

이 기하급수적인 증가는 ssthreshold 까지 지속되거나,

아니면 첫 loss 가 발생할 때 까지 지속된다.

-  처음의 cwnd = 1 MSS

- 매 RTT 마다 cwnd 가 두배가 된다.

- 어떻게 매 RTT 마다 두배로 만드냐?

  -> ACK 을 1개 받을 때 마다 cwnd 를 증가시키면 된다.

### 요약

처음에는 느리지만 기하급수적으로 증가하기 떄문에 엄청나게 빨라진다.

그래서 이름이 slow start.

## 3. TCP: detecting, reacting to loss

### 타임아웃이 되어서 loss 가 감지 되었을 때

- **cwnd** 를 1 MSS 로 설정한다.

- window 의 크기를 기하급수적으로 증가시킨다(마치 slow start 처럼).

  ssthreshold 까지 증가시키고나서, linear 하게 증가시킨다.

### 3 duplicate ACKs 로 loss 가 감지 되었을 때: TCP RENO

- 일단 ACK 이 왔다는 것은 네트워크가 segment 를 보낼 여력이 있다는 것이다.

  즉, timeout 보다는 상황이 낫다고 판단.

- **cwnd** 를 절반으로 줄인다. 그리고 linear 하게 증가시킨다.

### TCP Tahoe 

TCP Tahoe 는 항상 cwnd 를 1MSS 로 설정

3dup acks 와 time out 을 구분해서 반응하지 않는다.

> TCP 는 loss 에 대해 어떻게 하라는 구체적인 규격이 정해져 있지 않아서, 구현마다 그 구현방식이 다르다.

### ssthreshold (Slow Start Threshold)

- 가변적으로 변한다.

- loss 가 감지되었을 때, ssrhreshold는

  loss가 발생하기 직전 cwnd 크기의 절반으로 설정된다.

## 4. 요약

### TCP Congestion Control

- Timeout 이면 무조건 slow start
- dup 3 ACKs 이면 무조건 fast recovery

