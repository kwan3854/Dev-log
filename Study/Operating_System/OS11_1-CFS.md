# CFS

**CFS (Completely Fair Scheduler)**

현대 리눅스에서 사용되는 스케줄링 방식

---

## 1. CFS의 개념

- 예를 들어보자.

  만원짜리 피자가 있다고 해보자.

  그리고 3명의 사람이 각각 다음과 같이 돈을 지불했다고 하자.

  - A: 3000원
  - B: 2000원
  - C: 5000원

  그렇다면 A,B,C 가 각각 3:2:5 비율로 피자를 나눠갖는것이 공정하다고 할 수 있다.

- CPU 타임을 task 들의 weight 에 맞춰서 스케줄링 하는것이 이상적이다.

  `CPU time` = ( `Weight of task`  /  `total weight` ) * `total CPU time`

---

### 그런데

CPU 타임을 통째로 주면 될까?

A가 끝날때 까지 기다렸다가 B하고, B끝나면 C 하고 이런식으로 하면 I/O 시간 문제등이 생긴다.

결국 시간을 쪼개야 한다.

그러면 각 task 들의 weight 가 다음과 같다면

- A: 3
- B: 2
- C: 5

모두가 1씩의 CPU 타임을 사용했다면 공정한 상태인가?

아니다. C는 비율적으로 원래 받아야 하는 시간의 1/5 밖에 못받았다.

---

### Virtual Runtime

`Virtual Runtime` 

=( `the weight of nice value 0` /  `the weight of the task` ) * `actual runtime reeived in time interval [0, t]`

> nice 값의 중간 값은 0 이다.

|    -    |  A   |  B   |  C   |
| :-----: | :--: | :--: | :--: |
| weight  |  2   |  3   |  5   |
| runtime |  1   |  1   |  1   |
|  nice   |  -1  |  0   |  1   |

- A: 3/2 * 1 = 1.5
- B: 3/3 * 1 = 1
- C: 3/5 * 1 = 0.6

따라서 Virtual Runtime 값이 가장 작은 C가 가장 우선적으로 Scheduling 되어야 할 것이다.

---

### Run Queue Structure

큐에서 priority 0 ~ 99 까지는 real time 으로 스케줄링 하는 애들이라서, 따로 스케줄링 됨

priority 100 부터 CFS 로 스케줄링됨

CFS는 Red-Black tree 를 사용. (우리가 했던 priority scheduler 는 array 를 사용했기 때문에 O(n)의 시간 복잡도였음) 

---

### CFS Algorithm: Timeslice

`Time slice`= ( `weight` / `runqueue's total weight` ) * `P(constant)`

모두 같은 값의 타임 슬라이스를 적용하는게 아니라, 각 테스크의 웨이트에 비례해서 타임슬라이스를 배정하는 방식.

아까의 예시에서 A,B,C 의 총 weight 의 합은 10 이었다.

각 task의 타임 슬라이스는

- A: 3/10
- B: 2/10
- C: 5/10

이렇게 된다.

---



