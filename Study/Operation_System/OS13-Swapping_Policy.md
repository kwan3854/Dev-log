# Swapping - Policy

## 물리 메모리 크기의 극복: 정책

> **핵심 질문: 내보낼 페이지는 어떻게 결정하는가**
>
> 운영체제는 어떻게 메모리에서 내보낼 페이지를 결정할 수 있을까?
>
> 이 결정은 시스템의 교체 정책에 의해서 내려진다.

## 1. 캐시 관리

시스템의 전체 페이지들 중 일부만이 메인 메모리에 유지된다는 것을 가정하면,

**메인 메모리는 시스템의 가상 메모리 페이지를 가져다 놓기 위한 캐시로 생각할 수 있다.**

이 캐시를 위한 교체 정책의 목표는 **캐시 미스** 의 횟수를 최소화하는 것이다.

캐시 히트와 미스의 횟수를 안다면 프로그램의 **평균 메모리 접근 시간 (average memory access time, AMAT)** 을 계산할 수 있다.

`AMAT = T(M) + ( P(Miss) * T(D) )`

- **T(M):** 메모리 접근 비용
- **T(D):** 디스크 접근 비용
- **P(Miss):** 캐시에서 데이터를 못 찾을 확률. 0.0~1.0 사이의 값

여기서 메모리에는 항상 접근해야 해서, 메모리 접근 비용은 항상 지불해야 하지만,

메모리에서 데이터를 못 찾을 경우에는 디스크로부터 데이터를 가져오는 비용을 추가 지불해야 한다.

> 현대 시스템에서는 디스크 접근 비용이 너무 크기 때문에 아주 작은 미스가 발생하더라도 전체적인 AMAT에 큰 영향을 주게 된다.
>
> 컴퓨터가 디스크 속도 수준으로 느리게 실행되는 것을 방지하기 위해서는 당연히 미스를 최대한 줄여야 한다.
>
> 이를 위해서는 좋은 정책을 잘 만들어야 한다.

## 2. 최적 교체 정책

어떤 정책이 잘 동작하는지 알기 위해서는 이론상 최적의 정책을 가정한 다음, 그것과 비교해 보면된다.

**최적 교체 정책** 은 미스를 최소화한다.

**가장 나중에 접근된 페이지를 교체하는 것** 이 최적이며, 가장 적은 횟수의 미스를 발생시킨다는 것이 증명되었다.

> 최적 정책(optimal replacement policy) 는 Belady 에 의해 개발되었다.

다음 예시를 보면서 최적 기법의 동작을 살펴보자.

- 프로그램이 0, 1, 2, 0, 1, 3, 0, 3, 1, 2, 1 의 순서로 가상 페이지들을 접근한다.
- 캐시에는 세 개의 페이지를 저장할 수 있다.

| 접근 | 히트/미스? | 내보냄 | 결과적인 캐시 상태 |
| :--: | :--------: | :----: | :----------------: |
|  0   |  **미스**  |        |         0          |
|  1   |  **미스**  |        |        0, 1        |
|  2   |  **미스**  |        |      0, 1, 2       |
|  0   |    히트    |        |      0, 1, 2       |
|  1   |    히트    |        |      0, 1, 2       |
|  3   |  **미스**  |   2    |      0, 1, 3       |
|  0   |    히트    |        |      0, 1, 3       |
|  3   |    히트    |        |      0, 1, 3       |
|  1   |    히트    |        |      0, 1, 3       |
|  2   |  **미스**  |   3    |      0, 1, 2       |
|  1   |    히트    |        |      0, 1, 2       |

최적 기법의 경우 캐시에 현재 탑재되어 있는 각 페이지들의 미래를 살펴본다.

그리고 가장 먼 미래에 접근될 것을 내보낸다.

불행히도 미래는 일반적으로 미리 알 수 없다.

그렇기 때문에 범용 운영체제에서는 최적 기법의 구현은 불가능하다.

최적 기법은 비교 기준으로만 사용될 것이며, 이를 통해 정답에 얼마나 가까운지 알 수 있다.

## 3. 간단한 정책: FIFO

초기의 많은 시스템들은 매우 간단한 교체 정책을 채용했다.

예를들어 일부 시스템에서는 **FIFO (선입선출)** 교체 방식을 사용했다.

| 접근 | 히트/미스? | 내보냄 | 결과적인 캐시 상태 |
| :--: | :--------: | :----: | :----------------: |
|  0   |  **미스**  |        |         0          |
|  1   |  **미스**  |        |        0, 1        |
|  2   |  **미스**  |        |      0, 1, 2       |
|  0   |    히트    |        |      0, 1, 2       |
|  1   |    히트    |        |      0, 1, 2       |
|  3   |  **미스**  |   0    |      1, 2, 3       |
|  0   |  **미스**  |   1    |      2, 3, 0       |
|  3   |    히트    |        |      2, 3, 0       |
|  1   |  **미스**  |   2    |      3, 0, 1       |
|  2   |  **미스**  |   3    |      0, 1, 2       |
|  1   |    히트    |        |      0, 1, 2       |

최적의 경우와 비교하면 FIFO는 눈에 띄게 성능이 안좋다.

FIFO는 블럭들의 중요도를 판단할 수가 없다.

페이지 0이 여러 차례 접근이 되었더라도 단순히 메모리에 먼저 들어왔다는 이유로 페이지 0을 내보낸다.

## 4. 또 다른 간단한 정책: 무작위 선택

무작위 선택 방식은 FIFO와 유사한 성질을 가지고 있다.

구현하기 쉽지만 내보낼 블럭을 제대로 선택하려는 노력을 하지 않는다.

무작위 선택 방식의 동작은 그때그때 달라진다.

## 5. 과거 정보 사용: LRU

스케줄링 정책에서처럼, 미래에 대한 예측을 하기 위해 과거의 이력을 활용해 보자.

예컨데 어떤 프로그램이 가까운 과거에 어떤 페이지에 접근했다면 가까운 미래에 그 페이지에 다시 접근할 확률이 높다.

- **Least-Frequently-Used (LFU):**

  가장 적은 빈도로 사용된 페이지를 교체한다.

- **Least-Recently-Used (LRU):**

  가장 오래전에 사용한 페이지를 교체한다.

LRU 방식이 동작하는 예를 살펴보자

| 접근 | 히트/미스? | 내보냄 | 결과적인 캐시 상태 |
| :--: | :--------: | :----: | :----------------: |
|  0   |  **미스**  |        |         0          |
|  1   |  **미스**  |        |        0, 1        |
|  2   |  **미스**  |        |      0, 1, 2       |
|  0   |    히트    |        |      1, 2, 0       |
|  1   |    히트    |        |      2, 0, 1       |
|  3   |  **미스**  |   2    |      0, 1, 3       |
|  0   |    히트    |        |      1, 3, 0       |
|  3   |    히트    |        |      1, 0, 3       |
|  1   |    히트    |        |      0, 3, 1       |
|  2   |  **미스**  |   0    |      3, 1, 2       |
|  1   |    히트    |        |      3, 2, 1       |

이 예제는 LRU가 최적 기법과 비슷한 수준의 성능을 얻을 수 있는 최고의 결과를 보여주고 있다.

## 6. 워크로드에 따른 성능 비교

지금까지 살펴본 정책들의 동작을 몇가지 워크로드를 가정해서 살펴보자.

1. **지역성이 없는 워크로드:**

   워크로드에 지역성이 없다면 어느 정책을 사용하든 상관이 없다. 모두 동일한 성능을 보인다.

2. **80 대 20 워크로드:**

   20%의 인기 있는 페이지에서 80%의 참조가 발생하고 나머지 80%의 페이지들에서 20%의 참조가 이루어진다.

   랜덤과 FIFO 정책이 상당히 좋은 성능을 보이지만, LRU가 더 좋은 성능을 보인다.

과연 LRU가 항상 좋은 성능을 보여주기만 할까?

상황에 따라 다르다. 다음 상황을 보자.

3. **순차 반복 워크로드:**

   50개의 페이지들을 순차적으로 참조한다. 0, 1, 2, .. 48, 49, 50, 0, 1, 2, ...

   LRU와 FIFO 정책에서 가장 안좋은 성능을 보인다.

   캐시의 크기가 49라고해도 50개의 페이지를 순차 반복하면 캐시 히트율이 0%가 된다.

   무작위 선택 정책은 최적의 경웨 못미치기는 하지만 눈에 띄게 좋은 성능을 보인다.

   무작위 선택 정책은 이상한 코너 케이스가 발생하지 않는다는 장점이 있다.

## 7. 과거 이력 기반 알고리즘의 구현

> **핵심 질문: LRU 교체 정책을 구현하는 방법**
>
> 완벽한 LRU를 구현하는 것은 비싼 비용이 든다는 것을 전제로, 어떻게 하면 LRU에 가까운 선택을 하고 유사한 행동을 보이는 정책을 구현할 수 있을까?

대체로 FIFO 와 무작위 선택 정책보다는 LRU 가 더 좋은 성능을 보인다.

하지만 과거 정보에 기반을 둔 정책을 어떻게 구현해야 할까?

LRU를 실제로 완벽하게 구현하기 위해서는 많은 작업이 필요하다.

메모리 참조마다 매번 자료구조를 갱신할 필요가 있다.

정말 가장 오래된 페이지를 꼭 찾아야만 할까?

대신 비슷하게 오래된 페이지를 찾아도 되지 않을까?

## 8. LRU 정책 근사하기

LRU 정책에 가까운 구현이 가능한가? 그렇다.

연산량이라는 관점에서 볼때 LRU를 "근사" 하는 방법으로 만들면 구현이 훨씬 쉬워진다.

실제로 현대의 많은 시스템이 이런 방식을 택하고 있다.

이 개념에는 **use bit (a.k.a reference bit)** 라고 하는 하드웨어 지원이 필요하다.

이 **use bit** 는 메모리 어딘가에 존재한다. (구현에 따라 프로세스마다 있는 페이지 테이블에 있을수도, 어딘가에 배열 형태로 존재할 수도 있다.)

페이지가 참조될 때마다 하드웨어에 의해서 use bit 가 1로 설정된다.

하드웨어는 이 비트를 절대로 0으로 바꾸지 않는다.

0으로 바꾸는 것은 운영체제만 할 수 있다.

운영체제는 use bit 을 어떻게 활용할까?

간단한 방법은 **시계 알고리즘(clock algorithm)** 이 있다.

### 시계 알고리즘

시계 바늘은 시스템의 모든 페이지들을 환형리스트로 해서 순회한다.

1. 바늘이 가리키는 페이지의 use bit을 검사한다.

2. 1이면 해당 페이지는 최근에 사용되었으며 교체하기에 바람직한 대상이 아니라는 뜻이다.

   use bit을 0으로 바꾸고 다음 페이지로 이동한다.

3. 0이면 해당 페이지를 교체한다.

4. use bit이 0인, 최근에 사용된 적 없는 페이지를 찾을 때 까지 반복한다.

## 9. 갱신된 페이지 (Dirty Page)의 고려

시계 알고리즘에 대한 추가 개선 사항을 논의하고자 한다.

운영체제가 교체 대상을 선택할 때 메모리에 탑재된 이후에 변경되었는지를 추가적으로 고려하는 것이다.

이것이 필요한 이유는...

1. 만약 어떤 페이지가 변경 되어 더티 상태가 되었다면 그 페이지를 내보내기 위해서는 디스크에 변경 내용을 기록해야 하기 때문에 비싼 비용을 지불해야 한다.
2. 만약 변경되지 않았다면 추가적인 I/O 없이 다른 용도로 재사용될 수 있다.

예를 들어 시계 알고리즘은 교체 대상을 선택할 때 사용되지 않은 상태이고 깨끗한, 두 조건을 모두 만족하는 페이지를 먼저 찾도록 수정된다.

이런 페이지가 없으면, 다음으로 수정되었지만 한동안 사용되지 않았던 페이지를 찾는다.

## 10. 다른 VM 정책들

페이지 교체 정책만이 VM 시스템이 채택하는 유일한 정책은 아니다 (가장 중요하긴 하다).

운영체제는 언제 페이지를 메모리로 불러들일지 결정해야 한다.

이 정책은 **페이지 선택(page selection)** 정책이라고 불린다.

운영체제는 대부분의 페이지를 읽어 들일 때 **요구 페이징 (demand paging)** 정책을 사용한다.

- **요구 페이징 (demand paging):**

  이 정책은 요청된 후 즉시, 페이지가 실제로 접근될 때 운영체제가 해당 페이지를 메로리로 읽어 들인다.

- **선반입 (prefetching):**

  운영체제는 어떤 페이지가 곧 사용될것이라는 걸 대략 예상할 수 있기 때문에 미리 메모리로 읽어들일 수 있다.

  성공할 확률이 충분히 높을 때에만 해야 한다.

- **클러스터링(clustering) 또는 쓰기모으기(grouping of writes):**

  운영체제가 변경된 페이지를 디스크에 반영할 때 한번에 한 페이지씩 디스크에 쓰는것이 아니라,

  기록해야 할 페이지들을 메모리에 모은 후, 한번에 디스크에 기록한다.

## 11. 쓰레싱(Thrashing)

메모리 사용 요구가 감당할 수 없을 만큼 많고 실행 중인 프로세스가 요구하는 메모리가 가용 물리 메모리 크기를 초과하는 경우에 운영체제는 어떻게 해야 하는가?

이런 경우 시스템은 끊임없이 페이징을 할 수 밖에 없고, 이와 같은 상황을 **쓰레싱(Thrashing)** 이라고 한다.

몇몇 초기 운영체제들은 상당히 정교한 기법들로 이를 해결했지만,

일부 최신 시스템들은 메모리 과부하에 대해 좀 더 과감한 조치를 취한다.

Linux는 메모리 요구가 초과되면 **메모리 부족 킬러(out-of-memory killer)** 를 실행시킨다.

이 데몬은 많은 메모리를 요구하는 프로세스를 골라 죽이는 방식으로 메모리 요구를 줄인다.

---
