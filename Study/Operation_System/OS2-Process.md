

# Process

**핵심질문: CPU가 여러 개 존재한다는 환상을 어떻게 제공하는가?**

**답: 가상화**

> 시분할, 공간분할, 스케쥴링

---

## 1. 프로세스란?

> 프로세스란 보이지 않는 개념이다.
>
> 프로세스는 code+state 의 개념이다.

---

## 2. 프로세스 API

아래의 API들은 모든 현대 운영체제에서 제공된다.

- 생성 (Create)

  > 새로운 프로세스를 생성

- 제거 (Destroy)

  > 프로세스를 강제로 종료하는 수단

- 대기 (Wait)

  > 때론 어떤 프로세스의 실행 중지를 기다릴 필요가 있다. 다양한 종류의 대기 인터페이스가 제공된다.

- 각종 제어 (Miscellaneous Control)

  > 프로세스의 일시정지, 재개 등의 다양한 기능을 제공

- 상태 (Status)

  > 프로세스의 상태 정보를 얻어내는 인터페이스. 얼마 동안 실행되었는지, 어떤 상태인지 등.

---

## 3. 프로세스 생성

1. 프로그램 코드를 프로세스의 메모리(address space)에 로드한다.

   > 과거의 운영체제들은 프로그램 실행 전에 한번에 코드와 데이터를 모두 메모리에 로드했다.
   >
   > 현대의 운영체제들은 프로그램을 실행하면서 코드나 데이터가 필요할 때 필요한 부분만 로드한다.
   >
   > 이를 위해서는 **paging** 과 **swapping** 기법을 이해해야한다. 추후 메모리 가상화에서 자세히 설명.

2. 프로그램의 스택이 할당된다.

   > C언어의 경우, 지역변수, 함수파라미터, 리턴주소 등을 저장하기 위해 **stack**을 사용한다.
   >
   > **main()** 함수의 파라미터인 **argc** 와 **argv**를 사용하여 **stack**을 초기화 한다.

3. 프로그램의 힙 영역이 생성된다.

   > 힙은 동적으로 할당된 데이터를 저장하기 위해 사용된다.
   >
   > **malloc()** 으로 필요한 공간 요청, **free()** 로 사용했던 공간을 반환한다.
   >
   > 참고: 실제 운영체제의 커널 코드 등에서는 **malloc()**함수를 잘 사용하지 않고 자체 정의 함수등을 사용한다.
   >
   > **malloc()** 함수의 오버헤드가 너무 크기 때문이다.

4. OS가 입출력 관련 초기화 작업을 수행한다.

   > **UNIX**는 **STDIN**, **STDOUT**, **STDERR** 장치에 해당하는 세 개의 **file descriptor**를 갖는다.
   >
   > 이 **file descriptor**를 사용해 프로그램은 터미널로부터 입력을 읽고 화면에 출력을 프린트 작업을 쉽게 할 수 있다.
   >
   > 입출력, file descriptor 에 관해서는 영속성에관한 부분에서 더 자세히 설명.

5. 프로그램을 entry point (main 함수) 부터 실행한다.

   > 프로그램의 시작 지점인 **main()** 함수 루틴으로 분기함으로써, 운영체제는 CPU를 새로 생성된 프로세스에 넘기고, 프로그램이 실행된다.

---

## 4. 프로세스의 상태 (state)

프로세스 상태를 단순화하면 다음 세 상태가 있다.

- 실행 (Running)

  > 프로세스는 명령어를 실행하고 있다.

- 준비 (Ready)

  > 프로세스는 실행될 준비가 되어있지만, OS가 다른 프로세스를 실행하고 있거나 하는 등의 이유로 대기중이다.

- 대기 (Blocked)

  > 프로세스가 다른 사건을 기다리는 동안 프로세스의 수행을 중단시키는 연산.
  >
  > CPU를 줘도 지금 일 할 수 있는 상태가 아닌 프로세스이다.
  >
  > 예) 프로세스가 디스크에 입출력 요청을 했을 때, 10초에 한번 실행되는 프로세스 등.



> 예) 프로세스는 운영체제의 스케줄링 정책에 따라 스케줄되면 **준비** 상태에서 **실행** 상태가 된다.
>
> **실행** 상태에서 입출력 요청을 하면, **대기** 상태가 된다.
>
> **대기** 상태에서 입출력이 완료되면 다시 **준비** 상태가 된다.
>
> **준비** 상태에서 다시 프로세스는 운영체제의 스케줄링을 기다린다.



만약 **프로세스0**가 입출력요청으로 인해 **대기** 상태가 되면, 놀고있는 CPU를

그 동안 새롭게 **프로세스1**을 스케줄링해서 **실행** 하는것이 반드시 옳을까?

> 운영체제는 스케줄러를 통해 이를 결정하는데, 이후 자세히 다룬다.

---

## 5. 자료구조 (Data Structures)

운영체제도 일종의 프로그램이다. 운영체제도 다른 프로그램과 마찬가지로 다양한 정보를 유지하기 위한 자료구조를 가지고 있다.

예를들어 준비 상태를 위한 자료구조 프로세스 리스트가 있고, 마찬가지로 실행, 대기 상태를 위한 자료구조도 있다.



- 레지스터 문맥 (register context) 자료구조

  > 프로세스가 중단되었을 때 해당 프로세스의 레지스터 값들을 저장.
  >
  > 후에 이 레지스터 값들을 복원해서 운영체제는 프로세스 실행을 재개.
  >
  > 이를 **문맥 교환 (context switch)** 라고 한다. 추후 자세히 설명.



**실행**, **준비**, **대기** 외에 다른 상태들도 존재한다.

- **초기(initial)** 상태

  > 프로세스가 완전히 생성되기 전까지의 상태.

- **최종(final)** 상태 ==  **좀비(zombie)** 상태

  > 프로세스는 종료되었지만 해당 프로세스가 사용하던 각종 자원이 아직 완전히 반납되지 않은 상태.
  >
  > UNIX 기반 시스템에서는 **좀비(zombie)** 라고 부른다.
  >
  > 보통 하나의 프로세스가 다른 프로세스가 성공적으로 실행을 마쳤는지 파악하는데 유용하게 사용된다.
  >
  > > 예) **부모(parent)** 프로세스가 **자식(child)** 프로세스 실행 마쳤는지 확인할 때.
  > >
  > > **부모** 프로세스는 **자식** 프로세스의 종료를 대기하는 **시스템 콜**( 예, **wait()** )을 호출한다.



xv6 코드를 통해 보면



프로세스를 중단하고 이후 재개하기 위해 저장하고 복원하는 레지스터.

```c
struct context
{
  int eip;
  int esp;
  int ebx;
  int ecx;
  int edx;
  int esi;
  int edi;
  int ebp;
};
```



가능한 프로세스 상태.

```c
enum proc_state { UNUSED, EMBRYO, SLEEPING, RUNNABLE, RUNNING, ZOMBIE };
```



레지스터 문맥과 상태를 포함하여 각 프로세스에 대하여 xv6가 추적하는 정보.

```c
struct proc
{
  char *mem;										// 프로세스 메모리 시작주소
  uint sz;											// 프로세스 메모리의 크기
  char *kstack;									// 이 프로세스의 커널 스택의 바닥 구조
  enum proc_state state;				// 프로세스 상태
  int pid;											// 프로세스 ID
  struct proc *parent;					// 부모 프로세스
  void *chan;										// 0이 아니면, chan에서 수면
  int killed;										// 0이 아니면 종료됨
  struct file *ofile[NOFILE];		// 열린 파일
  struct inode *cwd;						// 현재 디렉터리
  struct context context;				// 프로세스를 실행시키려면 여기로 교환
  struct trapframe *tf;					// 현재 인터럽트에 해당하는 트랩 프레임
};
```

>process list (process control block, 즉 PCB 가 링크드 리스트처럼 서로 연결된 구조)의 PCB가 xv6에서는 struct proc의 형태로 구현되어 있다.
>
>PCB == Process Descriptor == struct proc 라고 생각하면 된다. (리눅스에서는 task_struct 이다)

---

## 6. 프로세스 API 의 실제 코드 구현

핵심질문: 프로세스를 생성하고 제어하는 법.

> 프로세스를 생성하고 제어하려면 운영체제가 어떤 인터페이스를 제공해야 하는가?
>
> 유용성, 편리성, 성능을 위해서는 어떻게 인터페이스를 설계해야 하는가?



### 6.1 fork ()

프로세스 생성에 fork() 시스템 콜이 사용된다.

**주의** : 시스템 콜 중 가장 이해하기 복잡하다. 

> parent 에는 자식의 PID를 리턴.
>
> 자식에게는 0이 리턴.



아래는 p1.c 파일에서 fork()를 호출하는 코드이다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unitsd.h>

int main (int argc, char *argv[])
{
  printf("hello world (pid:%d)\n", (int) getpid());
  int rc = fork();
  if (rc < 0) // fork failed; exit
  {
    fprintf(stderr, "fork failed\n");
    exit(1);
  }
  else if (rc == 0) // child (new process)
  {
    printf("hello, I am child (pid:%d)\n", (int) getpid());
  }
  else // parent goes down this path (main)
  {
    printf("hello, I am parent of %d (pid:%d)\n", rc, (int) getpid());
  }
  return 0;
}
```

```shell
prompt> ./p1
hello world (pid:29146)
hello, I am parent of 29147 (pid:29146)
hello, I am child (pid:29147)
prompt>
```

> 자식 프로세스는 부모 프로세스의 복사본이다.
>
> 자식 프로세스는 fork()를 호출하면서부터 시작된다.
>
> 그리고 자식 프로세스는 다시 main 함수의 첫부분부터 시작하지 않는다.
>
> 그렇다고 자식 프로세스와 부모 프로세스가 완전히 동일하지는 않다.
>
> 자식 프로세스는 자신의 주소 공간, 자신의 레지스터, 자신의 PC 값을 가진다.
>
> 그리고 가장 중요한 부분은 **fork() 시스템 콜의 반환 값**이다.
>
> **부모는 자식 프로세스의 PID, 자식은 0 을 반환받는다.**



그렇다면 **fork()** 이후, **자식 프로세스**가 먼저 실행될까? **부모 프로세스**가 먼저 실행될까?

알 수 없다. 정확히는 **CPU 스케줄러**에 의해 결정되는데, 스케줄러는 상황에 따라 다른 결과를 내므로 알 수 없다.

이런 **비결정성** 떄문에 **멀티 쓰레드 프로그램** 실행 시 많은 문제가 발생한다. 이는 **병행성** 부분에서 더 자세히 다룬다.



### 6.2 wait ()

그렇다면 확정적으로 부모 프로세스가 자식 프로세스의 종료를 기다렸다가 실행되게 하려면 어떻게 해야 할까?

이러한 동작을 위해 필요한 시스템 콜이 **wait ()**, **waitpid ()** 이다. (waitpid()는 더 많은 기능을 제공한다.)

위의 p1.c 코드에서 부모 프로세스 부분에 다음과 같이 wait()를 추가하면 반드시 동일한 순서로 출력값이 나오게 된다.

```c
  else // parent goes down this path (main)
  {
    int rc_wait = wait(NULL);// 추가된 부분
    printf("hello, I am parent of %d (pid:%d)\n", rc, (int) getpid());
  }
```

~~~sh
prompt> ./p1
hello world (pid:29266)
hello, I am child (pid:29267)
hello, I am parent of 29267 (pid:29266)
prompt>
~~~

> 이 코드를 실행시켜보면 반드시 자식 프로세스가 먼저 출력된다.
>
> 2가지 경우로 나누어 생각해보자.
>
> 1. **자식 프로세스가 먼저 실행될 경우:** 그냥 자연스럽게 자식 프로세스가 먼저 실행된다.
> 2. **부모 프로세스가 먼저 실행될 경우:** wait()을 호출한다. 이 wait() 시스템 콜은 ~~반드시~~ 자식 프로세스가 종료될 때까지 리턴하지 않는다. *(책의 저자의 주장과는 다르게 그 전에 리턴하는 경우가 있긴 하다. man 페이지 참고)* 그 후 자식 프로세스가 종료 되고 wait() 이 리턴하고, 부모 프로세스가 진행된다.



### 6.3 exec ()

자기 자신이 아닌 다른 프로그램을 실행해야 할 때 사용한다.

이전의 **p1.c** 코드는 **fork()**를 통해 자신의 복사본을 생성하여 실행했다.

자기 자신이 아닌 다른 코드를 실행하고 싶을 때는 **exec()** 을 사용한다.

exec () 시스템 콜은 다음과 같은 과정으로 수행된다.

1. 실행 파일의 이름과 인자가 주어지면 해당 실행 파일의 코드와 정적 데이터를 읽어 들여 현재 실행 중인 프로세스의 코드 세그먼트와 정적 데이터 부분을 덮어쓴다.
2. 힙과 스택 및 프로그램 다른 주소 공간들로 새로운 프로그램의 실행을 위해 다시 초기화된다.
3. 운영체제는 프로세스의 argv와 같은 인자를 전달하여 프로그램을 실행시킨다.

즉, 새로운 프로세스를 생성하는것이 아니라, 현재 실행중인 프로그램을 다른 프로그램으로 대체하는 것이다.

> 참고: 리눅스에는 여섯개의 exec () 시스템 콜이 있다.
>
> 모두 알 필요는 없고, 용도에 따라 다른 exec을 호출한다는 것을 알면된다. (궁금하면 man 페이지를 통해 알아봐라)
>
> - execl ()
>
> - execle ()
>
> - execlp ()
>
> - execv ()
>
> - execvp ()
>
> - execve ()



아래 **p3.c** 코드는 fork(), wait(), exec()을 사용한다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/wait.h>

int main (int argc, char *argv[])
{
  printf("hello world (pid: %d)\n", (int) getpid());
  int rc = fork();
  if (rc < 0) // 만약 fork 실패시
  {
    fprintf(stderr, "fork failed\n");
    exit(1);
  }
  else if (rc == 0) // 자식 프로세스 (새 프로세스)
  {
    printf("hello, I am child (pid:%d)\n", (int) getpid());
    char *myargs[3];
    myargs[0] = strdup("wc");		// 프로그램: "wc" (단어를 세는 프로그램)
    myargs[1] = strdup("p3.c");		// 인자: 단어를 셀 대상 파일
    myargs[2] = NULL;		// 배열의 끝 표시
    execvp(myargs[0], myargs);		// "wc" 실행
    printf("this shouldn't print out");		// exec을 통해 현재 프로세스를 덮어쓰기 때문에 이 문구는 출력되어서는 안된다.
  }
  else // 부모 프로세스
  {
    int rc_wait = wait(NULL);
    printf("hello, I am parent of %d (rc_wait:%d) (pid:%d)\n", rc, wc, (int)getpid());
  }
  return 0;
}
```

```shell
prompt> ./p3
hello world (pid:29383)
hello, I am child (pid:29384)
	29	107	1030	p3.c
hello, I am parent of 29384 (rc_wait:29384) (pid:29383)
prompt>
```

> 쉘의 동작과정에 대해 이해할 필요가 있다.
>
> 1. shell은 promt를 표시하고 사용자가 무언가를 입력하기를 기다린다.
> 2. 명령어를 입력한다. (에, 실행 프로그램의 이름과 필요한 인자 등)
> 3. shell은 파일 시스템에서 실행 파일의 위치를 찾고 명령어를 실행하기 위하여 fork()를 호출하여 새로운 자식 프로세스를 만든다.
> 4. exec() 의 변형 중 하나를 호출하여 프로그램을 실행시킨다.
> 5. wait()를 호출하여 명령어가 끝나기를 기다린다.
> 6. 자식 프로세스가 종료되면 shell은 wait() 으로부터 리턴하고 다시 promt를 출력하여 다음 명령어를 기다린다.



### 6.4 왜 이런 복잡한 과정으로 동작하는가?

**fork**와 **exec**을 나누지 않고 한번에 새로운 프로세스를 구성하면 되지 않을까?

> **fork()** 와 **exec ()** 을 나눈 이유는 **fork ()** 과정과 **exec ()** 과정 사이에 **shell**을 동작시키고 싶기 때문이다.
>
> **shell**을 통해 **fork** 와 **exec** 동작 중간에 끼어들어 **redirection**, **pipe**, ... 등 다양한 작업을 수행할 수 있다.

다음 예시를 보자

```shell
prompt> wc p3.c > newfile.txt
```

> **wc**의 결과가 직접 출력되지 않고, **newfile.txt** 라는 파일에 결과값이 저장된다.
>
> `>` 표시가 재지정을 의미한다. `>`를 통해 **wc** 프로그램의 출력방향을 표준출력에서 **newfile.txt**로 재지정한 것이다.
>
> 어떤 과정으로 이게 가능할까?
>
> **자식**이 생성되고 **exec()** 이 호출되기 전에 **표준 출력(standard output)** 파일을 닫고 **newfile.txt** 라는 파일을 연다.
>
> 위 과정을 거치면, **wc**의 출력은 모두 화면이 아닌, 파일에 전송된다.

아래 p4.c 코드는 입출력 재지정에 대해 다룬다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
#include <sys/wait.h>

int main (int argc, char *argv[])
{
  int rc = fork();
  if (rc < 0) // fork 실패 시
  {
    fprintf(stderr, "fork failed\n");
    exit(1);
  }
  else if (rc == 0) // 자식: 표준 출력 파일로 재지정
  {
    close(STDOUT_FILENO);
    open("./p4.output ", O_CREAT|O_WRONLY|O_TRUNC, S_IRWCU);
    
    //이제 exec "wc"...
    char *myargs[3];
    myargs[0] = strdup("wc");		// 프로그램: "wc" (단어를 세는 프로그램)
    myargs[1] = strdup("p4,c");		// 인자: 단어를 셀 대상 파일
    myargs[2] = NULL;		// 배열의 끝 표시
    execvp(myargs[0], myargs);		// wc 실행
  }
  else // 부모 프로세스
  {
    int rc_wait = wait(NULL);
  }
  return 0;
}
```

```shell
prompt> ./p4
prompt> cat p4.output
	32	109	846	p4.c
prompt>
```

> 이 출력 결과에서 p4를 실행하면 화면에 아무일도 일어나지 않는다는 점을 주목해야 한다.
>
> 그러나 실제로는 프로그램 p4는 다음의 과정을 실행했다.
>
> 1. **fork()**를 호출하여 새로운 자식 프로세스를 생성
> 2. **execvp()**를 호출
> 3. **wc** 프로그램을 실행
> 4. 출력이 **p4.output** 으로 재지정되어있어 ***화면에는 아무것도 출력되지 않음***
>
> **cat**을 통해 출력파일을 확인해 보면 위의 과정이 모두 잘 실행되었음을 확인 가능하다.

**UNIX**의 **pipe**가 이와 유사한 방식으로 구현된다.

> **pipe()** 시스템 콜을 통하여 ***한 프로세스의 출력***과 ***다른 프로세스의 입력***이 ***동일한 파이프***에 연결된다.
>
> 다시말해, ***한 프로세스의 출력이 다음 프로세스의 입력으로 사용***된다.

```shell
promt> grep -o foo file | wc -l
```



### 6.5 프로세스 제어

UNIX 시스템에는 fork(), exec(), wait() 외에도 많은 프로세스 관련 인터페이스가 존재한다.

kill () 시스템 콜은 프로세스에게 멈추기나 끝내기 같은 시그널(signal)을 보내는 데 사용된다.

> 편의를 위해서 대부분의 UNIX shell 은 현재 실행중인 프로세스에 특정 시그널을 보내는 단축키가 설정되어 있다.
>
> `control-c` 는 프로세스에게 SIGINT (인터럽트) 시그널을 보내여 종료시키는 단축키,
>
> `control-z` 는 프로세스에게 SIGSTP (멈춤) 시그널로 실행 도중에 프로세스를 잠시 멈추는 단축키이다.
>
> 이렇게 잠시 멈춘 프로세스를 재개하기 위해서는 `fg` 명령어를 사용하면 된다. 

---

## 7. 여담

이 책의 저자는 **UNIX 시스템**의 **프로세스 API**에 대한 예찬을 하지만, 최근 연구에 따르면 **fork ()** 에 문제가 있다는 것이 밝혀졌고,

**spawn ()** 과 같은 간단한 **프로세스 생성 API**를 사용해야 한다고 주장한다.

기본적으로는 책이 주는 **사실**은 신뢰하되 저자의 모든 **의견**이 맞지는 않다는 것을 항상 주지하고 있어야겠다.

