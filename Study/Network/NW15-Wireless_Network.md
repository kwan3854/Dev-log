# Wireless Network 무선 이동 네트워크

## 1. CSMA/CA (Carrier Sense Multiple Access/Collision Avoidance)

- Carrier sense 매커니즘을 통해 채널 액세스 함
- Robustness 와 scalability 제공
- 노드 밀도가 증가하면, collision 일어날 확률 증가

---

- Data 를 보내는 경우

  DIFS 만큼 기다린다.

- Control data 를 보내는 경우

  SIFS 만큼 기다린다.

  컨트롤 패킷이 우선이니까 DIFS 보다 더 짧은 시간 기다림.

## 2. CSMA/CA 의 문제

- **Hidden terminal problem**

  - A와 B의 통신이 C의 감지 범위 밖에서 일어난다. (A가 B에게 데이터 전송)

  - C는 아무 통신이 없다고 판단하고 (carrier sense 결과) B와 통신을 시도 (C가 B에게 데이터 전송)
  - B는 이미 A와 통신중이었으므로 **충돌 방생**

- **Exposed terminal problem**

  - A와 B가 통신 중이다. (B가 A에게 데이터 전송)

  - A와 B의 통신 데이터가 C에게까지 닿고 있다.

  - C는 현재 D와 통신하고 싶으나, A와 B의 통신이 carrier sense 결과 감지되어서

    하지 못하고 있다.

  - 그러나 사실, A와 B의 통신은 C와 D의 통신를 방해하는 범위에는 실제로 들어있지 않는다.

    사실은, **C와 D는 통신을 해도 되었다.**

- **에너지 낭비**

  - 계속해서 carrier 가 비어있는지 carrier sense 를해야 하므로, 에너지가 낭비되고 있다.

## 3. CSMA/CA 의 문제 해결

### RTS, CTS:

Hidden, exposed terminal 문제를 해결하기 위해 도입

- **RTS (Request To Send) Packet:**

  "내가 너한테 데이터를 보내도 되니?"

- **CTS (Clear To Send) Packet:**

  "그래 보내도 돼"

- 다음의 일련의 과정을 통해 통신이 이루어지게 한다.

  1. Source: DIFS 만큼 대기, 그래도 carrier 비어있다면,
  2. Source: RTS 패킷을 보냄
  3. Destination: SIFS 만틈 대기
  4. Destination: CTS 패킷을 보냄
  5. Source: SIFS 만큼 대기
  6. Source: Data 보냄
  7. Destination: SIFS 만큼 대기
  8. Destination: ACK 보냄

- 항상 DIFS 만큼 대기하는 것으로 시작, 중간은 SIFS 만큼 대기.

- **Hidden terminal problem 해결**

  - A와 B의 통신을 C가 감지 못하는것이 원인이었음. (A가 보낸 데이터를 감지 못하므로)
  - B가 A의 데이터(RTS)에 대한 CTS 를 보낸다.
  - C는 B가 보낸 CTS를 감지하고 A와 B가 통신중이라는 것을 알게 됨
  - C는 데이터를 보내지 않고, 충돌이 발생하지 않음

- **Exposed terminal problem 해결**

  - A와 B의 통신이 사실 C와 D의 통신과 충돌되지 않지만, (B가 데이터를 보낼때)

    감지되어서 C와 D가 통신하지 못하는것이 문제였음

  - B가 RTS를 A에게 보낸다. 이것이 C에게도 감지된다.

  - A가 CTS를 B에게 보낸다. C는 A의 CTS는 감지하지 못한다.

  - C는 A가 고장났거나, 자신과 충돌하지 않을정도로 멀리 있다고 판단한다.

  - C는 D와 통신해도 충돌하지 않는다고 판단하고 통신을 마음껏 할 수 있다.

- 주의: 여전히 RTS 패킷은 충돌할 수 있다.

  충돌을 exponential backoff 을 이용해 최대한 피하려고 노력한다.

- RTS 패킷은 Data 보다 훨씬 크기가 작으므로, 충돌이 나도 Data가 충돌하는 것 보다는 상황이 낫다.

### NAV (Network Allocation Vector):

에너지 낭비를 해결하기 위해 도입

- Virtual timer 라고 생각하면 된다.
- RTS, CTS 를 보낼 때, NAV 를 포함해서 보낸다.
- NAV는 "앞으로 X msec 만큼 내가 사용할거야" 라고 알려주는 것
- NAV 를 감지한 다른 노드들은 그동안 carrier sense 를 계속하지 않고 기다린다.
- 계속해서 채널 감지를 하지 않으므로 에너지를 절약할 수 있다. 

---

