# Routing Algorithms

## 라우팅과 포워딩의 인터플레이

1. **라우팅 알고리즘** 은 네트워크를 통해 **end to end path** 를 결정한다.
2. **포워딩 테이블** 은 현재 라우터안에서의 **로컬 포워딩** 을 결정한다.

각 라우터는 각 라우터 쌍에 대해서 `(자신, dest) ` Shortest path를 알아야 한다.

> 결국 routing algorithm 이란,
>
> Single source, all destination의 shortest path 를 구하는 알고리즘.

---

라우터 간의 연결 cost는 낮을수록 좋다. (cost 와 bandwidth 는 반비례 관계)

cost가 크다는 것은 congestion 이 많이 일어난다는 뜻이다.

Shortest path 알고리즘이란, 가장 낮은 Cost의 경로를 찾는 것이다.

---

## 1. Routing algorithm classification

### global

- Vertex 와 각 edge의 cost 정보 모두 알고 있다. 그걸로 path를 찾는 방법.

  = **"link state"** algorithms

### decentralized

- 자신과 인접한 Vertex, edge cost 에 대해서만 앎.

- 정보의 일부만 가지고 동작하게 됨.

- 여러번의 연산과, 다른 노드와의 협력으로 결국 shortest path 찾아냄

  = **"distatnce vector"** algorithms

---

## 2. A Link-State Routing Algorithm

**Dijkstra's algorithm** 사용. (Bellman-ford 방식으로도 구현 가능)

### Dijkstra

- **시간복잡도: O(N^2)** (일반적인 경우의 복잡도)

  (Vertex의 갯수의 제곱만큼의 시간 복잡도)

  (가장 효율적으로 구현했을때의 복잡도는 O(nlogn))

### Bellman-ford

- **시간복잡도: O(N*E)**

  (Vertex*Edge 만큼의 시간 복잡도)

---

### Dijkstra 알고리즘: oscillation 문제

만약 **link cost** 가 **트래픽의 양** 과 같다고 가정하면, (즉, cost 상정시 트래픽 양만 고려하면,)

라우팅이 계속 왔다 갔다 하는 문제가 생김

---

## 3. Distance vector algorithm

나와 인접한 것과의 정보(일부의 정보)만 가지고 있음.

### Bellman-Ford equation (dynamic programming)

>  *주의* 
>
> Bellman-Ford 알고리즘을 쓴다는 의미가 아니라, (Bellman-Ford 알고리즘은 모든 정보가 있어야 사용 가능)
>
> node-node 거리 계산에 Bellman-Ford 공식을 사용한다는 뜻.

여러개로 분할해서 연산한 다음, 나중에 합쳐서 결과값을 도출하는 방식이다.

`x to y 거리 = {x to v 의 거리 + v to y 의 거리} 의 최소값`

`dx(y) = min {c(x,v) + dv(y)}`

`dv(y)` 는 또 다시 `min {c(x,v) + dv(y)}` 로 나눠지고, 또 dv(y)가 나눠지고...

반복하다가 마지막 y까지 다 나눠지면, 거기서부터 다시 식이 합쳐지면서 연산완료.

**즉, 다른 노드들의 협력 필요**

### Dx(y)

Dx(y) 의 값은 항상 정확한 값이 아니라, 일단 추측값을 넣어놓고, 나중에 정보들이 오면 업데이트한다.

각 노드들은 각각 single source all destination 값을 가진 테이블을 가지고 있어야 한다.

그 테이블안의 값은 처음부터 완벽한 값이 아니라, 시간이 지남에 따라 완벽해진다.

### Distance vector: link cost changes

- good news:

  링크 cost가 줄어들었을때, 금방 그 정보가 업데이트 됨.

- bad news:

  링크 cost가 늘어났을때, 정보가 정말 느리게 업데이트 된다.

  (책 p.356, [유튜브 영상](https://youtu.be/_lAJyA70Z-o) 참고) (routing loop, count to infinit problem)

---

## 4. LS 와 DV 비교

- LS (link state):

  oscillation 발생 가능

- DV (distance vector):

  routing loop 발생 가능

---

## 5. Hierarchical routing

여태까지 봤던 link state, distance vector 방식은 이상적인 이야기였다.

두 방식 모두 edge와 node의 개수가 적을 때만 잘 동작한다.

- 실제 인터넷은 엄청나게 규모가 커서 노드의 개수, 즉 dest의 개수가 엄청나게 많아진다.

  모든 dest 정보를 routing table에 저장할 수 없게 된다.

- 어떤 라우터는 인증되지 않은 접속을 막아버린다.

  어떤 네트워크의 admin은 자신의 네트워크에서의 라우팅을 컨트롤하고 싶어할 수 있다.

### AS (Autonomous Systems)

- 인접한 지역의 라우터들의 집합.
- 같은 AS 안에서는 같은 라우팅 프로토콜 사용
  - 이 라우팅 프로토콜을 **instra-AS routing protocol** 이라고 함
  - 다른 AS에서는 다른 intra-AS routing protocol을 사용할 수도 있다.

### gateway router

AS 안에서 다른 AS로 연결되는 라우터를 **gateway router** 라고 한다.

(외부의 AS와 연결되는 router)

Gateway router 끼리는 각 AS가 어떤 네트워크를 커버하는지에 대한 정보를 공유해야 한다.

### Forwarding table

Interconnected AS들 사이에서 Forwarding table은

1. intra-AS routing algorithm
2. inter-AS routing algorithm

으로 만들어진다.

### Inter-AS tasks

자신의 AS 외부를 목표지로 datagram을 전송하게 되면?

-> 라우터는 패킷을 gateway router로 forwarding 해야 한다.

-> 그런데 수많은 gateway router들 중, 어느 gateway router로 보내야 하는가?

**AS는 이렇게 해야 한다.**

1. eBGP (external)

   자신과 인접한 다른 AS들이 어떤 네트워크들과 연결되어 있는지 알아야 한다.

   그래서 각 AS마다 갈 수 있는 (reachable) 네트워크를 통해 reachability 정보를 알아낸다.

2. iBGP (internal)

   그 reachability 정보를 자신의 AS 내부의 모든 라우터들과 공유해야 (propagate) 한다.

이제 AS 내부에서 돌아가는 알고리즘은 살펴봤다. (link state, distance vector)

외부 AS 와 연결된 네트워크와 어떻게 접근하느냐가 핵심이다.

### 예: 라우터에서 forwarding table 세팅하기

AS1 내부의 **라우터 1d** 는 자신과 직접 연결되지 않은 네트워크 **X** 로 연결하고 싶다.

1. AS1은 **inter-AS protocol** 로 AS3 를 통해 **X** 로 갈 수 있고( **게이트웨이 라우터 1c** 를 통해), AS2로는 갈 수 없다는 것을 알아냈다.

   (inter-AS protocol은 reachability 정보를 모든 내부 라우터들에게 propagates 한다.)

2. **라우터 1d** 는 **intra-AS routing 정보** 를 통해서, **게이트웨이 라우터 1c** 까지의 최저 cost 라우팅 정보를 알아낸다.

   그 최저 cost 라우팅이 I 로 가야 한다는 결론이 나왔다고 한다면, 테이블 엔트리에

   (X,I) 라고 적는다.

### 예: 여러 AS들 사이에서 선택하기

만약 네트워크 **X** 가 **AS3** 으로도 갈 수 있고, **AS2** 로도 갈 수 있다면 어느쪽으로 가야 하는가?

forwarding table을 작성하기 위해서, **라우터 1d** 는 **어떤 게이트웨이 라우터** 로 패킷을 포워딩 할지 결정해야 한다.

(이것 역시 inter-AS routing protocol 이 할 일이다.)

> 보통,
>
> 거쳐야하는 AS의 갯수가 적은 쪽으로 라우팅된다.
>
> -> 만약 그 갯수마저 똑같다면?
>
> AS1 내부에서 (intra-AS routing) 했을 때 더 짧은 쪽으로
>
> 이를 BGP 알고리즘 이라고 한다.

- **hot potato routing**

  손에 뜨거운 감자를 들고 있으면, 일단 젤 빨리 줄 수 있는 사람한테 감자를 넘겨주고 볼 것이다.

  거쳐야 하는 AS의 개수까지는 생각안한다. 바로 내부적으로 빠른 길 (intra-AS routing)을 통해 그 쪽으로 보낸다.

  1. 여러 게이트웨이를 통해 목적지에 갈 수 있다면, 어느 게이트웨이로 포워딩 할지 선택해야 한다.
  2. intra-AS protocol 을 통해 각 게이트웨이로의 최저-비용 path를 알아낸다.
  3. 가장 비용 적게 드는 게이트웨이를 선택한다.
  4. 최저-비용 게이트웨이로의 인터페이스 'I' 를 forwarding table 로부터 알아내서, forwarding table 에 (x, I) 를 넣는다.

## 6. Intra-AS Routing

- Link state
- Distance Vector

방식이 있다.

**IGP (interior gateway protocols)** 로도 알려져 있다.

### 흔히 사용되는 intra-AS routing protocols

- RIP (Routing Information Protocol)

  Distance Vector 방식 사용

- OSPF (Open Shortest Path First)

  Link State 방식 사용

### RIP (Routing Information Protocol)

Distance Vector 알고리즘을 사용한다. (불완전한 정보에서 시작해서 점점 수정해 나감)

목적지 subnet 까지 다음과 같이 표시한다.

ex) router D 에서

| destination subnet | next router | # hops to dest |
| :----------------: | :---------: | :------------: |
|         w          |      A      |       2        |
|         y          |      B      |       2        |
|         z          |   ~~B~~ A   |    ~~7~~ 5     |
|         x          |     --      |       1        |
|        ...         |     ...     |      ...       |

ex) router A-to-D advertisement

| dest | next | hops |
| :--: | :--: | :--: |
|  w   |  -   |  1   |
|  x   |  -   |  1   |
|  z   |  c   |  4   |
| ...  | ...  | ...  |

라우터 A가 라우터 D에게

A 입장에서 Z 까지 가는데, C를 경유해서 가면 총 4hop 만에 도착할 수 있다는 사실을 알려줬다.

라우터 D는 이 정보를 바탕으로 A를 경유해서, C를 경유 하면, Z까지 4+1hop 만에 도착한다는 결론을 얻었다.

그래서 위와 같이 table을 수정한다.

