# Translation Lookaside Buffers (TLBs)

**핵심 질문: 주소 변환 속도를 어떻게 향상할까**

- 평범한 페이징
  - linear page table 은 메모리 룩-업 비용을 두배로 만든다.
  - Multi-level page table 까지 사용하면, 그 비용이 더 커진다.

운영체제의 실행 속도 개선을 위해서는 하드웨어의 도움이 필요하다.

**변환-색인 버퍼 (translation-lookaside buffer)** 또는 **TLB** 라고 부르는 하드웨어를 도입한다.

- **TLB** 

  - **MMU (memory-management unit)** 의 일부이다.

  - 자주 참조되는 가상 주소-실주소 변환 정보를 저장하는 하드웨어 캐시이다.

  - **주소-변환 캐시(addreess-translation cache)** 가 좀 더 정확한 명칭이다.

  - 모든 엔트리를 룩-업 하는데 한꺼번에 병렬적으로 룩-업 한다. (look up in parallel) -> linear 하게 search 하지 않는다.

  - Replacement policy: LRU (Least Recently Used) -> 누구를 내쫒을 것인가? 젤 오래전에 사용했던거.

  - TLB는 사실 PTEs 전체를 캐싱한다. PFNs 만 캐싱하는게 아니라.

    TLB 안에는 VPN (virtual page number)가 Tag 로 있다.

    이는 page table과는 다르게 entry 정보를 따로 가지고 있지 않기 때문에 필요한 것이다.

- **TLB를 사용한 메모리 참조 과정** (간단히)

  1. 가상 메모리 참조
  2. 하드웨어가 먼저 TLB에 원하는 변환 정보가 있는지 확인 
  3. 있다면 페이지 테이블을 통하지 않고 변환을 빠르게 수행. (페이지 테이블에는 모든 변환 정보가 있다.)

---

## 1. TLB 의 기본 알고리즘

```pseudocode
VPN = (VirtualAddress & VPN_MASK) >> SHIFT	// VPN(virtual page number) 추출
(Success, TlbEntry) = TLB_Lookup(VPN)	// TLB가 해당 VPN의 변환정보를 가지고 있는지 확인
if(Success == True)	// TLB 히트
  if(CanAccess(TlbEntry.ProtectBits) == True)	// 해당페이지에 대해 접근권한 검사
    Offset = VirtualAddress & OFFSET_MASK	// TLB entry 로부터 PFN(page frame number)를 추출
    PhysAddr = (TlbEntry.PFN << SHIFT) | Offset // 원하는 physical address 를 형성해서
    Register = AccessMemory(PhysAddr)	// 물리 메모리에 접속
  else
    RaiseException(PROTECTION_FAULT)
else	// TLB 미스
  PTEAddr = PTBR + (VPN * sizeof(PTE))	// 하드웨어가 페이지 테이블에 변환 정보를 찾기위해 접속 
  PTE = AccessMemory(PTEAddr)	// 페이지 테이블에 접속해서 PTE 정보를 받아옴.
  if (PTE.Valid == False)
    RaiseException(SEGMENTATION_FAULT)
  else if (CanAccess(PTE.ProtectBits) == False)
    RaiseException(PROTECTION_FAULT)
  else
    TLB_Insert(VPN, PTE.PFN, PTE.ProtectionBits)	// TLB의 변환정보를 업데이트 한다.
    RetryInstruction()
```

> 참고
>
> - Page-Table Base Register(PTBR) : Page table을 가리키는 포인터
> - Page-Table Length Register(PTLR) : Page table의 size를 나타냄.

메모리 접근 연산은 다른 CPU연산 (덧셈, 곱셈) 등에 비해 매우 시간이 오래 걸린다.

TLB 미스가 많이 발생할수록 메모리 접근 횟수가 증가한다.

TLB 미스가 발생하는 경우를 최대한 피해야 한다.

---

## 2. 예제: 배열 접근 (accessing an array)

```c
int sum = 0;
for (i=0; i<10; i++){
  sum += a[i];
}
```

- 가상 주소 100번지부터 10개의 4바이트 크기의 정수 배열이 존재한다.
- 가상 주소 공간은 8bit 이다.
- 페이지의 크기는 16byte 이다.
- 가상 주소는 4bit VPN(16개의 가상 페이지 표현)과 4bit 오프셋(각각의 페이지는 16byte의 크기를 가짐)으로 구성된다.

|          | 00~04 | 04~08 | 08~12 | 12~16 |
| :------: | :---: | :---: | :---: | :---: |
|          |       |       |       |       |
| VPN = 01 |       |       |       |       |
| VPN = 02 |       |       |       |       |
| VPN = 03 |       |       |       |       |
| VPN = 04 |       |       |       |       |
| VPN = 05 |       |       |       |       |
| VPN = 06 |       | a[0]  | a[1]  | a[2]  |
| VPN = 07 | a[3]  | a[4]  | a[5]  | a[6]  |
| VPN = 08 | a[7]  | a[8]  | a[9]  |       |
| VPN = 09 |       |       |       |       |
| VPN = 10 |       |       |       |       |
| VPN = 11 |       |       |       |       |
| VPN = 12 |       |       |       |       |
| VPN = 13 |       |       |       |       |
| VPN = 14 |       |       |       |       |
| VPN = 15 |       |       |       |       |

변수 i, sum 그리고 명령어를 위한 메모리 접근은 무시한다.

정수 배열에 대한 메모리 접근만 보자.

1. 첫 번째 배열의 항목 a[0] 에 접근된다.

   가상주소 100번이다.

2. 하드웨어는 VPN을 추출한다.

   VPN이 06번이다.

3. 하드웨어는 TLB에서 해당 VPN을 검색한다. (TLB가 완전히 초기화 되어있다고 가정하자.)

4. 첫 접근이라 TLB 미스가 발생한다.

5. 미스가 발생하면 해당 VPN 06번에 대한 물리 페이지 번호를 찾아 TLB를 갱신한다.

6. a[1] 를 읽는다.

7. TLB 히트 (아까 페이지 06번을 이미 TLB에 가져왔다.)

8. a[2] 도 마찬가지

9. a[3] 를 읽는다.

10. TLB 미스

11. 그 다음 a[4], a[5], a[6] 을 읽을 때는 히트

12. 마찬가지로 a[7] 에서 미스, a[8], a[9] 는 히트

**총 정리**

3 미스, 7 히트, TLB hit rate = 70%.

TLB 는 공간 지역성 (spatial locality)로 인해 성능을 개선 가능하다는 것을 알 수 있다.

만약 페이지의 크기가 16byte 가 아니라 32byte 였다면? -> 미스 횟수가 줄어든다.

즉, 페이지의 크기는 히트율에 큰 영향을 준다.

실제 대부분의 시스템에서는 4KB 크기의 페이지를 사용한다.

---

### 지역성 (Locality)

- **Temporal Locality (시간 지역성)**

  최근에 접속한 instruction 이나 data 가 미래에 또 접속될 가능성이 높다.

- **Spatial Locality (공간 지역성)**

  만약 프로그램이 메모리 주소 x에 접근했다면, 곧 메모리 주소 x 근처에 접속할 가능성이 높다.

---

## 3. TLB 미스는 누가 처리할까?

TLB 미스 처리에는 두 가지 방법이 있다.

하드웨어가 처리하는 방식, 소프트웨어가 처리하는 방식.

- 하드웨어가 TLB 미스를 처리하는 방식 **CISC** (인텔의 x86 프로세서가 이 방식)

  - 하드웨어는 메모리에서 페이지 테이블이 정확히 어디에 있는지 알고있다.
  - 하드웨어는 다음과 같은 과정으로 미스를 처리한다.
    1. 페이지 테이블에서 원하는 페이지 테이블 엔드리를 찾는다.
    2. 필요한 변환 정보를 추출한다.
    3. TLB를 갱신한다.
    4. TLB 미스가 발생한 명령어를 재실행한다.
  - 하드웨어가 TLB를 관리한다.

- 소프트웨어가 TLB 를 관리하는 방식 **RISC** (ARM 프로세서가 이런 방식)

  - TLB 미스가 발생하면 하드웨어가 exception을 raise 한다. (trap handler)

    (trap handler 는 OS 안에 TLB 미스를 처리하기 위해 작성된 코드이다.

    시스템 콜 호출 시 사용되는 트랩 핸들러와는 다른 트랩핸들러 이다.)

---

## 4. TLB의 문제: 문맥 교환 (Context Switching)

**핵심 질문: 문맥 교환 시 TLB 내용을 어떻게 관리하는가**

TLB 관련 이슈들은 컴퓨터의 성능을 엄청나게 좌우하기 때문에 중요하다.

TLB에 있는 가상주소와 실제 주소간의 변환 정보는 이 정보를 탑재시킨 프로세스한테만 유효한 정보이다.

그런데, context switching 을 하게 되면, 같은 가상주소가 TLB 하나에 여러개 매핑될 수 있다. (여러 프로세스에서 같은 가상주소를 쓰니까)

다음과 같이 두개의 프로세스 항목들이 TLB에 같이 있다고 해보자.

| VPN  | PFN  | valid | prot |
| :--: | :--: | :---: | :--: |
|  10  | 100  |   1   | rwx  |
|  --  | ---  |   0   | ---  |
|  10  | 170  |   1   | rwx  |
|  --  | ---  |   0   | ---  |

VPN 10번에 대한 변환 정보가 2개 존재하게 된다.

둘 중 어느 정보가 현재 프로세스에 해당하는 정보인지 알 방법이 없다.

추가적인 기능이 필요하다.

---

여러가지 해결방법이 있을 수 있다.

- **문맥 교환 할 때 마다 TLB 내용 비우기** (이 방법은 별로다)

  만약 문맥 교환이 자주 일어나면? 성능에 큰 부담이 생긴다.

- **TLB 내에 주소 공간 식별자 (address space identifier, ASID) 필드를 추가한다.**

  PID 와 비슷한 개념이다.

  TLB 내용을 문맥 교환시마다 비우지 않고 유지하면서도, 어느 프로세스의 내용인지 구별 가능하다.

---

### ASID 적용

| VPN  | PFN  | valid | prot | ASID |
| :--: | :--: | :---: | :--: | :--: |
|  10  | 100  |   1   | rwx  |  1   |
|  --  | ---  |   0   | ---  |  -   |
|  10  | 170  |   1   | rwx  |  2   |
|  --  | ---  |   0   | ---  |  -   |

이렇게 되면, 같은 VPN 값을 가진 엔트리가 2개가 있어도, 각각 어느 프로세스에 해당하는 내용인지 구분할 수 있다.

### 두 개의 프로세스가 하나의 PFN 을 공유하는 경우

| VPN  | PFN  | valid | prot | ASID |
| :--: | :--: | :---: | :--: | :--: |
|  10  | 101  |   1   | r-x  |  1   |
|  --  | ---  |   0   | ---  |  -   |
|  50  | 101  |   1   | r-x  |  2   |
|  --  | ---  |   0   | ---  |  -   |

코드 페이지를 공유하면 프로세스가 사용하는 물리 페이지의 수를 줄일 수 있다.

만약, 공유하지 않는다면, 같은 내용을 서로 다른 물리 프레임에 배정해서 사용해야 할 것이다.

---

## 5. 이슈: 교체 정책 (TLB Replacement Policy)

**핵심 질문: TLB 교체 정책은 어떻게 설계하는가**

모든 캐시가 그러하듯, TLB에서도 캐시 교체 정책이 매우 중요하다.

새로운 항목을 TLB 에 탑재할 때, 어떤 녀석을 쫒아내고 새로운 녀석을 넣어야 하는가?

- **LRU (least-recently-used, 최저 사용 빈도)**
  - 가장 오래전에 사용되었던 것을 내쫒는다.
  - 메모리 참조 패턴에서 **지역성 (locality)** 를 최대한 활용하려는 것이 목적이다.

---

## 6. TLB Performance

- TLB 는 많은 performance 문제들의 근원지이다.

  hit rate, lookup latency ...

- Increase **TLB reach** (TLB의 한계에 도달)

  한번에 `TLB entry 개수 * Page size` 크기 만큼이 TLB 미스 안나고 최대로 접근할 수 있는 데이터의 크기이다.

  다시말해 이 크기를 넘어서면 무조건 미스가 난다.

  - page size 를 키우는 방법이 있다. (이렇게 하면 여러 문제가 발생한다.)

     huge page 라고 부르는데, Intel64 에서는 2MB, 1GB 크기의 page 지원.

  - TLB 의 크기를 키우는 방법.

- multi-level TLB 를 사용

- 너가 코딩할때 알고리즘과 자료구조를 TLB를 고려해서 짠다.

  요즘에는 워낙 컴파일러가 잘되어있어서 대부분의 경우 컴파일러가 알아서 잘 해주긴 하지만

  인지하고 코딩하는게 중요.

---

