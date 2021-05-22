# Semaphore

> **핵심 질문: 세마포어를 어떻게 사용하는가**
>
> 락과 컨디션 변수 대신에 세마포어를 사용하는 방법은 무엇인가?
>
> 세마포어의 정의는 무엇인가?
>
> 이진(binary) 세마포어는 무엇인가?
>
> 락과 컨디션 변수를 사용하여 세마포어를 만드는 것이 가능한가?
>
> 그 반대로 세마포어를 사용하여 락과 조건 변수를 만드는 것이 가능한가?

## 1. 세마포어: 정의

세마포어는 정수 값을 갖는 객체이다.

- 두 개의 루틴으로 조작할 수 있다.

  - sem_wait()
  - sem_post()

- 세마포어는 초기값에 의해 동작이 결정되기 때문에, 사용하기 전 초기화를 해야한다.

  ```c
  #include <semaphore.h>
  sem_t s;
  sem_init(&s, 0, 1); // 1로 초기화
  ```

  - 세마포어 s를 선언하고, 1로 초기화

  - sem_init 의 두 번째 인자는 0. (모든 예제에서 0으로 사용할 것임)

    0은 같은 프로세스 내의 쓰레드 간에 세마포어를 공유한다는 의미.

### sem_wait()

```c
int sem_wait(sem_t *s) {
  decrement the value of semaphore s by one;
  wait if value of semaphore s is negative;
}
```

- 세마포어의 값이 1 이상일때 sem_wait() 이 호출되면, **대기하지 않고 리턴한다.**

- 세마포어의 값이 0 이하이면, 호출자를 대기시킨다.

  0이면, sem_wait이 세마포어 값을 1 감소시켜서 -1이 된다.

  내부에서는 세마포어값이 음수이므로 대기한다.

- 세마포어의 값이 음수이면, 세마포어의 값이 기다리는 쓰레드의 수를 의미하게 된다. (-3 이면 3개의 쓰레드가 대기 중)

- 원자적으로 작동한다.

### sem_post()

```c
int sem_post(sem_t *s) {
  increment the value of semaphore s by one;
  if there are one or more threads waiting, wake one;
}
```

- 세마포어의 값을 증가시킨다.
- 만약 깨어나길 기다리는 쓰레드가 있으면, 그 중 하나를 깨운다.

## 2. 이진 세마포어 (락)

우리가 처음으로 세마포어를 적용할 곳은 이미 친숙한 "락" 이다.

X의 값이 얼마가 되어야 하겠는가?

```c
sem_t m;
sem_init(&m, 0, X); // X로 초기화하기. 이때 X가 가져야 할 값은?

sem_wait(&m);
// ------------------
// 임계 영역을 여기에 배치
// ------------------
sem_post(&m);
```

락은 한번에 하나만 임계영역에 접근할 수 있어햐 하므로,

X는 1이 되어야 한다.

락은 두 개의 상태(사용 가능, 사용 중)만 존재하므로 **이진 세마포어 (binary semaphore)** 라고도 불린다.

## 3. 순서 보장을 위한 세마포어

이전에 컨디션 변수를 사용했던 것과 유사하게 세마포어를 순서를 정하기 위한 기본 도구로 사용할 수 있다.

부모 쓰레드가 자식 쓰레드를 생성한 후, 자식 쓰레드의 종료를 대기하고자 한다.

```sh
parent: begin
child
parent: end
```

세마포어를 이용하여 어떻게 이 효과를 만들 수 있을까?

부모 프로세스는 자식 프로세스 생성 후 sem_wait() 를 호출하여 자식의 종료를 대기한다.

자식은 sem_post() 를 호출하여 종료되었음을 알린다.

세마포어 값을 무엇으로 초기화할까?

```c
sem_t s;

void *child(void *arg) {
  printf("child\n");
  sem_post(&s); // 시그널 전달: 자식의 동작이 끝남
  return NULL;
}

int main(int argc, char *argv[]) {
  sem_init(&s, 0, X); // X의 값은 무엇이 되어야 할까?
  printf("parent: begin\n");
  pthread_t c;
  Pthread_create(c, NULL, child, NULL);
  sem_wait(&s); // 자식을 여기서 대기
  printf("parent: end\n");
  return 0;
}
```

답은 0 이다.

## 4. 생산자/소비자 (유한 버퍼) 문제

### 첫 번째 시도

empty 와 full 이라는 두 개의 세마포어를 사용한다.

- empty

  가용 버퍼 공간이 있는지 여부 표시

- full

  읽을 내용이 있는지 여부 표시

- Producer: put()

  데이터를 넣기 위해 버퍼가 empty 가 되기를 기다린다.

- Consumer: get()

  사용하기 전에, 버퍼가 fill 되기를 기다린다.

```c
int buffer[MAX];
int fill = 0;
int use = 0;

void put(int value) {
  buffer[fill] = value;
  fill = (fill + 1) % MAX;
}

int get() {
  int tmp = buffer[use];
  use = (use + 1) % MAX;
  return tmp;
}
```

```c
sem_t empty;
sem_t full;

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    sem_wait(&empty);
    put(i);
    sem_post(&full);
  }
}

void *consumer(void *arg) {
  int i, tmp = 0;
  while (tmp != -1) {
    sem_wait(&full);
    tmp = get();
    sem_post(&empty);
    printf("%d\n", tmp);
  }
}

int main(int argc, char *argv[]) {
  // ...
  sem_init(&empty, 0, MAX); // MAX 버퍼는 비어 있는 상태로 시작
  sem_init(&full, 0, 0); // ... 그리고 0이 가득 참
}
```

문제가 있다.

- 만약 MAX 가 1보다 크다면?
  - producer 가 여려명 있다면, **race condition** 이 발생한다.
- **mutual exclusion** 을 잊었다.
  - 버퍼를 채우는 과정과, index를 증가시키는 과정이 **critical section** 이다.
- 즉, put() 과 get() 이 원자적으로 동작해야 한다.

### 해법: Mutual Exclution 의 추가

```c
sem_t empty;
sem_t full;
sem_t mutex; // 추가됨

void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    sem_wait(&mutex);
    sem_wait(&empty);
    put(i);
    sem_post(&full);
    sem_post(&mutex);
  }
}

void *consumer(void *arg) {
  int i, tmp = 0;
  while (tmp != -1) {
    sem_wait(&mutex);
    sem_wait(&full);
    tmp = get();
    sem_post(&empty);
    sem_post(&mutex);
    printf("%d\n", tmp);
  }
}
```

아직 잘 작동하지 않는다!

deadlock 이 발생한다.

1. mutex 를 잡은 상태로 wait(empty), 또는 wait(full) 에서 잠들면
2. 아무도 mutex 못들어감
3. 깨워주는 부분(post)이 mutex 안에 있으므로 아무도 못깨워줌

### 마침내, 제대로 된 해법

```c
void *producer(void *arg) {
  int i;
  for (i = 0; i < loops; i++) {
    sem_wait(&empty);
    sem_wait(&mutex); // 옮김
    put(i);
    sem_post(&mutex); // 옮김
    sem_post(&full);
  }
}

void *consumer(void *arg) {
  int i, tmp = 0;
  while (tmp != -1) {
    sem_wait(&full);
    sem_wait(&mutex); // 옮김
    tmp = get();
    sem_post(&mutex); // 옮김
    sem_post(&empty);
    printf("%d\n", tmp);
  }
}

int main(int argc, char *argv[]) {
  // ...
  sem_init(&empty, 0, MAX); // MAX 버퍼는 비어 있는 상태로 시작
  sem_init(&full, 0, 0); // ... 그리고 0이 가득 참
  sem_init(&mutex, 0, 1); // 락이기 때문에 1
  // ...
}
```

## 5. Reader-Writer 락

어떤 자료구조에

- 자료를 읽기만 하는 reader
- 자료를 변화시키는 writer

가 접근한다고 하자.

- reader 는 읽기만 하므로, 여러명이 동시에 읽어도 된다.
- writer 는 쓰는동안 혼자 있어야 한다. (다른 reader도 접근해서는 안된다.)

```c
typedef struct _rwlock_t {
  sem_t lock; // 이진 세마포어(기본 락)
  sem_t writelock; // 하나의 wrtier/여러 reader 허용
  int readers; // 임계 영역 내에 읽기를 수행 중인 reader의 수
}

void rwlock_init(rwlock_t *rw) {
  rw->readers = 0;
  sem_init(&rw->lock, 0, 1);
  sem_init(&rw->writelock, 0, 1);
}

void rwlock_acquire_readlock(rwlock_t *rw) {
  sem_wait(&rw->lock);
  //------- critical section ------------
  rw->readers++;
  if (rw->readers == 1)
    sem_wait(&rw->wrtielock); // 첫번째 reader가 writelock을 획득
  //--------------------------------------
  sem_post(&rw->lock);
}

void rwlock_release_readlock(rwlock_t *rw) {
  sem_wait(&rw->lock);
  //------- critical section ------------
  rw->readers--;
  if (rw->readers == 0)
    sem_post(&rw->writelock); // 마지막 reader가 wrtielock 해제
  //--------------------------------------
  sem_post(&rw->lock);
}

void rwlock_acquire_writelock(rwlock_t *rw) {
  sem_wait(&rw->writelock);
}

void rwlock_release_writelock(rwlock_t *rw) {
  sem_post(&rw->writelock);
}
```

의도한대로 동작한다.

그러나 단점이 있다.

공정성(fairness)에 문제가 있다.

상대적으로 쓰기 쓰레드가 너무 불리하다. 쓰기 쓰레드에게 starvation 문제가 발생하기 쉽다.

> 참고로, 해결법은 쓰기 쓰레드가 대기 중일때는 읽기 쓰레드가 락을 획득하지 못하게 하면 된다.

## 6. 식사하는 철학자 (The Dining Philosophers)

실제 활용도는 매우 낮은 문제이다.

그러나 공룡책에서 너무나 유명한 예제였어서 유명해졌다.

면접 질문에 등장하기도 한다.

- 5명의 철학자들이 둘러 앉았다.
- 각 철학자들 사이에 하나씩의 포크가 있어, 총 5개의 포크가 있다.
- 철학자가 식사를 하기 위해서는 양손에 하나씩, 두개의 포크를 잡아야 한다.

기본동작은 다음과 같다.

```c
while (1) {
  think();
  getforks();
  eat();
  putforks();
}
```

```c
// helper functions
int left(int p) {
  return p;
}

int right(int p) {
  return (p + 1) % 5;
}
```

만약 다음과 같이 행동한다면?

```c
void getforks()  {
  sem_wait(forks[left(p)]);
  sem_wait(forks[right(p)]);
}

void putforks() {
  sem_post(forks[left(p)]);
  sem_post(forks[right(p)]);
}
```

데드락이 발생한다.

- 다들 왼손의 포크를 동시에 먼저 잡고 오른손포크를 잡으려고 대기중이라면?
- 모든 철학자들이 영원히 왼손에만 포크를 쥔채 아무것도 못한다.

### 해결법

포크를 잡는법을 바꾸면 된다.

이전에는 다들 왼손잡이라서 왼쪽에 있는 포크를 먼저 집었다. 그래서 문제가 발생했다.

이번에는 한명이 오른손잡이면 된다.

```c
void getforks() {
  if (p == 4) {
    sem_wait(forks[right(p)]);
    sem_wait(forks[left(p)]);
  }
  else {
    sem_wait(forks[left(p)]);
    sem_wait(forks[right(p)]);
  }
}
```

모두가 포크를 하나만 쥐고 버티는 상황이 없어진다.

---