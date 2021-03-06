# Paging

**핵심 질문: 페이지를 사용하여 어떻게 메모리를 가상화할 수 있을까?**

**세그멘테이션** 방법은 가변 크기 조각들로 메모리를 분할해서 사용했다.

이는 **단편화 (fragmented)** 를 일으킬 수 있다. 그리고 이 때문에 점점 할당이 어려워진다.

메모리 공간을 동일한 크기의 조각으로 분할하는 방법을 고려해 볼 필요가 있다.

이를 **paging** 기법이라고 부른다.

그리고 각각의 고정크기의 단위를 **page** 라고 부른다.

마찬가지로 물리 메모리도 **page frame (페이지 프레임)** 이라는 고정 크기의 슬롯의 배열이라고 생각한다.

하나의 물리메모리 **page frame** 은 하나의 가상메모리 **page** 를 저장할 수 있다.

프로세스 마다 **page table** 이 필요하다. (가상 주소를 물리 주소로 변환하기 위해)

---

## 1. 간단한 예제 및 개요

### 페이징의 예

- 총 크기가 64byte 를 16byte **page** 4개로 구성할 수 있다.

  (실제 우리가 사용하는 컴퓨터의 주소 공간은 훨씬 크다. 32비트의 경우 최대 4GB, ~~64비트의 경우 그냥 엄청 크다~~)

- 128byte 물리 메모리를 8개의 **page frame** 으로 나눴다.

  이중에 4개의 **page frame** 에 **page** 가 할당 되고, 3개는 미사용, 1개는 운영체제를 위해 예약된다.

  (운영체제가 자기자신을 위해서 물리 메모리 일부를 사용한다.)

---

### 페이징의 장점

- **페이징은 유연성이 좋다.**

  기존 방식은 힙과 스택이 어느 방향으로 커지는가 어떻게 사용되는가 생각해야 되는데, 페이징은 그런거 생각할 필요가 없다.

- **비 연속적인 물리 메모리 공간 할당이 가능하다.**
  - **가상 메모리 (virtual memory)** 를 같은 사이즈의 블록으로 나눈다. **(pages)**
  - **물리 메모리 (physical memory)** 를 고정 사이즈의 블록으로 나눈다. **(frames)**
  - 페이지나 프레임을 2의 N승 크기로 한다. (통상적으로 4KB)

- **빈 공간 관리가 단순하다.**

  - 운영체제는 **빈 프레임 리스트** 를 유지하고 있다가 필요한 페이지를 할당한다.

  - **n page** 크기의 프로그램을 실행시키기 위해, **n개** 의 **free frame** 을 찾아 프로그램에 로드한다.

  - 프로세스 마다 **page table** 이라는 자료구조를 유지한다. (virtual 주소를 physical 주소로 변환하기 위해)

    (가상 페이지 0 -> 물리 프레임 3)

    (VP 0 -> PF 3)

    이런 형식.

  - **external fragmentation** 이 없다.

---

### 페이징의 과정을 자세히

프로세스가 생성한 가상 주소의 **변환 (translation)** 을 위해 **가상주소** 를 

- **가상 페이지 번호 (virtual page number, VPN)** 
- 페이지 내의 **오프셋 (offset)** 

으로 나눈다.

우리는 위에서 64byte 의 가상 메모리 공간을 가지고 있고, 16byte 크기의 페이지 4개로 나눈다고 가정했다.

따라서 6비트 (2^6 = 64) 가 필요하다.

| VPN  | VPN  | offset | offset | offset | offset |
| ---- | ---- | ------ | ------ | ------ | ------ |
| Va5  | Va4  | Va3    | Va2    | Va1    | Va0    |

Va5가 최상위 비트, Va0가 최하위 비트이다.

4개의 페이지를 선택할 수 있어야 한다. -> 2^2 = 4. 따라서 2비트의 **VPN** 필요.

나머지 4비트는 페이지 내에서 우리가 원하는 바이트의 위치를 나타낸다. 이것을 **오프셋 (offset)** 이라고 한다.

---

예를 들어 가상 주소가 21이라고 하자.

21 = 010101 (2진법) 이다.

| VPN  | VPN  | offset | offset | offset | offset |
| ---- | ---- | ------ | ------ | ------ | ------ |
| 0    | 1    | 0      | 1      | 0      | 1      |

따라서 가상 주소 21은 가상 페이지 01(즉, 1) 의 5번째 (0101 번째) 바이트이다.

이 가상 페이지 번호를 **페이지 테이블** 의 인덱스로 사용해서 가상 페이지 1이 어느 물리 프레임에 저장되어 있는지 알 수 있다.

다시말해 **페이지 테이블** 에는 가상 페이지 번호와 물리 프레임 번호가 서로 매핑되어 있다.

페이지 테이블이 다음과 같다고 해보자.

| 가상 페이지 | 물리 프레임 |
| :---------: | :---------: |
|    VP 0     |    PF 3     |
|    VP 1     |    PF 7     |
|    VP 2     |    PF 5     |
|    VP 3     |    PF 2     |

위의 페이지 테이블에서 VP1 과 매핑된 **물리 프레임 번호 (physical frame number, PFN)** 은 7 (이진수 111) 이다.

VPN 을 PFN 으로 교체하여 가상주소를 변환 할 수 있다. (위에서 가정했듯이 PFN 은 총 8개 이므로 PFN에 총 3비트 필요)

| VPN  | VPN  | offset | offset | offset | offset |
| ---- | ---- | ------ | ------ | ------ | ------ |
| 0    | 1    | 0      | 1      | 0      | 1      |

| PFN  | PFN  | PFN  | offset | offset | offset | offset |
| ---- | ---- | ---- | ------ | ------ | ------ | ------ |
| 1    | 1    | 1    | 0      | 1      | 0      | 1      |

오프셋은 동일하고 VPN 이 PFN 으로 변환된 것이다.

최종적으로 계산된 물리 주소는 1110101 (십진수 117) 이며, 이 주소가 데이터가 저장된 정확한 위치이다.

---

### 또다른 예시

- **Virtual address: 32 bits**
- **Physical address: 20bits**
- **Page size: 4KB** (= 2^2 * 2^10 = 2^12 byte)

여기서, offset, VPN, page table entry, page table size 를 구해보자.

- **Offset: 12 bits**

  한 페이지의 사이즈가 4KB 인데 한 페이지 안에서의 위치를 나타내려면 4KB = 2^12 Byte, 즉 12개의 비트가 필요하다.

- **VPN: 20 bits**

  가상 주소의 크기는 위에서 보면 32bit 이다.

  이 중에서 offset으로 12비트를 사용했다. 남은 비트 수는 32-12=20bits 이다.

- **Page table entries: 2^20**

  가상 페이지 개수 (VPN)가 20bit 이니까

  페이지 테이블에는 한페이지당 한 자리가 필요하니, 2^20개의 자리가 필요할 것이다. (어림잡아 백만)

- Page table size?

  하나의 페이지 테이블 엔트리 당 필요한 공간은 얼마일까?

  또, 프로세스 하나에 할당되는 페이지 테이블의 전체 크기는 얼마나 될까?
  
  사실 엄청나게 크다. 아래에서 설명.

---

## 2. 페이지 테이블은 어디에 저장되는가?

위 예시에서 봤듯이 만약 VPN이 20bits 이고 offset이 12bits 인 가상주소를 쓴다고 가정했을때,

하나의 **페이지 테이블 엔트리 (page table entry, PTE)** 당 필요한 메모리가 4byte 라고 어림잡아 가정하면,

전체 **페이지 테이블 (page table)** 의 크기는

4byte * 2^20 = 4MB 나 된다.

4MB는 너무 커서 레지스터에 저장 못한다. (MMU 안에 만들지 못한다.)

메모리에 저장해야 된다.

---

## 3. 페이지 테이블에는 실제 무엇이 있는가?

### 페이지 테이블이란?

- **page table** 은 가상 주소를 물리 주소에 매핑하기 위한 자료구조이다.
- 운영체제는 **VPN** 로 배열을 인덱싱 한다. 그리고 그것으로 페이지 테이블 엔트리를 look-up 한다.

---

### 페이지 테이블 엔트리에 흔히 쓰는 플래그

- Valid Bit

  이 페이지의 변환이 valid 했는지

- Protection BIt

  read, write, execute 권한 등

- Present Bit

  이 페이지가 물리 메모리에 있는지 아니면 디스크에 있는지 (스왑 아웃 되었는지)

- Dirty Bit

  메모리에 반입된 후 페이지가 변경되었는지 여부

- Reference Bit (Accessed Bit)

  페이지가 접근되었는지

  어떤 페이지가 인기가 있는지 알아내서, 메모리에 유지될지 어떤 메모리를 쫒아낼지 결정하는데 필요

  **페이지 교체** 할 때 매우 중요 (나중에 다룸)

---

## 4. 페이징: 너무 느림

선형 페이지 테이블을 사용한다고 하면,

- 원하는 **PFN (페이지 프레임 번호)** 를 찾기 위해서, **VPN (가상 페이지 번호)** 로 배열의 항목에 접근하고, 그 항목의 **PTE (페이지 테이블 엔트리)** 를 검색해야 한다.
- 원하는 **PTE** 를 얻기 위해 **페이지 테이블의 시작 위치** 를 알아야 한다.

- 다시 말해 매번 메모리 참조를 한번씩 더 해야 한다.

  이 말은 두배 느려진다는 뜻.

---

## 5. 페이징으로 메모리 접근하기

```c
// 가상 주소에서 VPN 추출
VPN = (VirtualAddress & VPN_MASK) >> SHIFT
// 페이지 테이블 엔트리(PTE)의 주소 생성
PTEAddr = PTBR + (VPN * sizeof(PTE)) // PTBR = 페이지 테이블 베이스 레지스터 값
// Fetch PTE
PTE = AccessMemory(PTEAddr)
// 프로세스가 페이지를 접근할 수 있는지 확인
if (PTE.Valid == False)
  RaiseException(SEGMENTATION_FAULT)
else if (CanAccess(PTE.ProtectionBits) == False)
  RaiseException(PROTECTION_FAULT)
else
  // 접근 가능하면물리 주소 만들고 값 가져오기
  offset = VirtualAddress & OFFSET_MASK // 가상 주소에서 offset 추출
  PhysAddr = (PTE.PFN << PFN_SHIFT) | offset // PFN 을 왼쪽으로 이동시키고, 오른쪽에 offset을 붙인다.
  Register = AccessMemory(PhysAddr)
```

---

## 6. Demand Paging

다 올리고 볼게 아니라 쓰는 부분만 메모리에 올리자.

- 운영체제는 메인 메모리를 프로세스가 할당한 모든 데이터의 페이지 캐시로 사용한다.
  - 페이지를 필요할때만 메모리로 가져온다.
  - 페이지는 **물리 메모리 프레임 (physical memory frame)** 으로부터 쫒겨날 수 있다.
  - 쫒겨난 페이지들은 디스크로 간다. (dirty page 만 write 한다. 변경점 없는 페이지는 wrtie할 필요 없다.)
  - 페이지의 이동은 프로세스에게 투명하다.
- 장점
  - 더 적은 I/O 만 해도 된다.
  - 더 적은 메모리만 있어도 된다.
  - 응답속도가 더 빠르다.
  - 더 많은 프로세스를 동시에 실행 가능.

---

### Page Fault

- **PTE (페이지 테이블 엔트리)** 에 **present bit** 이 꺼져있는 경우 (해당 페이지가 물리 메모리에 없는 경우) CPU 에 의해 exception 이 발생한다.

- Major page faults

  1. 페이지는 valid 하지만 메모리에 올라와 있지 않으면,
  2. OS는 그 페이지를 어디서 찾아야 하는지 정보를 가지고 있다.
  3. 이럴 때 disk I/O 가 필요하다.

- Minor page faults

  - 페이지 폴트가 disk I/O 없이 해결 될 때가 있다.

    lazy allocation (stack 이나 heap page 의 경우 메모리 추가 할당만 하면 된다.)

    페이지는 물리메모리로부터 미리 fetch 해놨지만 allocation은 안 해놓은 경우이다.

---

## 페이징 기법의 장단점 총정리

### 장점

- external fragmentation 이 없다

- allocate 와 free 가 빠르다

- page out (페이지 쫒아내기) 가 쉽다.

  쫒아내는 애 크기와 새로 들어올 애 크기가 같기 때문

- 페이지의 보호와 공유가 쉽다. (페이지 번호로 관리하니까 메모리 벗어나거나 하기 쉽지 않음)

### 단점

- Internal fragmentation

  사실 minor 한 issue 이다.

- Memory reference overhead

  메모리 참조를 두번 해야한다.

  (해결법: TLB 라는 하드웨어의 도입)

- page table 을 위한 메모리 공간이 필요하다.

  - **가상 주소 공간 (virtual address space)** 안에 **페이지** 하나 당 **PTE (페이지 테이블 엔트리)** 가 하나 필요하다.
    - 32bit 주소 공간, 페이지 크기 4KB 이면, 2^20개의 PTE 필요.
    - PTE 한개 당 4byte 가 필요하니, 페이지 테이블 한개 당 4MB 필요.
    - 시스템에 100개의 프로세스가 있다고 하면, 총 400MB 의 페이지 테이블 용량 필요.
  - 해결법: valid 한 PTE 들만 저장 한다. 또는 페이지 테이블을 페이징 한다. (TLB라는 하드웨어 도입)

---

