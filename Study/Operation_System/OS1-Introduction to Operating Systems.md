# Introduction to Operating Systems

> OS에 대해 필요한 대략적인 큰 배경지식을 본다.



## 1. OS란?

### 1.1 OS는 어떤 부분을 책임지는가?

- 프로그램이 쉽게 실행되도록 한다.

- 프로그램이 메모리를 share할 수 있게 한다.

  > Q. 만약 프로그램이 메모리를 share하지 못하면?
  >
  > > A. Fairness, Efficiency 문제 발생.

- 프로그램이 다양한 device들과 interact 할 수 있게 한다.



##  2. 프로그램이 실행되는 과정?

1. Fetch: 프로세서가 메모리로부터 Instruction을 fetch 한다.
2. Decode: 이 Instrction이 어떤 것인지 해석한다.
3. Execute: 해당 Instruction을 실행한다.
4. 프로세서가 다음 Instruction으로 이동한다.
5. 위를 반복.



## 3. Virtualization

> Virtualization은 OS의 가장 핵심적인 요소 중 하나이다.

### 3.1 Virtualization 이란?

OS는 physical resource를 virtual 형태로 변환시킨다.

> CPU가 몇개가 있든, 메모리가 어떻게 되어있든, 가상화를 통해 이와 관계없이 항상 적절한 하드웨어 자원이 있다고 생각하고 user program이 동작하게 할 수 있다.



### 3.2 Virtualizing the CPU

시스템은 매우 많은 갯수의 가상 CPU를 가질 수 있다.

> 이를 통해 한개의 CPU를 무한개의 CPU를 가진 것 처럼 사용할 수 있다.
>
> > 이는 수많은 프로그램을 동시에 동작하게 만들 수 있음을 의미한다.



### 3.3 Virtualizing Memory

- 실제 Physical memory는 byte의 Array이다.

- 프로그램은 자신의 모든 data structure를 메모리에 저장한다.

  > - Read memory (load)
  >
  > - Write memory (store)

> 추가설명: 메모리 가상화를 통해 프로그램은 자신이 가질 수 있는 최대의 메모리를 모두 가지고 있다고 상정하고 동작한다.
>
> 예를 들어 32bit 시스템은 최대 8gb의 메모리를 가질 수 있으므로, 하나의 프로그램은 자신이 8gb의 메모리를 가지고 있다고 상정하고 동작한다.

**주의**

프로그램내에서 사용하는 가상메모리 주소는 실제 Physical memory 주소와는 다르다.

각각의 프로그램은 각자의 private memory 주소를 사용한다.  (추후 OS가 이를 physical memory 주소에 매핑하는 방법에 대해 배운다.)



### 3.4 The Problem of Concurrency

- OS는 수많은 것들을 한번에 처리하는데 이것이 마치 저글링하는것과 비슷하다.

  > 하나의 프로세스를 잡으면 그것을 다시 공중에 던지고 다른 프로세스를 받고 그 프로세스를 다 처리하면 또 다시 던지고 다른것을 받고, 반복...

- 현대의 multi-threaded program 들은 현대 PC의 CPU 개수가 많아지면서 multi thread 로 동작하는 경우가 많다. 



## 4. System Call

> 쉽게 설명하자면 OS가 해야하는 일을 부탁할 때 호출하는 함수이다.
>
> 비유적으로 표현하면, 은행 창구에 앉아있는 은행원 같은 역할을 하는 함수이다.

- 보통의 OS는 수백개의 System call을 가진다.



## 5. Persistence (영속성)

- DRAM 과 같은 디바이스는 데이터가 휘발성으로 저장된다.

- 하드웨어와 소프트웨어는 데이터를 **영구적**으로 저장할 필요가 있다.

  > - Hardware: HDD, SSD와 같은 저장장치 활용.
  > - Software: File system



## 6. Design Goals

- Build up abstraction (추상화)

- Provide high performance (고성능)

- Protection between applications (isolation)

- High degree of reliability (높은 신뢰성)

- others

  > - Energy-efficiency
  >
  > - Security
  >
  > - Mobility
  >
  > - ...

