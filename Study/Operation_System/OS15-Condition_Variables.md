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

