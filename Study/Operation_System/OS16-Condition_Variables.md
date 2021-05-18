# Condition Variables

락(lock) 이외에도 병행 프로그램을 제작할 수 있는 다른 기법들이 존재한다.

쓰레드가 실행을 계속하기 전에, 특정 **조건** 의 만족여부를 검사해야 하는 경우가 많이 있다.

예를들어 부모 쓰레드가 작업을 시작하기 전에 자식 쓰레드가 작업을 끝냈는지 검사하고 싶을 수 있다. (이 과정을 **join()** 이라고 부른다)

### 자식을 기다리는 부모 쓰레드

```c
void *child(void *arg) {
  printf("child\n");
  // 작업이 끝났음을 어떻게 알리지?
  return NULL;
}

int main(int argc, char *argv[]) {
  printf("parent: begin\n");
  pthread_t c;
  Pthread_create(&c, NULL, child, NULL); // 자식을 생성하기
  // 자식 쓰레드를 어떻게 기다리지?
  printf("parent: end\n");
  return 0;
}
```

우리는 다음과 같은 출력문을 원한다.

```sh
parent: begin
child
parent: end
```

이를 위해 다음처럼 공유변수를 사용할수도 있다.

### 자식을 기다리는 부모 쓰레드: 회전 기반의 방법

```c
volatile int done = 0;

void *child(void *arg) {
  printf("child\n");
  done = 1;
  return NULL;
}

int main(int argc, char *argv[]) {
  printf("parent: begin\n");
  pthread_t c;
  Pthread_create(&c, NULL, child, NULL); // 자식 생성하기
  while (done == 0)
  ; // 회전
  printf("parent: end\n");
  return 0;
}
```

이 방법은 제대로 동작한다.

그러나 부모 쓰레드가 회전을 하면서 CPU 시간을 낭비하기 때문에 비효율적이다.

따라서 이 방법 대신, 부모 쓰레드가 특정 조건이 참이 될 때까지(예: 자식 쓰레드가 실행이 종료되는 것) 잠자면서 기다리는 방법이 좋다.

> **핵심 질문: 조건의 대기**
>
> 멀티 쓰레드 프로그램에서는 쓰레드가 계속 진행하기에 앞서 특정 조건이 참이 되기를 기다리는것이 유용할 때가 많다.
>
> 조건이 참이 될 때까지 회전을 하며 기다리는 것이 간단하긴 하겠지만, 지독하게 비효율적이다.
>
> 어떤 경우에는 부정확할 수도 있다.
>
> 그렇다면 **쓰레드는 어떻게 조건을 기다려야 할까?**

## 1. 컨디션 변수의 개념과 관련 루틴

쓰레드 실행 시, 특정 조건이 만족될 때까지 대기하기 위해 **컨디션 변수(conditional variable)** 이라고 부르는 개념을 사용할 수 있다.

컨디션 변수는 일종의 **큐(queue)** 자료구조이다.

쓰레드 실행에서 어떤 상태 또는 어떤 조건이 원하는 것과 다를 때 조건이 만족대기를 **대기** 하는 큐이다.

다른 쓰레드가 실행되어 상태를 변경시키고, 해당 조건이 만족되었을 때, 대기 중인 쓰레드를 깨워 실행을 계속하게 한다.

### 자식을 기다리는 부모 쓰레드: 컨디션 변수의 사용

```c
int done = 0;
pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t c = PTHREAD_COND_INITIALIZER;

void thr_exit() {
  Pthread_mutex_lock(&m);
  done = 1;
  Pthread_cond_signal(&c);
  Pthread_mutex_unlock(&m);
}

void *child(void *arg) {
  printf("child\n");
  thr_exit();
  return NULL;
}

void thr_join() {
  Pthread_mutex_lock(&m);
  while (done == 0)
    Pthread_cond_wait(&c, &m);
  Pthread_mutex_unlock(&m);
}

int main(int argc, char *argv[]) {
  printf("parent: begin\n");
  pthread_t p;
  Pthread_create(&p, NULL, child, NULL);
  thr_join();
  printf("parent: end\n");
  return 0;
}
```

컨디션 변수에는 두 개의 연산이 있다.

1. **wait()**

   쓰레드가 스스로를 잠재우기 위해서 호출하는 것

2. **signal()**

   조건이 만족되기를 대기하며 잠자고 있던 쓰레드를 깨울 때 호출

**POSIX** 의 사용례는 다음과 같다.

```c
pthread_cond_wait(pthread_cond_t *c, pthread_mutex_t *m);
pthread_cond_signal(pthread_cond_t *c);
```

---

위 코드가 실행되는 두 가지 경우가 있다. (부모 쓰레드가 자식 쓰레드를 생성 한 후,)

- **부모 쓰레드가 자식 쓰레드보다 먼저 실행되는 경우**

  1. 부모 쓰레드는 thr-join() 을 호출하고 자식 쓰레드의 종료를 대기.

  2. thr_join() 내에서, 부모 쓰레드는 락 획득 후 자식 쓰레드의 종료여부를 검사

     done 이 1인지 검사

  3. 아직 자식 쓰레드는 실행되지 못한 상태

  4. 자식 쓰레드가 종료되지 않았기 때문에 부모 쓰레드는 wait()를 호출하고 대기상태로 변함.

  5. **여기서 중요한 사항**

     Pthread_cond_wait(&c, &m) 함수의 호출을 통해서

     부모 쓰레드는 자신의 상태를 대기로 변경함과 동시에 획득했던 락을 반납한다.

  6. 자식 쓰레드가 실행되면, "child"라는 메시지를 출력하고 thr_exit() 을 호출한다.

  7. 자식 쓰레드는 thr_exit() 함수를 호출하여 상태 변수 done을 1 로 설정하고

     부모 쓰레드에 시그널을 보낸다.

  8. 부모 쓰레드는 wait() 에서 빠져나와 나머지 코드를 실행한다.

  9. **여기서 다시 중요한 사항**

     wait() 에서 리턴 시, 부모 쓰레드는 락을 보유한 상태가 된다.

  10. 부모 쓰레드는 wait() 에서 리턴 후 락을 해제하고 "parent: end" 메시지를 출력

- **자식 쓰레드가 부모 쓰레드보다 먼저 실행되는 경우**

  1. 자식 쓰레드는 done 변수를 1로 설정하고, 시그널 함수를 호출한다.
  2. 대기중인 쓰레드가 없기 때문에 시그널 함수는 단순히 리턴한다.
  3. 추후 부모 쓰레드가 실행되면, thr_join() 을 호출한다.
  4. done 변수가 1이므로, 대기없이 바로 리턴한다.

- **매우 중요한 사실**

  부모 쓰레드가 특정 조건의 만족 여부를 검사할 때 (여기서는 done의 값)

  if 문이 아니라 while 문을 사용한다는 사실이다. (이유는 나중에 설명)

> **wait() 와 락에 대해서**
>
> 1. wait() 는 mutex를 해제하고 호출한 쓰레드를 재운다.
>
> 2. 다른 쓰레드가 시그널을 보내서 대기중인 쓰레드가 슬립 상태에서 깨어나면,
>
>    wait() 에서 리턴하기 전에 반드시 락을 재획득해야 한다.
>
>    **"슬립에서 깨어난 프로세스는 리턴하기 전에 락을 재획득해야 한다"**
>
> 3. 시그널을 받아서 대기상태에서 깨어났더라도 락 획득에 실패하면 다시 sleep 상태로 들어간다.

의문: done 이라는 상태 변수가 꼭 필요할까?

### 부모의 기다리기: 상태 변수 없음

```c
void thr_exit() {
  Pthread_mutex_lock(&m);
  Pthread_cond_signal(&c);
  Pthread_mutex_unlock(&m);
}

void thr_join() {
  Pthread_mutex_lock(&m);
  Pthread_cond_wait(&c, &m);
  Pthread_mutex_unlock(&m);
}
```

이 방법은 잘못되었다.

자식 쓰레드가 생성된 즉시 실행되어서 thr_exit()를 호출하는 경우를 생각해 보자.

그 경우, 자식 프로세스가 시그널을 보내겠지만, 깨워야할 쓰레드가 없다.

부모 쓰레드가 실행되면 wait() 을 호출하고 멈춰 있을 것이다.

어떤 쓰레드도 부모 쓰레드를 깨우지 않을 것이다,

done 이라는 변수는 꼭 필요하다.

잠자고, 깨우고, 락을 설정하는 것이 done 이라는 상태 변수를 중심으로 구현되어 있다.

의문: 그렇다면, 락은 꼭 필요할까?

### 부모의 기다리기: 락 없음

```c
void thr_exit() {
  done = 1;
  Pthread_cond_signal(&c);
}

void thr_join() {
  if (done == 0)
    Pthread_cond_wait(&c);
}
```

여기서는 경쟁 조건이 발생한다.

1. 만약 부모 쓰레드가 thr_join() 을 호출하고 나서 done 변수의 값이 0 인것을 확인한 후 대기상태로 들어가려 한다면,
2. wait() 호출 직전에 자식 쓰레드로 문맥전환이 일어나서 자식 쓰레드가 실행이 되었다.
3. 자식 쓰레드는 상태 변수 done의 값을 1로 변경하고 시그널을 보낸다.
4. 대기중인 쓰레드가 없다.
5. 타이머 인터럽트가 발생하고, 문맥이 전환되어 부모 쓰레드가 다시 실행된다.
6. 부모 쓰레드는 wait() 을 호출하고 잠자게 된다.
7. 부모 쓰레드를 깨워줄 쓰레드가 존재하지 않는다.

## 2. 생산자/소비자 (유한 버퍼) 문제

다음으로 살펴볼 동기화 문제는 Dijkstra가 처음 제시한 **생산자/소비자 (producer/consumer)** 문제이다.

**유한 버퍼 (bounded 버퍼)** 문제로도 알려져 있다.

후에 락이나 컨디션 변수를 대신하여 사용할 수 있는 일반화된 세마포어가 발명된 이유가 이 생산자/소비자 문제 때문이다.

### 생산자/소비자 란?

다수의 생산자 쓰레드와 소비자 쓰레드가 있다고 하자.

- **생산자는**

  데이터를 만들어 버퍼에 넣는다.

- **소비자는**

  버퍼에서 데이터를 꺼내어 사용한다.

이러한 관계는 실제 시스템에서 자주 사용된다.

> 예)
>
> - HTTP 요청을 생산자 쓰레드가 작업 큐에 넣고, 소비자 쓰레드가 큐에서 요청을 꺼내서 처리
>
> - pipe 명령으로 한 프로그램의 결과를 다른 프로그램에게 전달할 때 유한 버퍼 사용.
>
>   ```sh
>   grep foo file.txt | wc -l
>   ```
>
>   - 생산자: grep
>   - 소비자: wc
>
>   둘 사이에 커널 내부의 유한 버퍼가 있음

유한 버퍼는 공유 자원이다.

경쟁 조건의 발생을 방지하기 위해 동기화가 필요하다.

```c
int buffer;
int count = 0; // 처음에는 비어있음

void put(int value) {
  assert(count == 0);
  count = 1;
  buffer = value;
}

int get() {
  assert(count == 1);
  count = 0;
  return buffer;
}
```

공유 버퍼에 값을 넣는 루틴, 꺼내는 루틴 두 개가 있다.

- put() 루틴

  버퍼가 있다고 가정하고 (assert 문으로 확인도 함), 값을 공유 버퍼에 넣은 후, count를 1로 설정

- get() 루틴

  버퍼가 찼는지 확인하고, 값을 꺼낸 후 버퍼가 비었다고 설정.

  count를 0으로 설정, 읽은 값은 리턴한다.

일단 버퍼의 크기가 1이라고 가정해보자. (나중에 크기를 키우자)

버퍼에 데이터를 넣거나 버퍼의 데이터를 꺼내도 괜찮은지를 판단하는 루틴을 작성해야 한다.

판단하는 조건은 버퍼의 count가 0이면(즉, 버퍼가 비어 있다면) 데이터를 넣고,

count가 1이면 (버퍼가 가득차면) 버퍼에서 데이터를 꺼낸다.

생산자/소비자 문제에서는 두 종류의 쓰레드들이 존재한다.

1. 생산자 쓰레드들
2. 소비자 쓰레드들

아래의 코드는 생산자/소비자 쓰레드를 나타낸다.

```c
void *producer(void *arg) {
  int i;
  int loops = (int) arg;
  for (i = 0; i < loops; i++) {
    put(i);
  }
}

void *consumer(void *arg) {
  int i;
  while (1) {
    int tmp = get();
    printf("%d\n", tmp);
  }
}
```

이 코드는 제대로 동작하지 않는다.

공유 변수에서 경쟁조건이 발생하기 때문이다.

### 불완전한 해답

생산자와 소비자가 각 하나씩 있다고 가정한다.

- put() 과 get() 루틴에는 임계 영역이 존재한다.

  - put() 은 버퍼에 내용을 기록
  - get() 은 버퍼에 있는 내용을 읽음

- 임계 영역을 락으로 보호하는 것만으로는 제대로 동작하지 않는다.

  추가적인 장치가 필요하다. ( **컨디션 변수가 필요하다.** )

```c
int loops; // 초기화하는 것 잊지 말기
cond_t cond;
mutex_t mutex;

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    Ptrhead_mutex_lock(&mutex);           // p1
    if (count == 1)                       // p2
      Pthread_cond_wait(&cond, &mutex);   // p3
    put(i);                               // p4
    Pthread_cond_signal(&cond);           // p5
    Pthread_mutex_unlock(&mutex);         // p6
  }
}

void *consumer(void *arg) {
  int i;
  for (i = 0; i< loops; i++) {
    Pthread_mutex_lock(&mutex);           // c1
    if (count == 0)                       // c2
      Pthread_cond_wait(&cond, &mutex);   // c3
    int tmp = get();                      // c4
    Pthread_cond_signal(&cond);           // c5
    Pthread_mutex_unlock(&mutex);         // c6
    printf("%d\n", tmp);                  // c7
  }
}
```

생산자와 소비자가 각각 하나씩이면 문제가 없다.

1. (p1 - p3) 생산자는 버퍼가 빌 때까지 기다린다
2. (c1 - c3) 소비자는 버퍼에 내용이 기록될 때를 기다린다 

그러나 같은 종류의 쓰레드가 두 개 이상 있다면 두가지 문제가 생긴다.

### 첫번째 문제: 대기 명령 전 if 문과 관련

- T(c1), T(c2) 두 개의 소비자가 있고

- T(p) 하나의 생산자가 있다.

---

1. 소비자 T(c1) 이 먼저 실행된다.

2. 락(c1) 을 획득하고 버퍼를 소비할 수 있는지 검사한다(c2)

3. 비어있음을 확인한 후, 대기하며 (c3) 락을 해제한다.

4. 생산자 T(p) 가 실행된다.

5. 락을 획득하고(p1)

6. 버퍼가 비었는지 확인한다(p2)

7. 비어있음을 발견하고, 버퍼를 채운다(p4)

8. 생산자는 버퍼가 가득 찼다는 시그널을 보낸다(p5)

9. 대기 중인 첫번째 소비자 T(c1)이 꺠어나 준비 큐 (ready queue)로 이동한다.

10. T(c1) 은 이제 실행할 수 있는 상태이지만 아직 실행 상태는 아니다.

11. 생산자는 실행을 계속한다.

12. 버퍼가 차 있으므로 대기 상태로 전이한다 (p6, p1-p3)

13. **문제 발생**

    다른 소비자 T(c2) 가 끼어들어서 실행하면서 버퍼 값을 소비한다.

    (c1, c2, c4, c5, c6 를 수행, c3는 버퍼가 가득 찼기 때문에 건너뜀)

14. T(c2) 가 버퍼를 소비한 직후, T(c1) 이 실행된다고 해보자.

15. 대기에서 리턴하기 전에 락을 획득한다.

16. 그리고 get() 을 호출하지만(c4), 버퍼는 이미 비었다.

---

코드가 의도한대로 기능하지 못했다.

생산자가 버퍼에 기록한 값을 T(c2) 가 끼어들어서 소비했다.

T(c1) 가 빈 버퍼를 읽는 행위를 막았어야 했다.

---

### 첫번째 문제: 문제의 원인

T(c1) 이 시그널을 받는 시점, 즉 대기상태에서 깨어나는 시점과 이 쓰레드가 실제로 실행되는 시점사이에 시차가 존재한다.

이 기간 동안에 버퍼 상태가 변경되었다는 것이 문제이다.

시그널은 쓰레드를 깨우는 역할을 한다.

"깨운다" 라는 것은 쓰레드의 상태를 대기상태(blocked) 에서 준비상태 (ready) 로 변경하는 것이다.

깨어난 쓰레드가 실행되는 (running) 시점에서는 버퍼의 상태가 다시 변경될 수도 있다. (blocked -> ready -> 버퍼변경 -> running)

시그널을 받았다는 것은, 해당 쓰레드가 실행될 때, 버퍼의 상태가 원하는 상태일 수도 있다는 힌트로 보아야 한다. (확신하면 안된다.)

깨어난 쓰레드가 실제 실행될 때 시그널을 받았던 시점의 상태가 그대로 유지되어있는지를 다시 체크해야 한다.

이런 식으로 시그널을 정의하는 것을 **Mesa semantic** 이라고 한다.

> 이와 대비되는 시그널의 개념으로 **Hoare semantic** 이 있다.
>
> 시그널에서는 시그널을 받아서 깨어난 쓰레드가 실행될때, 시그널 방생시점의 조건이 유지된다는 것을 보장한다.
>
> Hoare semantic 의 구현이 더 어렵다.
>
> 대부분의 시스템이 Mesa semantic 을 채용하고 있다.
>
> https://youtu.be/5R9u16q4IcI 이것과 관련해서 고민하시는 현업자의 의견
>
> Windows 에서는 Mesa semantic 을 채용한 것 같다.

### 개선된, 하지만 아직도 불완전한: if 문 대신 while 문

```c
int loops; // 초기화하는 것 잊지 말기
cond_t cond;
mutex_t mutex;

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    Ptrhead_mutex_lock(&mutex);           // p1
    while (count == 1)                    // p2
      Pthread_cond_signal(&cond, &mutex); // p3
    put(i);                               // p4
    Pthread_cond_signal(&cond);           // p5
    Pthread_mutex_unlock(&mutex);         // p6
  }
}

void *consumer(void *arg) {
  int i;
  for (i = 0; i< loops; i++) {
    Pthread_mutex_lock(&mutex);           // c1
    while (count == 0)                    // c2
      Pthread_cond_wait(&cond, &mutex);   // c3
    int tmp = get();                      // c4
    Pthread_cond_signal(&cond);           // c5
    Pthread_mutex_unlock(&mutex);         // c6
    printf("%d\n", tmp);                  // c7
  }
}
```

Mesa semantic 의 컨디션 변수에서 가장 기본적인 법칙은 **언제나 while 문을 사용** 하라는 것이다.

때로는 조건을 재확인하지 않아도 되지만 항상 검사하는 것이 안전하다.

**무조건 다시 검사하고 마음 편히 살자.**

그러나 여전히 코드는 완벽하지 않다.

### 두번째 문제: 컨디션 변수가 하나뿐

이 문제는 소비자 쓰레드 T(c1) 과 T(c2) 가 먼저 실행한 후에 둘 다 대기 상태에 있을 때 발생한다(c3).

1. 생산자가 실행되어 버퍼에 값을 넣고 대기 중인 쓰레드 하나를 깨우고, (T(c1) 을 깨웠다고 하자)

2. 생산자가 루프를 다시 수행하며 락을 해제하고 다시 획득하고 버퍼에 데이터를 추가하려고 시도한다.

3. 버퍼가 꽉 찼기 때문에 생산자는 조건을 기다려야 하고 따라서 대기 상태로 들어간다.

4. 현재 한 소비자는 준비 상태에 있고 T(c1), 두 쓰레드는 조건을 기다리며 대기 중이다 T(c2), T(p)

5. **문제 발생**

   소비자 T(c1) 이 wait() 에서 리턴을 받아 깨어나고(c3) 조건을 재확인한다(c2).

   버퍼가 차있다는 것을 발견하고 값을 소비한다(c4)

   이 소비자는 시그널을 전송하여(c5) 대기중인 쓰레드 중 하나를 (딱 하나만) 깨운다.

   이 때 어떤 쓰레드를 깨울 것인가?

---

소비자가 버퍼를 비웠기 때문에 생산자를 당연히 깨워야 한다.

하지만, 만약 생산자를 깨우지 않고, 소비자 T(c2) 를 깨운다면 (다들 하나의 큐를 공유하기 때문에 큐의 상태에 따라 발생 가능)

문제가 발생한다.

만약 소비자 T(c2) 를 깨운다면, 버퍼가 비어 있다는 것을 발견한 후(c2) 다시 대기 상태로 들어간다(c3).

버퍼에 값을 넣어야 하는 생산자 T(p) 는 대기 중이다.

소비자 쓰레드 T(c1) 역시 대기 상태에 들어간다.

세 개의 쓰레드가 모두 대기 상태이다.

시그널을 받는 대상을 명시하는 것이 실질적으로 가능하지 않다.

우리가 원하는 것은 소비자는 생산자만을, 반대로 생산자는 소비자만을 깨우는 것이다.

### 단일 버퍼 생산자/소비자 해법

이 문제의 해결 방법은 간단하다.

두 개의 컨디션 변수를 사용하면 된다.

```c
cond_t empty, fill;
mutex_t mutex;

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    Ptrhead_mutex_lock(&mutex);
    while (count == 1)
      Pthread_cond_wait(&empty, &mutex);
    put(i);
    Pthread_cond_signal(&fill);
    Pthread_mutex_unlock(&mutex);
  }
}

void *consumer(void *arg) {
  int i;
  for (i = 0; i< loops; i++) {
    Pthread_mutex_lock(&mutex);
    while (count == 0)
      Pthread_cond_wait(&fill, &mutex);
    int tmp = get();
    Pthread_cond_signal(&empty);
    Pthread_mutex_unlock(&mutex);
    printf("%d\n", tmp);
  }
}
```

생산자 쓰레드가 empty 조건 변수에서 대기하고, fill 에 대해서 시그널을 발생한다.

반대로 소비자 쓰레드는 fill 에 대해서 대기하고 empty 에 대해서 시그널을 발생시킨다.

소비자가 실수로 다른 소비자를 깨울 일이 없고,

생산자도 다른 생산자를 깨우는 일이 절대 없다.

### 올바른 생산자/소비자 해법

이제 제대로 동작하는 생산자/소비자 해법을 얻었다.

그러나 가정했던 버퍼의 크기(1)가 너무 작다.

버퍼를 확장하여 대기 상태에 들어가기 전에 여러 값들이 생산될 수 있도록,

여러개의 값이 대기 상태 전에 소비될 수 있도록 하자.

하나의 생산자와 소비자의 경우에는 버퍼크기가 증가하면 쓰레드 간의 문맥 교환이 줄어들어, 더 효율적이게 된다.

멀티 생산자의 경우, 또는 멀티 소비자의 경우, 또는 둘 다 인경우, 생산과 소비가 병행이 될 수 있기 때문에,

병행성이 좋아진다.

올바른 해법으로 가기 위한 첫 번째 수정 사항은 다음처럼 버퍼 구조와 put(), get() 을 변경하는 것이다.

```c
int buffer[MAX];
int fill = 0;
int use = 0;
int count = 0;

void put(int value) {
  buffer[fill] = value;
  fill = (fill + 1) % MAX;
  count++;
}

int get() {
  int tmp = buffer[use];
  use = (use + 1) % MAX;
  count--;
  return tmp;
}
```

```c
cond_t empty, fill;
mutex_t mutex;

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    Ptrhead_mutex_lock(&mutex);                 // p1
    while (count == MAX) // 이 부분이 변경 되었다    // p2
      Pthread_cond_wait(&empty, &mutex);        // p3
    put(i);                                     // p4
    Pthread_cond_signal(&fill);                 // p5
    Pthread_mutex_unlock(&mutex);               // p6
  }
}

void *consumer(void *arg) {
  int i;
  for (i = 0; i< loops; i++) {
    Pthread_mutex_lock(&mutex);                 // c1
    while (count == 0)                          // c2
      Pthread_cond_wait(&fill, &mutex);         // c3
    int tmp = get();                            // c4
    Pthread_cond_signal(&empty);                // c5
    Pthread_mutex_unlock(&mutex);               // c6
    printf("%d\n", tmp);
  }
}
```

생산자는 모든 버퍼가 가득 차 있으면, 대기 상태로 들어가고(p2)

소비자도 모든 버퍼가 비어 있다면 대기에 들어간다(c2)

이렇게 하여 생산자/소비자 문제에 대한 범용적인 답을 구했다.

## 3. 포함 조건 (Covering Condition)

컨디션 변수가 사용되는 재미있는 경우를 살펴보자.

```c
cond_t empty, fill;
mutex_t mutex;

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    Pthread_mutex_lock(&mutex);           // p1
    while (count == MAX)                  // p2
      Pthread_cond_wait(&empty, &mutex);  // p3
    put(i);                               // p4
    Pthread_cond_signal(&fill);           // p5
    Pthread_mutex_unlock(&mutex);         // p6
  }
}

void *consumer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    Pthread_mutex_lock(&mutex);           // c1
    while (count == 0)                    // c2
      Pthread_cond_wait(&fill, &mutex);   // c3
    int tmp = get();                      // c4
    Pthread_cond_signal(&empty);          // c5
    Pthread_mutex_unlock(&mutex);         // c6
    printf("%d\n", tmp);
  }
}
```

메모리 할당을 요청한 쓰레드는 메모리 공간이 생길 때까지 대기한다.

응용 프로그램이 메모리를 해제하고 반납하면, 가용 메모리 공간의 발생을 알리는 시그널을 생성한다.

**여기서 문제가 발생한다**

다수의 쓰레드가 메모리 공간의 발생을 대기하고 있는 경우, 어떤 쓰레드를 깨워야 하는가?

시그널을 생성하는 쓰레드는 시그널 수신 대상자를 명시할 수 없다.

다음과 같은 경우를 생각해보자.

1. 현재 빈 공간이 없다

2. 쓰레드 T(a) 가 allocate(100) 을,

3. 쓰레드 T(b) 가 allocate(10) 을 호출하였다.

4. 쓰레드 T(a), T(b) 는 대기 상태가 된다. (가용 메모리 공간이 없기 때문)

5. 이 시점에서 쓰레드 T(c) 가 세 번째로 free(50)을 호출

   50 바이트 크기의 가용 공간이 발생

6. 쓰레드 T(c) 는 가용공간의 발생을 알리는 시그널을 생성

7. 이상적으로는 해당 시그널이 10 byte 공간을 필요로 하는 T(b) 에게 전달되고,

   T(b)가 깨어나야 한다.

8. 하지만 이 시그널이 T(a)에게 전달될 수도 있다.

   해당 시그널을 어떤 쓰레드에게 전달할지는 전적으로 운영체제의 몫이다.

9. 만약 T(a) 가 깨어나게 되면

   T(a) 는 발생한 메모리 공간의 크기를 파악한 후, 다시 대기상태로 들어간다.

10. 따라서 이 코드는 제대로 동작하지 않는다.

**이 문제의 핵심은 시그널의 본질에 근간한다.**

**컨디션 변수에서의 시그널 함수는 시그널 수신자를 명시하지 않는다.**

---

Lampson 과 Redell 은 이 문제에 대한 간단한 해결법을 제시했다.

컨디션 변수에서 대기중인 **모든 쓰레드에게 시그널을 다 전송** 하는 것이다.

`pthread_cond_signal()` 을 대기 중인 모든 쓰레드를 깨우는

`pthread_cond_broadcast()` 로 바꿔서 사용하면 된다.

깨어난 쓰레드들은 차례로 실행되면서, 자신이 대기했었던 조건이 만족되는지 검사한다.

조건이 만족되지 않았다면 다시 대기모드로 들어간다.

만약 조건이 만족되었다면, 해당 쓰레드는 wait() 에서 리턴하여 실행을 계속한다.

어떤 쓰레드가 wait() 에서 리턴하여 실행될 경우, 해당 쓰레드는 락을 획득한 상태가 된다.

나머지 쓰레드들은 wait() 에서 여전히 대기해야 한다.

이 방식은 **단점** 이 있다.

1. 다수의 쓰레드를 불필요하게 깨울 수 있다.
2. 불필요한 문맥전환이 많이 발생할 수 있다.

이런 경우를 **포함 조건(covering condition)** 이라고 한다.

보수적으로 쓰레드가 깨어나야 하는 모든 경우를 다 포함하기 때문이다.

---

