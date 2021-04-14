# Introduction (소개)

전체적인 배경지식들을 자잘 자잘하게 설명한다.

---

**간단한 용어정리**

- **Internet 이란**

  네트워크의 네트워크이다.

- **Protocols**

  약속된 규약이다.

  TCP, IP, HTTP, 802.11 등등

- **network edge**

  edge란 end user 들의 디바이스라고 생각하면 된다.

  host: clients and servers

- **access networks, physical media**

  무선, 유선 커뮤니케이션 링크

- **network core**

  interconnected routers, network of networks

- **gateway**

  edge에 있는 애가 인터넷에 접속하기위해 처음 접속하는 라우터.

- **shared**

  여럿이 공유한다

- **dedicated**

  나만 쓴다.

---

### Access net: digital subscriber line (DSL)

전화선을 생각해라. 얘전에는 전화선으로 인터넷을 하던 시대가 있었다.

기존에 있는 전화선을 이용한다.

업스트림과 다운스트림 속도가 비대칭이다. 다운스트림이 훨씬 빠르다.

---

### Access net: cable network

ISP 라우터에 케이블 등이 연결된 네트워크이다.

DSL 과는 다르게 전용(dedicated access)선을 쓴다.

Shared cable에 tv나 데이터 등이 다른 프리퀀시로 전송된다.

---

### Access net: home network

하나의 라우터에 (요즘은 라우터와 wireless access 장치가 하나로 된 경우가 많다.) 컴퓨터, 스마트 디바이스, tv 등이 모두 연결된다.

---

### Wireless access networks

- wireless LANs

  와이파이를 생각하면 된다.

  802.11b/g 이런식으로 표현한다.

- wide-area wireless access (WAN)

  휴대전화의 무선연결을 생각하면 된다.

  텔레콤 회사에서 제공된다. 10km 정도의 거리

  3g,4g,lte 등등

---

### Host: sends packets of data

데이터를 작은 덩어리로 나누어서 전송한다.

이를 packet 이라고 부른다.

한 패킷의 길이를 L bits 라고 하자.

transission rate R (초당 보내는 양)

R = bandwidth = capacity 라고 생각하자.

`패킷 전송 딜레이 = L-bit 패킷을 링크에 보내는데 필요한 시간 = L/R` 이다.

---

### Physical media

쉽게 생각해서 무선과 유선 전송이 있다고 생각하자.

- 유선 : coaxial cable, fiber optic cable ...
- 무선: radio link 즉, LAN, 3g, 위성통신 등등이 있다.

유선을 guided media (solid media)라고 하고

무선을 unguided media 라고 하다. (사방팔방 뿌린다)

---

### Packet-switching: store-and-forward

**L-bit** 길이의 패킷을 **R bps**의 링크에 전송하는데 **L/R 초**가 걸린다.

end-end delay 는 2L/R 가 된다. (다른 딜레이는 다 무시했을때)

여기서 말하는 딜레이는 store and forward 하는데 걸리는 시간만 따진 것이다. 다른 수많은 요인들을 고려하면 훨씬 긴 딜레이가 생긴다.

---

### Packet Switching: queueing delay, loss

패킷을 받을 때 queueing 을 하는데, 줄서서 링크에 전송되기를 기다리게 된다.

그런데 줄이 너무 길어져서 꽉찬 queue 에 패킷이 들어오면, 패킷의 손실이 생길 수 있다.

마치 고속도로 톨게이트에서 차들이 기다리는 것을 생각하면 된다.

---

### Two key network-core fuctions

Router 가 하는 일이 뭐냐?

1. routing - routing algorithm 으로 패킷의 소스 투 데스티네이션 루트를 정한다. (routing table을 라우팅 알고리즘으로 작성한다.)
2. forwading - 인풋된 패킷을 routing table을 보고 적절한 라우터 아웃풋으로 보낸다.

routing table에 대해 추후 자세히 배운다. (중요하다)

---

### Alternative core: circuit switching

- circuit switch: 예약방식 (많이 놀게 됨)
- packet switch: burst 도 잘 처리 가능

데이터가 쭉 이어져서가면 예약방식이다. 데이터가 비슷한 양이 계속 지속적으로 가면 괜찮지만,  burst 하게 데이터가 가면 노는 시간이 많아짐.

실제데이터는 대부분 burst 하게 이동한다. (영상, 오디오등이 이어져서 가는 데이터 형식의 예이다.)

dedicated resources (no sharing), 전용 선을 사용한다.

그래서 circuit swithing은 전통적인 전화 연결에 많이 쓰인다.

- FDM (Frequency Division Multiplexing): 한 케이블을 주파수를 나눠서 사용해서 동시에 여러 이용이 가능하다.
- TDM (Time Division Multiplexing): 한번에 하나씩 사용하는데 시간을 쪼개서 사용하는 방식이다.

---

### Packet switching VS Circuit switching

패킷 스위칭이 더 많은 유저들이 네트워크를 이용할 수 있게 한다.

**그렇다면 패킷이 무조건 더 좋은거냐?**

버스트한 데이터에는 엄청 좋다.

패킷 로스가 있을 수 있다. (congestion 으로 인한) (너무나 당연한 얘기)

---

### Internet structure: network of networks

수많은 ISP (Internet Service Providers) 가 있는데 얘들을 서로 어떻게 연결하냐?

- 서로서로 직접 연결? -> 불가능 O(N^2) 커넥션 필요.

- 글로벌 ISP?

  글로벌 ISP 가 괜찮은 비지니스라면 하나만 생기겠나? 여러개 생긴다.

- 만약 글로벌 ISP 가 여러개 생기면?

  중간에 IXP(Internet exchange point) 를 둬서 글로벌 ISP 끼리 연결.

- 그리고 지역 네트워크도 생길 수도 있다.

  미국같이 땅이 넓은 곳은 지역네트워크가 있음. 한국은 잘 없음.

- Content provider networks

  Google, Microsoft 같은 기업들이 자기들만의 네트워크를 서비스를 위해 구축할 수도 있다.

- Tier 1 ISP

  적은 수가 있고, 잘 연결된 아주 큰 네트워크이다.

  예) Sprint, AT&T, NTT 등, 국가, 국제 단위 커버리지.

- Content provider network

  예) Google

---

### How do loss and delay occur?

라우터 버퍼 안에 패킷 큐가 있다.

패킷이 도착하면 임시로 큐에 들어간다. 만약 큐에 들어가는 패킷이 나오는 패킷보다 많아서 (들어오는 속도가 나가는 속도보다 빨라서) 계속 큐에 패킷이 쌓이다가, 큐를 꽉채우고 넘어가게 되면, loss 가 발생한다.

추가적으로 설명하면, 데이터는 burst 하게 들어오는 경우가 많다. 그렇기 때문에, 데이터의 양을 평균으로 내서 생각하고 그 용량으로 설계하게 되면 안된다.

---

### Fout sources of packet delay

**d (nodal) = d(proc) + d(queue) + d(trans) = d(prop)**

- **d (proc)** : nodal processing (router를 node 로 봤을 때)
  - check bit error (깨진게 있나 확인, OSI7의 Link layer에서 한다.)
  - determine output link (어디로 보낼지 결정)
  - typically < msec (보통 수 밀리초 이내에 이루어진다.)
- **d (queue)** : queueing delay (대기시간)
  - time waiting at output link for transmission (큐에서 전송까지 대기시간)
  - depends on congestion level of router (라우터에서 체증이 얼마나 일어나냐에 따라 결정됨)
- **d (trans)** : transmission delay (한 패킷을 첫비트부터 마지막 비트까지 전송하는데 걸리는 시간)
  - L: packet length (bits)
  - R: link bandwidth (bps)
  - d(trans) = L/R
- **d (prop)** : propagation delay (전파되는데 걸리는 시간, 매체의 길이(거리)와 메체의 특성으로 결정된다.)
  - d: length of physical link
  - s: propagation speed in medium (~2x10^8 m/sec) -> 매체 마다 값이 다르다. 동축, 구리선, 광, 무선 등등
  - d(prop) = d/s

- **주의!!**

  d(trans) 와 d(prop) 은 매우 다른 것이다.

  transmission delay 는 대역폭과 패킷의 크기로 결정되고

  propagation delay 는 매체의 길이와 매체의 특성으로 결정된다.

---

### Queueing delay (revisited)

- R: link bandwidth (bps)
- L: packet length (bits)
- a: average packet arrival rate

즉, 초당 axL 비트만큼 들어온다는 이야기 이고, 나가는 속도가 R 이라는 의미이다.

- La/R ~0: avg. queueing delay **small**
- La/R _> 1: avg. queueing delay **large**
- La/R > 1: more "work" arriving than can be serviced, average delay **infinite**

La = R 이 되기만 해도 delay 는 개 높다.

왜냐하면 평균값이 같다는 것이지 packet은 burst하게 오기 때문이다.

---

### "Real" Internet delays and routes

**실제 인터넷 딜레이와 로스는 어떤가?**

traceroute program 이라는 게 있다.

만나는 라우터 마다, 패킷 받을 때 마다, probe packet 을 돌려보내서 시간을 계산한다.

이것을 이용해 딜레이를 계산한다.

---

### Packet loss

큐 (버퍼) 의 사이즈는 유한하다.

큐가 꽉차면 패킷을 버린다. (로스가 발생한다.)

로스된 패킷은 이전 node 로 재전송 될 수도 있다. (link layer, 2계층에서 하는 일)

---

### Throughput (처리율)

대역폭과 비슷한데 end to end 에서의 이야기이다.

- Throughput: sender 와 receiver 사이에서의 rate(bits/time unit)

bottleneck link 가 생길 수 있다.

더 대역폭이 작은애 (성능을 제한하는 애) 기준으로 성능이 결정된다.

Rs 에서 Rc 로 간다고 했을 때,

- Rs < Rc 이면 더 작은 쪽인 Rs 가 보틀넥이다. 얘 기준으로 성능이 제한된다.

- Rs > Rc 이면 Rc가 보틀넥이다. 근데 깊게 따지고 보면 여기가 상황이 더 심각하다.

  Rc의 성능에 반도 안나올 수 있는데, 중간중간 drop되는 패킷이 생기기 때문이다.

**보통은 중간에 있는 core 들은 대역폭이 엄청나게 크기 때문에 끝쪽에 있는 link 들이 보틀넥이다.**

---

IP 주소는 컴퓨터 당 1개이다. 즉 컴퓨터를 구분하는 용도이다.

port 번호는 app을 구분하는 용도이다. -> 4계층에서 활용해서 어떤 응용프로그램인지 판단한다.