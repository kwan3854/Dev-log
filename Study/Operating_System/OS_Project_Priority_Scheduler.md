# XV6 에서의 Priority Scheduler 구현

이전의 getnice, setnice, ps 구현 코드를 재활용 한다.

## 요구사항

- nice 값이 낮을 수록(priority 값이 클 수록) 우선해서 스케줄링 한다.
- Tiebreak 시 라운드 로빈 한다. (아무 방식이나 해도 된다고 했다. 그러나 라운드 로빈이 젤 쉬운 방법같다. 알파벳 순 같은것을 다른 예시로 들으셨다.)
- 타임 인터럽트 시 스케줄러 호출이 필요없다. 제거해라. (priority 값 변경될때만 스케줄러 호출)
- Makefile 에서 CPU 설정을 2에서 1로 바꾼다.
- fork 시 자식 프로세스의 priority 값을 5로 설정해라. (기존의 부모 priority 값을 가져오는 방식 말고.)
- 기존에는 priority = nice 값으로 취급했으나, 지금은 서로 반비례 관계로 사용해라.

## 의문

- **proc.c 에서 값을 nice 만 사용하면 효율적인데 priority 사용해야 하는 이유?**

  priority 라는 값을 아예 없애고 nice만 사용하면 계산과정도 줄어들고 훨씬 직관적인데 왜 굳이 priority 값을 넣어야 하는가?

  교수님께 질문해 본 결과, 명확한 답을 주시지 않는다. 구글링을 해봐도 다들 그냥 nice를 이용해 구현하는 것 같다. priority를 써야할 당위성을 설명하는 자료는 찾지 못했다. 이유는 모르겠지만 그냥 nice를 빼고 priority 만 구현하고 getnice, setnice 시 priority를 nice와 변환하면서 사용하게 만들었다.

- **priority 스케줄링이란 정확히 무엇인가?**

  명확하게 priority 스케줄링이 정의되어 있지가 않다.

  추측컨데, MLFQ를 구현하라고 시키면 학생들이 어려워 할까봐 MLFQ에서  몇가지 기능을 뺀 것을 의미한 것 같다.

  priority boost, 타임 슬라이스 마다 우선순위 낮추는 작업 등을 뺀 것인듯.

  처음에는 SJF 와 같이 타이머 인터럽트가 없는 방식이라고 생각해서 혼란스러웠는데, MLFQ에서 기능을 뺀것이라 생각하니 쉽다.

- **스케줄러를 구현하는 방법론**

  처음에는 간단하다고 생각했는데 수많은 방법이 생각나지만, 뭐가 optimal 한 방법인지 어렵다...

  아래에서 코드로 살펴본다.

## 스케줄러 구현 방법

여러가지 방법이 떠올랐으나 다 문제가 있어보인다 (완벽해 보이지 않는다.).

### 첫번째 방법

가장 큰 priority를 찾아서 전체 proc 순회하면서 그 priority 가진 프로세스 실행.

proc.c 에서 scheduler() 부분의 코드 일부.

```c
int highest_priority = 0; // 최대값을 찾기 위한 변수이다. 초기화를 위해 최저값을 넣는다.

acquire(&ptable.lock);

// 최대 우선순위 값 찾기
for (p = ptable.proc; p < &ptable.proc[NPROC]; p++)
{
  if (p->state == RUNNABLE && p->priority > highest_priority)
  {
    highest_priority = p->priority;
  }
}

// 가장 큰 우선순위를 가진 프로세스를 찾아 실행.
for (p = ptable.proc; p < &ptable.proc[NPROC]; p++)
{
  if(p->state == RUNNABLE && highest_priority == p->priority)
  {
    //코드 진행
  }
}
```

간단해 보인다. 그런데 문제가 있다.

만약 프로세스 1,2 가 같은 최대 우선순위를 가졌다고 해보자. 그리고 둘다 엄청 오래걸리는 작업이라고 치자.

프로세스 1이 먼저 실행되고 타임 인터럽트가 걸리면 , 프로세스 2가 실행된다.

프로세스 2실행도중 타임 인터럽트가 걸리면? 다시 scheduler 코드 전체에 크게 묶여있는 무한루프 for(;;)문이 다시 돈다.

즉, 우선순위를 변경한 적이 없지만, 스케줄러가 불필요하게 우선순위 계산을 한 번 더 하게 된다.

테스트 코드는 잘 통과한다. 그렇지만...

세번째 요구사항에 반하는 것 아닌가?

---

### 두번째 방법

하나만 집어서 실행하니까 문제가 생겼다. 기존의 스케줄러처럼 순회를 하게 하면 문제가 없어질 것 같다.

이중 for문을 이용해보는 방법이 떠올랐다.

```c
		struct proc *highestP;

		acquire(&ptable.lock);

    // Loop over process table looking for process to run.
    for(p = ptable.proc; p < &ptable.proc[NPROC]; p++)
    {
      if(p->state == RUNNABLE)
      {
        highestP = p;
        for (p1 = ptable.proc; p1 < &ptable.proc[NPROC]; p1++)
        {
          if(p1->state == RUNNABLE && highestP->priority < p1->priority)
          {
            highestP = p1;
          }
        }
        p = highestP;
        // Switch to chosen process.  It is the process's job
        // to release ptable.lock and then reacquire it
        // before jumping back to us.
        c->proc = p;
        switchuvm(p);
        p->state = RUNNING;

        swtch(&(c->scheduler), p->context);
        switchkvm();

        // Process is done running for now.
        // It should have changed its p->state before coming back.
        c->proc = 0;
      }
    }
    release(&ptable.lock);
  }
```

코드가 지저분해졌다.

역시 테스트 코드는 통과한다.

그런데 동작 과정을 생각해보면 쉽게 이게 optimal 하다고 말 할수가 없을 것 같다.

얘도 마찬가지로 한 프로세스가 끝나면 다시 새롭게 가장 큰 우선순위값을 갱신한다.

여전히 불필요하게 우선순위 계산을 매번 하는것 아닌가?

세번째 요구사항에 여전히 반하는 것 아닌가?

---

### 세번째 방법

매번 우선순위를 계산하니까 문제가 생겼다.

자료구조를 이용해 한번에 정렬을 한 후에 큐와 같은 자료구조에 저장하고, 정렬된 자료구조를 순회하면 되지 않을까?

생각해보니까 처음부터 모든 sorting을 하는것이 더 손해다.

---

### 네번째 방법

잘 생각해보니까 첫번째 방법에서 무한 순회를 하게 하고 탈출조건을 잘 조정하면 될 것 같다.

구현할때는 2번째로 높은 우선순위를 가진 프로세스는 생각할 필요가 없다.

매번 가장 중요한 것만 하나씩 실행하는것을 반복하면 된다. (사실 이게 selection sort 랑 동일한 개념인것 같다.)

첫번째 방법에서 두번째 루프 부분을 수정하면 될 것같다.

```c
int highest_priority = 0; // 최대값을 찾기 위한 변수이다. 초기화를 위해 최저값을 넣는다.

acquire(&ptable.lock);

// 최대 우선순위 값 찾기
for (p = ptable.proc; p < &ptable.proc[NPROC]; p++)
{
  if (p->state == RUNNABLE && p->priority > highest_priority)
  {
    highest_priority = p->priority;
  }
}

p = ptable.proc;
int counter = 0;
while(1)
{
  if(p->state == RUNNABLE && highest_priority == p->priority)
  {
    //코드 진행
    counter++;
  }
  
  p++;
  if(p >= &ptable.proc[NPROC]) // 다 순회했으면
  {
  	if(counter == 0) // highest_priority 없으면
  	{
    	break;
  	}
   	else // highest_priority 하나라도 있었으면
    {
      // 다시 처음부터
      p = ptable.proc;
      counter = 0;
    }
  }
}
```

루프의 탈출조건이 문제였다.

한번만 순회하고 탈출하지 말고 해당 prioirty값을 가진애가 사라질때 까지 계속 순회하면 된다.

**이 방법으로 하면 test코드를 통과하지 못한다** (버그?)

지금은 시간이 없어 나중에 이유를 찾아봐야겠다.

일단 두번째 코드를 사용해야겠다.

---

그런데 매번 꼭 처음부터 NPROC 까지 다 순회할 필요가 있을까?

어차피 한번 우선순위 최고인 프로세스는 끝까지 변화가 없을것이다.

변화가 있다면 다시 새롭게 스케줄러를 처음부터 다시 호출할것이기 때문에.

가장 큰 우선순위를 가진 프로세스를 한번 순회해서 리스트를 만들고 그 리스트만 순회하면 되는것 아닌가?

(리스트는 큐나 링크드 리스트 같은 자료구조를 말한다.)

대충 생각해보면 다음과 같을 것이다.

```c
int highest_priority = 0; // 최대값을 찾기 위한 변수이다. 초기화를 위해 최저값을 넣는다.

acquire(&ptable.lock);

// 최대 우선순위 값 찾기
for (p = ptable.proc; p < &ptable.proc[NPROC]; p++)
{
  if (p->state == RUNNABLE && p->priority > highest_priority)
  {
    highest_priority = p->priority;
  }
}

// 가장 큰 우선순위를 가진 프로세스를 찾아 리스트를 만듦.
for (p = ptable.proc; p < &ptable.proc[NPROC]; p++)
{
  if(p->state == RUNNABLE && highest_priority == p->priority)
  {
    //리스트에 push 함.
  }
}

//리스트를 무한 순회한다.
```

그런데 리스트를 만들어서 리스트를 참조, 순회하는 것 자체가 더 큰 오버헤드가 될 수도 있을 것같다.

NPROC은 해봤자 64밖에 되지 않는다.

이것보다는 네번째 방법이 나을 것 같다.

---

### 테스트 코드

교수님이 주신 테스트코드를 그대로 사용해야 해서 했지만, 문제가 있는 코드임을 알았고, 교수님께 질문해서 피드백을 받았다,

일단 코드를 보자.

**test_sched1.c**

```c
#include "types.h"
#include "stat.h"
#include "user.h"

int main(int argc, char** argv)
{
    int pid;
    int mypid;

    // Change the priority of init process.
    setnice(1, 10);

    // Change the priority of current processes.
    setnice(getpid(), 2);

    // Create a child process
    pid = fork();

    if(pid == 0)
    {
        printf(1, "#### State 2 ####\n");
    }
    else
    {
        printf(1, "#### State 1 ####\n");

        // Change the priority of parent process
        setnice(pid, 10);
        wait();
        printf(1, "#### State 3 ####\n");
    }

    mypid = getpid();
    printf(1, "PID %d is finished\n", mypid);

    exit();
}
```

**test_sched2.c**

```c
#include "types.h"
#include "stat.h"
#include "user.h"

int main(int argc, char** argv)
{
    int pid;
    int mypid;

    // Change the priority of init process.
    setnice(1, 10);

    // Change the priority of current processes.
    // setnice(getpid(), 2);
    setnice(getpid(), 9);

    // Create a child process
    pid = fork();

    if(pid == 0)
    {
        // Child
        printf(1, "#### State 2 ####\n");
    }
    else
    {
        // Parent
        printf(1, "#### State 1 ####\n");

        // Change the priority of parent process
        setnice(pid, 10);
        wait();
        printf(1, "#### State 3 ####\n");
    }

    mypid = getpid();
    printf(1, "PID %d is finished\n", mypid);

    exit();
}
```

일단 교수님이 원하는 실행결과는 1번코드 실행 시 state 1,2,3 순으로 출력되고, 2번코드 실행 시 state 2, 1, 3 순으로 출력되는 것이다.

문제가 되는 부분은 다음 부분이다.

```c
// Change the priority of parent process
setnice(pid, 10);
```

comment 에서는 parent process의 priority 를 변경한다고 되어있지만, 자식 프로세스가 변환된다. 오류이다.

그리고 더 큰 문제는 이 코드가 아무 의미가 없는 코드라는 것이다.

위의 setnice() 가 어떤 프로세스를, 또 그 프로세스의 nice 값을 어떤 값으로 바꾸든 결과값에는 아무 영향도 미치지 못한다.

wait() 함수가 무조건적으로 자식프로세스가 끝나기까지 기다리기 때문이다.

결국 위 코드는 `pid =fork();` 부분에서 모든 것이 결정되는것이다.

fork 수행될때 스케줄러를 호출하도록 코드를 수정해 놓았기 때문에, fork 시 priority 값에 의해 모든 것이 결정된다.

결코 좋은 테스트코드가 아닌것 같다.

---

### 내가 생각하는 바람직한 테스트 코드

교수님께서는...

- 타이머 인터럽트 시 스케줄러를 호출하지 말라는 것을 강조하셨다.

  이 말씀에 나는 당연히 동일한 nice값을 가진 여러개의 프로세스가 존재 할 때 타이머 인터럽트가 걸릴시, 어떻게 반응하는지도 당연히 생각을 많이 해 봤다.

- priority 값이 변경될때 마다 스케줄러가 호출되어야 함도 강조하셨다.

  그렇다면 setnice 시에도 당연히 스케줄러가 호출되어야 할 것이다.

즉, 최소한 위와같은 요구조건을 모두 충족했는지는 테스트가 이루어져야 한다고 생각한다.

---

제출한 코드는 다음 레포에 있다.

https://github.com/kwan3854/OS_2021/tree/master/Project_2

