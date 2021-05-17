# Condition variable 에 대해

OSTEP 교제에서 POSIX 의 condition variable 의 동작을 가르치는데,

당연하다는 듯 너무나 복잡하고 난해한 방법을 **반드시** 사용해야 한다고 가르치는데

계속 무언가가 잘못되었다는 느낌을 지울 수 없었다.

1. wait 함수는 이름과 다르게, wait 만 하지 않는다.

   즉 함수의 이름이 wait and unlock, and relock when its return 이렇게 해야 맞다.

   이렇게 이름을 바꾸면 뭔가 잘못되었다는것이 확 와 닿는다.

   - 하나의 함수가 이렇게 복합적인 일을 해도 되는가?
   - 과연 wait 이라는 이름만 보고 저 동작들을 유추해 낼 수 있는가?
   - 저것 때문에 코딩의 형태가 너무 강요되는것 아닌가?

2. 위 이유 때문에 결국, 조건 확인 시 if 문 대신 while 문이 강요된다.

3. 이 문제를 나만 느낀 게 아니었다.

   Mesa sementic, Hoare semantic

   https://youtu.be/5R9u16q4IcI 현업자의 의견

---

OSTEP 교제에서는 대부분의 시스템에서 Mesa sementic 을 사용하고, Hoare semantic 은 구현이 어렵다고 설명한다.

위 영상의 현업자의 말을 들어보면 아마 Windows 에서는 Hoare semantic 을 사용하는 듯 하다.

Windows 에서의 구현(event object)을 한번 보고, 왜 POSIX 는 Mesa sementic 을 고집하는지 알아봐야겠다.

Mesa sementic 은 개발자들의 실수를 유도하고, 직관적으로 코드를 이해하는것을 저해한다고 생각한다.

---

후에 이에 대해 알아보고 글을 추가하겠다.

-2021.05.16-

---

리눅스 커널에 대해 연구하신 학교 교수님께 질문해본 결과

교수님도 ''잘 모른다''가 답이었다.

>  아마 다른 요소들과 엮인 문제가 있어서 구현하지 못하지 않았을까
>
> 아니면 정말로 기술력의 부족으로 아직 만들지 못했을 수도 있다

정도의 답을 얻을 수 있었다.

아직 추가적으로 더 찾아볼 필요가 있는 것 같다.

-2021.05.17-

---

IEEE 문서에서 정말 아름답고 명쾌한 답을 찾을 수 있었다.

POSIX.1-2017 treatment of `pthread_cond_broadcast` and `pthread_cond_signal`

의 RATIONALE 섹션을 보자.

[문서 링크](https://pubs.opengroup.org/onlinepubs/9699919799/functions/pthread_cond_signal.html#tag_16_418_08_01) 

문서에서 해당 부분 인용 (중요한 부분은 강조 표시 했다.)

> The effect is that more than one thread can return from its call to [*pthread_cond_wait*()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/pthread_cond_wait.html) or [*pthread_cond_timedwait*()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/pthread_cond_timedwait.html) as a result of one call to *pthread_cond_signal*(). This effect is called "spurious wakeup". Note that the situation is self-correcting in that the number of threads that are so awakened is finite; for example, the next thread to call [*pthread_cond_wait*()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/pthread_cond_wait.html) after the sequence of events above blocks.
>
> **While this problem could be resolved, the loss of efficiency for a fringe condition that occurs only rarely is unacceptable,** especially given that one has to check the predicate associated with a condition variable anyway. Correcting this problem would unnecessarily reduce the degree of concurrency in this basic building block for all higher-level synchronization operations.
>
> **An added benefit of allowing spurious wakeups is that applications are forced to code a predicate-testing-loop around the condition wait. This also makes the application tolerate superfluous condition broadcasts or signals on the same condition variable that may be coded in some other part of the application.** The resulting applications are thus more robust. Therefore, POSIX.1-2017 explicitly documents that spurious wakeups may occur.

문서에 따르면 Spurious wakeup 문제를 해결할 수 있음에도 해결하지 않은 이유는,

**아주 가끔 일어나는 이 문제를 해결하기 위해 너무 많은 성능을 포기할 수 없다는 것** 이다.

또 컨디션 체크 부분을 while 문으로 강제하는 것이 장점이 되는 부분도 있는데,

같은 condition variable 에서 발생하는 불필요한 condition broadcast 와 signal 을 억제할 수 있다는 것이다.

이제 왜 POSIX 계열에서 이 문제를 해결하지 않았는지는 알았다.

그러나 아직 의문이 하나 남았다.

그렇다면 **과연 마이크로소프트는 Windows 에서 어떻게 이 문제를 해결했고, 정말 큰 성능 저하 없이 이를 해 냈을까?**

-2021.05.18-

---

