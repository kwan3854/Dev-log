# Network Layer

- forwarding = 데이터 평면: 최적의 경로로 보내는 일 (path은 찾았다 치고)
- routing = 제어평면: 최적의 path를 찾는 일

---

- virtual circuit: connection
- datagram networks: connection-less

---

- segment를 datagram(packet)으로 encapsulate 해서 보낸다.
- 받는 쪽에서는 segment를 transport layer로 보낸다.
- 네트워크 레이어는 모든 호스트와 모든 라우터 안에 있다.
- 라우터는 모든 IP datagram(packet) 안에 있는 헤더(IP 정보)를 분석해서 passing 한다.

---

### Two key network-layer functions

- forwarding: move packets from router's input to appropriate router output
- routing: determine route taken by packets from source to dest.

---

- routing: process of planning trip from source to dest.
- forwarding: process of getting through single interchange.

---

### Interplay between routing and forwarding

라우터 안에서 routing algorithm(routing) 으로 forwarding table을 만든다.

forwarding은 forwarding table을 보고 실행된다.

---

3계층 IP 는 데이터 잘 받았는지 보장하지 않는다.

최선의 경로로 보내줄 뿐이다.

몇 ms 걸려서 도착하는지? 그런거 알 수 없다. 보장도 못한다.

만약 잘 안도착한게 있으면?

4계층에서 segment 번호 확인해서 다시 요청한다.

**핵심은 3계층에서는 이런것들 못한다는 것이다.**

---

3계층은 Connection-less 이다.

3계층은 packet 의 전송을 보장하지는 않는다. 최선을 다해서 보낼 뿐이다. Best-effort 방식

(여기서 보장 못하는 것을 4계층에서 보장해 준다. end to end 연결성)

---

### Datagram forwarding table

- 라우터 안에서는 store and forward

  -> 저장한 다음, 어디로 갈지 결정해서 보낸다.

- 패킷 (datagram)의 헤더에는 target의 IP 주소가 있다.

  그 IP에서 Network 주소를 보고 forwarding table을 판단한다.

  (forwarding table은 수시로 routing algorithm에 의해 갱신된다.)

- 같은 호스트가 패킷을 여러개 보냈어도 모든 패킷들이 똑같은 라우팅 경로로 간다고 할 수는 없다. (대부분 같은 경로로 가긴 한다.)

  왜냐하면 패킷 단위로 (독립적으로) 라우팅 하기 떄문이다.

---

### Datagram forwarding table

| Destination Address Range                                    | Link Interface |
| ------------------------------------------------------------ | -------------- |
| **11001000 00010111 00010000** 00000000 through **11001000 00010111 00010111** 11111111 | 0              |
|                                                              |                |
|                                                              |                |
|                                                              |                |

이런 식으로 IP 주소의 범위를 주고 그 범위 안에 있으면 특정 번호로 링크 하라는 테이블로 되어있다.

마지막 바이트 (호스트주소)는 모든 호스트들을 포함한다는 뜻이다.

만약 범위가 이렇게 깔끔하게 안 구분된다면?

---

### Longest prefix matching

별표로 표시되지 않은 애 중에 가장 길게 매칭되는데로 가라. (너무 당연한 얘기)

(책에는 별표인데 여기는 물음표로 적음)

| Destination Address Range           | Link interface |
| ----------------------------------- | -------------- |
| 11001000 00010111 00010??? ???????? | 0              |
| 11001000 00010111 00011000 ???????? | 1              |
| 11001000 00010111 00011??? ???????? | 2              |
| otherwise                           | 3              |

예를들어

- 11001000 00010111 00010110 10100001

  은 0으로 매핑된다.

- 11001000 00010111 00011000 10101010

  은 1로 매핑된다.

  2가 아닌 이유는 1이 더 길게 일치하기 때문이다.

---

### Datagram vs VC network

> vc 네트워크에 대해서는 알 필요 없다.

- Internet (datagram, packet)

  다양한 매체를 섞어서 사용할 수 있다. 무선, 유선, 케이블...

  - 컴퓨터 끼리의 데이터 교환

  - 다양한 매체 종류

  - smart 한 end system 을 쓴다. (end system이 컴퓨터니까.)

    -> 네트워크 안에서는 전송을 단순하게 한다.

    ​	edge, 즉 end 단의 컴퓨터에서 복잡하게 처리한다. (smart 하니까)

    ​	중간에서는 1~3 계층만 거치는데

    ​	end단에서는 1~7 계층까지 간다.

    ​	4계층에서 신뢰성 검사를 한다.

---

## Router architecture overview

ppt 자료의 그림 참고

- forwarding table은 앞쪽, 데이터가 들어오는 쪽에 있어야 한다.

  -> forwarding table 보고 어디로 갈지 결정 한 후 해당 output으로 보내야 하니까. (packet 마다 따로따로 라우팅됨)

  -> forwarding table이 routing processor에 의해 만들어진 후 input port에 넣어진다.

- routing, management control plane (software) -> 제어 평면

- forwarding data plane (hardware) -> 데이터 평면

---

- 들어올때 (input port)

  - 1계층 -> 2계층 -> 3계층

  - line termination -> link layer protocol (receive) -> lookup, forwarding, queueing

    queueing 되다가 패킷 버려질 수도 있음

    기본적으로는 패킷(datagram)이 FIFO로 처리되지만, 급한애는 먼저 보내준다. (priority scheduling)

- 나갈때 (output port)

  - 반대로 3->2->1 계층으로 빠져나감

---

### Switching fabrics

- memory: 요즘 안씀
- bus: 공통 차선을 공유한다-> 속도 한계가 있다.
- crossbar: bus share 하지 않는다.

---

### Input port queuing

- **Head-of-the-line(HOL) blocking:** 앞에 큐 되어있는애가 뒤 큐에 있는애들이 나가는걸 막는 것. (동맥경화)

  둘이 동시에 못 가니까 한놈이 기다렸다가 가야됨

  그래서 Input port 에서도 queuing 이 생길 수 있는 것이다.

---

### 정리

- **3계층의 핵심은 길찾기다**

- routing protocols (제어평면): 길찾기 알고리즘
- IP protocol: 길 찾기를 제대로 하기 위한 프로토콜
- ICMP protocol: IP 프로토콜에 문제 생겼을 때 작동하는 프로토콜이다.

---

## IP datagram(packet) format

- Header 부분(20byte) + 4계층에서 내려온 segment

  다시 그 segment는 TCP or UDP 의 header 부분(20byte) + Appdata

ppt 그림 참고 (7주 1회차)

---

### IP fragmentation, reassembly

physical layer의 특성에 따라 datagram의 전체 사이즈 제한이 있음.

여러 physical layer를 섞어 쓴다면, 그 제한에 걸릴 수 있음.

만약 사이즈가 너무 크다면, 사이즈 제한에 걸린 시점에 여러개로 쪼개서 보내게됨.

주의할 점은 여러개로 쪼갠 것을 다시 중간에 합치지 않는 다는 것.

마지막에 host에 가서 다시 합쳐짐.

아까도 말했지만 end 단이 smart하기 때문에 smart한 end단 Host 한테 이런 복잡한 일을 맡기고

중간의 router 들은 간단한 일만 하는것.

---

| -    | length | ID   | fragflag | offset | -    |
| ---- | ------ | ---- | -------- | ------ | ---- |
| -    | =4000  | =x   | =0       | =0     | -    |

- 4000 byte datagram

- MTU (maximum transfer unit) = 1500 bytes

  2계층 frame의 Payload의 크기를 MTU 라고 함

  Payload = IP datagram = MTU

| -    | length | ID   | fragflag | offset | -    |
| ---- | ------ | ---- | -------- | ------ | ---- |
|      | =1500  | =x   | =1       | =0     |      |

| -    | length | ID   | fragflag | offset | -    |
| ---- | ------ | ---- | -------- | ------ | ---- |
|      | =1500  | =x   | =1       | =185   |      |

| -    | length | ID   | fragflag | offset | -    |
| ---- | ------ | ---- | -------- | ------ | ---- |
|      | =1040  | =x   | =1       | =370   |      |

- length: 헤더의 크기 20을 고려해라.

  3980 을 1480+1480+1020 으로 나눈것.

- ID: 나눈것들 모두 같은 ID를 사용한다.

- fragflag:

  - 1: 나 단편화 된건데 뒤에 올거 더 있어 내가 마지막 조각이 아니야.
  - 0: 내가 마지막 조각이야.

- offset: 쪼개서 보냈을 때 꼭 순서대로 도착한다는 보장이 없음. offset 보고 순서대로 재조합. 작을수록 앞의 것

  offset은 8byte 단위.

---

## IP addressing: introduction

만약 PC 한 대에 NIC 2개 달면?

-> IP 주소 두개, MAC 주소 2개.

-> NIC 마다 IP 주소 하나씩 주어짐.

-> Router 는 엄청 많은 In, Out 있는네, 거기에 각각 하나씩 NIC 있음.

---

### Subnets

분리해서 만들어지는 고립된 Network

NIC를 router로부터 분리했을 때 고립된 네트워크

Subnet 안에는 Router가 없음.

한 subnet 안에서는 같은 네트워크 주소 사용.

라우터 끼리만 연결된 (중간에 컴퓨터 없는) 것도 subnet 임.

---

### IP addressing: CIDR

만약 어떤 기관에 Class A 또는 B 줬는데 그 호스트 개수 풀로 사용 못하고 낭비하고 있다면?

-> 요즘에는 IP 분배시 class로 분배하지 않음. 최근에는 CIDR 사용

- CIDR: Class InterDomain Routing
  - a.b.c.d/x

만약

200.23.16.0/23 이면

23비트까지 subnet 주소라는 뜻

---

### IP addresses: how to get one?

- DHCP: Dynamic Host Configuration Protocol:

  dynamically get address from as server

  plug and play

  -> 스마트폰 wifi 연결하는데 내 IP 뭔지 신경쓰나?

  신경안쓴다. 자동으로 할당됨.

---

### DHCP: dynamic Host Configuration Protocol

DHCP 의 동작 과정보다는 DHCP의 역할을 아는 것이 중요하다.

-> Network 주소를 알려주고, 사용가능한 Host 주소 배정해서 알려주는 역할. (IP 주소를 만들어주는 역할)

----

만약 Host가 subnet에 처음 들어가면 자신의 IP 주소를 모를 것이다.

subnet 안의 DHCP 서버에 host가 요청을 하면,

DHCP서버는 IP를 만들어서 보내준다.

DHCP 입장에서 subnet 주소는 자신이 거기 속해있으니까 알고있고, 사용 가능한 Host 주소를 붙여서 주는 것.

---

과정

1. Host: (broadcast) DHCP 서버 있음?
2. DHCP: (broadcast) 내가 DHCP 서버이고, 여기 네가 쓸 수 있는 IP 주소야.
3. Host: (broadcast) 알았어 그 주소 쓸게
4. DHCP: (broadcast) 알았어 너 그거 써.

---

### DHCP: 사실 그냥 IP 주소 주는 것 이상 하는 일이 있다.

- first-hop router (gateway)의 주소를 client에게 준다.
- DNS 서버의 주소와 이름을 준다.
- network mask (subnet mask) 제공. (내가 네트워크 설정 수동으로 안해줘도 알아서 준다.)

---

### ISP 가 할당받은 IP 분할해서 기관들에게 주는 법

ISP's block	11001000 00010111 0001/0000 00000000	200.23.16.0/20

기관 0	11001000 00010111 0001/000/0 00000000	200.23.16.0/23

기관 1	11001000 00010111 0001/001/0 00000000	200.23.18.0/23

기관 3	11001000 00010111 0001/010/0 00000000	200.23.20.0/23

...

기관 7	11001000 00010111 0001/111/0 00000000	200.23.30.0/23

---

### Hierarchical addressing: route aggregation

만약 ISP1에 속해 있는 서울캠 IP를 ISP2에 속해 있는 대전캠에서도 그대로 쓰고 싶다면?

-> 원래는 네트워크 옮기면 당연히 IP 변한다.

더 구체적인 routing을 해서 특정 IP를 다르게 라우팅 해줄 수 있다.

---

### NAT: network address translation

IPv4가 IP 개수 부족하니까 사용.

공인 IP 주소 하나만 있어도 내부네트워크 구성하면 여러 호스트 커버할 수 있음.

공적인 IP 하나만 내세우고 내부에서는 사설 IP를 사용하는 방법

외부에서는 사설 IP 전혀 모름.

---

**NAT translation table** 을 사용.

1. 내부에서 외부로 먼저 보내면 table에 기록을 남김
2. 외부에서 내부로 다시 보내주면 table 보고 변환함.

근데 외부에서 먼저 오면 누구한테 가는건지 어떻게 알지?

해결법: port forwarding

특정 포트는 특정 포트로 무조건 포워딩 되게 미리 적어놓음.

---

### ICMP: internet control message protocol

> 중간에 라우터에서 문제가 발생했을 때 (예를들어 TTL 0 됐을 때)
>
> 소스 IP를 보고 소스한테 알려줌 (나 TTL 0 되서 drop 했다~)

1. 패킷을 하나 따로 만든다
2. 앞에 source IP, Target IP 넣고, segment 들어가는 자리에 ICMP를 넣는다.

---

### Traceroute and ICMP

예전에 배웠던 delay 구하는 것 어떻게 구현하는 것인가?

1. 처음에 TTL 1 로 보냄 -> TTL 0 되서 ICMP 메세지 돌아옴 -> 그걸로 delay 계산

2. TTL 2 로 보냄 -> TTL 0 되서 ICMP 메세지 돌아옴 -> 그걸로 delay 계산

3. TTL 3 로 보냄 -> TTL 0 되서 ICMP 메세지 돌아옴 -> 그걸로 delay 계산

4. 반복

5. 마지막 Host에 도착하면 ICMP 안보내줌.

   Host까지의 delay는 어떻게 구하냐?

6. 마지막에는 port 번호에 이상한 값 써서 보냄 -> Host 한테도 ICMP 받음.

---

## IPv6

터널링 기법만 알면됨

### Tunneling

- IPv6로 가다가 IPv4가 중간에 껴 있으면?

  IPv6 패킷자체를 IPv6 헤더로 감싸버린다.

  IPv4 패킷 = 20byte 헤더 + segment

  segment 자리에 segment 대신 IPv6 패킷을 넣는다.

- 받은 datagram 안에 IPv6 datagram 들어있는지 어떻게 알고 껍질 벗기는가?

  (그냥 평범한 IPv4 일수도 있는데?)

  -> IPv4 패킷의 header 부분에 upper layer 라는 부분 있는데 거기에 상위 계층 표시가능.

  그 값을 TCP나 UDP 를 나타내는 값 말고 특별한 값으로 변경해 높음.

- 만약 A~F 까지 라우터 있는데 C,D 만 IPv4 이고 나머지는 IPv6 이면?

  - B가 껍질 싸서 C로 보냄
  - C-D는 같으니까 알아서 됨
  - D로부터 E가 받으면 헤더에 upper layer 보고 껍질 깜.

  즉, B~E가 IPv4 패킷을 사용하게 됨.

