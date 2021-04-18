# Link Layer

2계층에 속한다.

peer to peer 연결성을 보장한다.

---

## context

- datagram == frame

- datagram (frame)은 서로 다른 링크로, 그 위의 서로 다른 링크 프로토콜로 전송된다.

  예) Ethernet(wired) 을 첫 링크로 하고 나중에 802.1(wireless)를 마지막 링크로 할 수 있다.

- 각각의 링크 프로토콜은 다른 서비스를 제공한다.

  어떤 거는 재전송 요구,

  어떤 거는 error correction 정보 포함해서 재전송 안해도 됨,

  어떤 거는 깨지면 깨진상태로 그냥 감...

---

## Link layer services

2계층은 크게 2가지가 중요하다.

1. **MAC**
2. **Error correction**

---

- MAC 서버 layer : framing, link access

  - encapsulate datagram into frame, adding header, trailer. (frame에는 헤더와 트레일러가 붙는다.)
  - channel access if shared medium (MAC, 공유된 매체라면 액세스 컨트롤을 해야한다.)
  - MAC addresses used in frame headers to indentify source, dest (소스와 목표를 찾기위해 프레임 헤더에 MAC 주소가 있다.)
    - IP 주소와는 다르다!
      - router는 3계층까지
      - switch는 2계층까지
      - end 단에서는 Application 까지 쭉 올라감.
      - 따라서 2계층에서는 IP 주소 알필요도 없다. 2계층에서는 MAC주소를 사용한다.

- Error Correction 서버 layer : reliable delivery between adjacent nodes

  - 에러가 잘 안나는 링크에서는 거의 사용 안한다. (fiber, some twisted pair)

    참고

    - UTP: 쌈, 비교적 에러 잘 남
    - STP: 비쌈, shield 처리되어서 에러 잘 안남.

  - 무선 링크는 에러율이 높다.

    **왜 2계층에서 peer to peer 전송 보장하는데 4계층의 end to end 전송 보장이 필요?**

    2계층의 peer to peer 보장은 end to end 데이터 안정성 보장까지 이어지지는 않음.

    예) peer to peer 전송은 완벽하게 했다. 그러나 중간에 전송받은 peer가 정전등으로 데이터 다 잃어버리면?

    peer to peer 전송은 완벽하게 했지만 end to end 보장은 되지 않는다.

---

- **error detection**
- **error correction**

이 두가지가

2계층에서 가장 중요하다고 했던 error correction계층, 그리고 MAC 계층 중 error correction 계층에 속한다.

- half-duplex

  주거나 받거나 한번에 둘 중 하나만 할 수 있음.

- full-duplex

  주고 받고 동시에 가능

---

## Where is the link layer implemented?

**중요한 건 "Link layer의 많은 기능들은 NIC에서 HW, Firmware 적으로 구현되어 있다." 라는 것.**

- 모든 각각의 호스트에 link layer 는 적용되어있다.

- link layer는 NIC(network interface card)라는 하드웨어 칩에 구현되어있다.

  예) Ethernet card, 802.11 card, Ethernet chipset...

- NIC는 link, physical layer를 구현한다.

- NIC는 호스트의 시스템 버스에 붙어있다.

- link layer는 하드웨어와 소프트웨어, 펌웨어의 조합이다.

---

## Adaptors communicating

- 보내는 쪽
  - datagram을 frame으로 캡슐화한다.
  - error checking bit 등을 추가한다, flow control (패킷에 헤더와 트레일러가 있다고 했는데 트레일러에 에러체킹비트가 들어간다.)
- 받는 쪽
  - 에러가 있는지 본다, flow control.
  - datagram을 추출하고 (datagram == packet), 받는쪽의 상위 레이어로 보낸다.

---

## Error detection

- EDC = Error Detection and Correction bits

  에러 디텍션은 100퍼센트 완벽하지 않다.

참고

EDC 는 correction 까지 되지만 훨씬 길다.

ED 는 Detection 까지만 된다.

---

## Parity checking

- single bit parity

  끝에 parity bit라는 것을 하나 붙인다.

  하나의 bit 에러를 **감지** 할 수 있다.

  간단해서 좋아보이지만, 2개의 bit만 깨져도 판단이 불가하다.

- two-dimensional bit parity

  2차원으로 parity bit를 붙인다.

  하나의 bit 에러를 **감지** , **correction** 까지 된다.

  얘도 많이 깨지면 감지 제대로 안되지만 single bit 보다 훨씬 낫다.

너무 단순해서 2계층에서는 사용 잘 안한다.

4계층에서 더 많이 사용한다.

---

## Internet checksum

만약 128bit 의 데이터라고 하면,

128bit 를 16bit 씩 쪼개서 각각의 16bit를 하나의 숫자라고 생각하고 서로 다 더한다.

그리고 그 결과의 1의 보수를 checksum으로 붙인다.

마찬가지로 너무 단순해서 2계층에서는 별로 사용안하고 4계층에서 더 많이 사용한다.

---

## Cyclic redundancy check

**CRC: 2계층에서 주로 사용하는 방식이다.**

대충 원리만 이해하면 된다.

1. 데이터를 비트로 본다. (0과 1의 바이너리로)

2. 비트들 뒤에 어떤 비트들 (R 값, r bit 길이)을 더 붙인다. (4에서 붙인 비트들에대해 설명)

3. 그리고 이 비트들을 이어서 하나의 숫자로 본다.

4. 이 때 이 숫자를 G(r+1 bit 길이)라는 숫자로 나누었을 때 나누어 떨어지게 한다.

   즉, 비트들 뒤에 G라는 숫자에 나누어 떨어지도록 비트를 붙였던 것이다.

   **방식:** 보내고자 하는 데이터를 r bits left-shift (쉬프트된 곳은 0으로 채움) 한 값을

   G 값으로 나눈 나머지인 R(r bit) 값을 데이터 뒤에 붙인다.

   이렇게 하면 G 값으로 나누어 떨어지게 된다.

   **여기서 나눈다는 것은 xor 연산 (modulo-2)을 의미한다**

5. G(r+1비트) 값은 bit가 깨졌을때 잘 나누어 떨어지는 숫자로 잘 정해야 한다.

   이 숫자는 그래서 수학자들이 미리 정해놓았다.

CRC는 32bit 를 붙이면 최대 31bit 연속된 error 까지 detection 가능할 정도로 성능이 좋다.

Internet error의 대부분은 burst하게 난다. 따라서 긴 연속 에러까지 디텍션 가능한 CRC가 효과가 좋다.

---

## Multiple access links, protocols

링크의 종류에는 두가지가 있다.

1. point-to-point (독점 사용)
   - PPP for dial-up access (쉽게 말해 전화선)
   - point-to-point link between Ethernet switch, host
2. **boradcast (shared wire or medium)**
   - old-fashioned Ethernet
   - upstream HFC
   - 802.11 wireless LAN

---

## Multiple access protocols

하나의 공유된 broadcast channel 사용할 때, 두개 이상의 동시적인 전송이 노드에 의해 일어날 때 간섭이 일어난다.

- **multiple access protocol**

  - 어떻게 노드들이 채널을 공유하는지 결정하는 **distributed algorithm** (분산 알고리즘)

  - 채널 공유에 대한 커뮤니케이션은 채널 그 차제를 사용해야 한다.

    쉽게말해, 중앙에서 누가 제어해주는게 아니라, 각각의 노드들에서 컨트롤 되어야 한다.

---

## An ideal multiple access protocol

**전제조건:** broadcast channel 은 R bps. (혼자 쓸 때 최대 R의 대역폭을 가진다.)

**이상적인 조건**

1. **최대한 활용:** 하나의 노드가 전송을 원하면, R의 속도로 보낼 수 있어야 한다. (필요할 때는 혼자 써도 Maximum을 사용할 수 있어야 한다.)
2. **공평하게 활용:** M 개의 노드가 전송을 원하면, 각각의 노드들은 평균 값인, R/M 의 속도로 전송해야한다.
3. **스스로 결정:** 완전한 탈중앙화
   - 전체적인 전송을 관장하는 어떠한 특별한 노드도 없어야 한다.
   - clocks, slots 등, 어떠한 싱크로나이제이션도 없어야 한다.
4. **빨리 결정할 수 있어야 한다:** simple

---

## MAC protocols: taxonomy

세가지의 분할 방법이 있다.

- **channel partitioning** (시간, 프리퀀시, 코드, 등을 기준으로 나눠서 사용한다.)

  - 채널을 여러개의 조각들로 나눈다.
    - time slots: delay, 응답시간 관점에서 좋지 않다.
    - frequency: 한명이 사용할때 한 채널의 최대 용량을 다 사용하지 못한다. (최대 R 만큼 사용 불가)
    - code
  - 나눈 조각들을 노드들이 독점 사용하게 한다.

- **random access** (=Random time access = 내가 원하는 시간에 보낸다.)

  - 채널을 분할하지 않고 충돌을 허용한다. (충돌 발생 확률이 높다.) -> 대신 Through put(활용도)는 좋다.
  - 충돌이 발생하면 다시 recover 하는 방식.

- **taking turns** (key를 주고 key 있는 애만 사용가능하게 한다.)

  - 노드들이 서로 key를 주고받으며 key를 가지고 있는 애가 사용할 수 있게 하는 방식.

    보낼게 있는 애들이 양보안하고(내가 키 포기안하고) 원하는 만큼 오래 사용할 수 있다.

  - TDMA (시간 쪼개기) 랑 비슷한거 아님?

    - TDMA 는 시간 똑같이 나눠쓰기이다. 내가 할일 다 못끝냈어도 시간 됐으면 무조건 넘겨줘야한다.

      taking turns는 내가 끝날 때 까지 key 안넘겨주면 된다.

---

## Channel partitioning MAC protocols: TDMA

**TDMA: time division multiple access** (시간 쪼개기)

- 돌아가면서 (round) 채널에 접속한다.
- 각각의 스테이션은 고정된 길이의 슬롯을 매 라운드마다 가진다.
- 사용되지 않는 슬롯은 빈채로 둔다. -> 낭비가 생긴다.

---

## Channel partitioning MAC protocols: FDMA

**FDMA: frequency division multiple access**

- 채널 스펙트럼이 주파수 밴드로 분할된다.
- 각각의 스테이션은 고정된 주파수 밴드를 할당 받는다.
- 사용되지 주파수는 빈 채로 있는다. -> 노는 채널 생김

---

## Random access protocols

- 노드가 보낼 패킷이 있을 때

  - full data rate R을 다 사용할 수 있다.
  - 노드들 사이에서 위계가 없다.

- 두개 이상의 노드들이 동시에 전송하면 충돌(collision) 발생한다.

- **random access MAC protocol** 다음을 구체화 해야한다.

  - 어떻게 충돌을 감지하는가

  - 어떻게 충돌로 부터 회복하는가

    예) 충돌 발생시, 바로 다시 재전송하거나 정해진 시간 이후에 재전송하면 다시 충돌발생할게 뻔함.

    랜덤으로 딜레이시간을 골라서 재 전송한다. -> **Random backoff**

- random access MAC protocol 의 예

  - slotted ALOHA (1980년대)

  - ALOHA (1970년대)

  - **CSMA, SCMA/CD, CSMA/CA**

    이 녀석들 셋이 지금 표준이다.

---

## CSMA (carrier sense multiple access)

**기본 논리: 데이터 보내기 전에 한번 데이터가 다니고 있나 sense 하고 신호 없으면 보낸다. (비어있을때 보내자 전략)**

​	그럼에도 불구하고 collision 발생 가능하다.

​	예) 전파를 쏘고 detect 되기 까지 delay가 있다.

​	그 delay 안에 다른 노드가 전파없다고 판단하고 데이터 쏠 수 있다.

---

## CSMA/CD (collision detectrion)

- **CSMA/CD:** 
  - 짧은 시간에 충돌을 감지
  - 충돌을 감지하자마자 전송 중지해서 채널 낭비를 감소시킴

- 충돌감지
  - 유선 LAN 에서는 쉽다.
  - 무선 LAN 에서는 어렵다.

- 참고
  - CSMA/CD: 충돌감지해서 다시 보낼거임.
  - CSMA/CA: 감지는 못하니까 최대한 피해서 보낼게.

---

## Ethernet CSMA/CD algorithm (충돌감지 한거 어떻게 해결?)

1. **NIC** (1,2 계층의 하드웨어) 가 **네트워크 레이어** 로부터 **datagram** 을 받고 **frame** 을 생성한다.

2. 만약 **NIC** 가 채널이 idle 상태인 것을 감지하면, **frame** 전송을 시작한다. 만약 **NIC** 가 채널이 사용중임을 감지하면, 채널이 idle이 될때까지 기다렸다가 전송한다.

3. 만약 **NIC** 가 **frame** 전체를 다른 전송의 감지 없이(충돌감지 없이) 전송하게 되면 **NIC** 는 **frame** 전송에 성공한것.

4. 만약 **NIC** 가 다른 전송 신호를 **frame** 전송 중에 감지하면, 전송을 중지하고 jam signal 을 보낸다.

5. 중지 후, **NIC** 는 binary (exponential) backoff 상태에 들어간다. (back off 시간을 기하급수적으로 증가시킨다.)

   - m번째 충돌 후, NIC는 0 ~ 2^m-1 사이의 숫자 중에서 랜덤하게 한 숫자를 고른다.

     NIC 는 k*512 bit 시간 (512bit 보내는데 걸리는 시간 기준)을 기다리고, 2단계로 돌아간다.

     추가설명: 겹칠때마다 랜덤 숫자 범위를 증가시킨다. (성능을 위해)

   - 겹칠때(충돌발생 시) 마다 backoff 인터벌이 증가한다. (정확히는 랜덤 숫자 범위가 증가된다.)

---

- CSMA/CD 는 Ethernet (유선) 에서 사용된다.
- CSMA/CA 는 802.11 (무선) 에서 사용된다.

---

broad cast -> 허브 -> 스위치

로 진화했다.

허브도 사실 broad cast 방식이다.

스위치 방식은 table을 가지고 있어서 받을 애 한테 보낸다. (traffic 격리)

---

frame에 source주소, target 주소가 들어간다.

target 주소는 switch 주소가 될 수 없다.

switch 라는 것은 편의상 만든 것이지 사실상 하나의 선으로 연결된것이나 마찬가지이다.

---

