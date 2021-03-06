# Swapping - Mechanism

## 물리 메모리 크기의 극복: 메커니즘

> **핵심 질문: 물리 메모리 이상으로 나아가기 위해서 어떻게 할까**
>
> 운영체제는 어떻게 크고 느린 장치를 사용하면서 마치 커다란 가상 주소 공간이 있는 것처럼 할 수 있을까?

스왑 공간이 추가되면 운영체제는 실행되는 각 프로세스들에게 큰 가상 메모리가 있는 것 같은 환상을 줄 수 있다.

멀티프로그래밍 시스템(컴퓨터의 사용률을 높이기 위해 동시에 여러 프로그램들을 실행시키는 시스템)이 발명되면서 많은 프로세스들의 페이지를 물리 메모리에 전부 저장하는 것이 불가능하게 되었다.

멀티 프로그래밍과 사용 편의성 등의 이유로 실제 물리 메모리보다 더 많은 용량의 메모리가 필요하게 되었다.

이것이 현대 **Virtual Memory(가상 메모리)** 의 역할이다.

---

### 0. 스왑 방식

- Overlays

  - 프로그래머가 수동으로 코드나 데이터를 메모리에 넣었다 뺏다가 함

    -> 프로그래머가 너무 많은 일을 해야 하므로 보통 사용하지 않음

  - OS의 특별한 지원이 필요없음

- Process-level swapping

  - 어떤 프로세스를 통째로 스왑하는 방식
  - 나중에 다시 메인 메모리에 넣어서 계속 실행을 진행함
  - 초기 버전의 안드로이드가 사용했던 방식

- Page-level swapping

  - 실제 가장 많이 사용하는 방식
  - 페이지 단위로 스왑 아웃, 스왑 인 하는 방식
  - 프로그램 A의 일부 + 프로그램 B의 일부 이런식으로 나눠서도 스왑인 아웃할 수 있음. (process level 과는 다르게)

---

## 1. 스왑 공간

가장 먼저 할 일은 페이지들을 저장할 수 있는 일정 공간을 확보하는 것이다.

이 용도의 공간을 **스왑 공간(swap space)** 라고 한다.

운영체제는 스왑 공간에 있는 모든 페이지들의 **디스크 주소** 를 기억해야 한다.

스왑 공간을 이용하면, 실제 존재하는 메모리 공간보다 더 많은 공간이 존재하는 것처럼 가장할 수 있다는 것을 알 수 있다.

꼭 스왑 공간에만 스왑을 할 수 있는 것은 아니다.

코드 영역의 페이지들이 차지하는 물리 페이지는 다른 페이지가 사용할 수 있다. 코드가 저장되어 있는 파일 시스템 영역이 스압 목적으로 사용되는 셈이다.

해당 페이지들은 디스크에 원본이 있으므로, 언제든지 다시 스왑-인이 가능하기 때문이다.

## 2. Present Bit

디스크에 스왑 공간을 확보했으니, 이제 페이지 스왑을 위한 기능을 다룰 차례이다.

페이지가 디스크로 스왑되는 것을 가능케 하려면, 많은 기법들이 추가되어야 한다.

특히, 하드웨어가 PTE에서 해당 페이지가 물리 메모리에 존재하지 않는다는 것을 표현해야 한다.

하드웨어는 **present bit** 를 사용하여 각 **page table entry** 에 어떤 페이지가 존재하는지를 표현한다.

- **present bit 가 1이면:**

  물리 메모리에 해당 페이지가 존재한다.

- **present bit 가 0이면:**

  물리 메모리에 해당 페이지가 존재하지 않고 디스크 어딘가에 존재한다.

물리 메모리에 존재하지 않는 페이지를 접근하는 행위를 일반적으로 **페이지 폴트(page fault)** 라고 한다.

**페이지 폴트** 가 발생하면 **페이지 폴트 핸들러(page fault handler)** 가 실행된다.

## 3. 페이지 폴트 (Page Fault)

TLB 미스의 처리 방법에 따라, 두 종류의 시스템이 있었다.

1. 하드웨어 기반의 TLB
2. 소프트웨어 기반의 TLB

두 방법 모두, 페이지 폴트가 발생하면, 운영체제가 그 처리를 담당한다.

운영체제의 **페이지 폴트 핸들러** 가 그 처리 메커니즘을 규정한다.

만약 요청된 페이지가 메모리에 없고, 디스크로 스왑되었다면, 운영체제는 해당 페이지를 메모리로 스왑해 온다.

> Q.  **원하는 페이지의 위치는 어떻게 알까?**
>
> 많은 시스템들은 해당 페이지의 스왑 공간상에서의 위치를 페이지 테이블에 저장한다.
>
> 운영체제는 테이블 항목에서 해당 페이지의 디스크 상 위치를 파악하여, 메모리로 탑재한다.

## 4. 메모리에 빈 공간이 없으면?

여태까지 스왑 공간으로부터 페이지를 가져오기 위한 (Page-in) 여유 메모리가 충분하다고 가정했다.

메모리에 여유 공간이 없을 수도 있다(또는 거의 다 찼거나).

새로운 페이지를 위한 공간을 확보하기 위해 하나 또는 그 이상의 페이지들을 먼저 **페이지 아웃(page out)** 할 수 있다.

교체할 페이지를 선택하는 것을 **페이지 교체 정책(page-replacement policy)** 이라고 한다.

> 잘못된 교체 정책은 성능에 큰 악영향을 미친다.
>
> 나쁜 정책은 프로그램이 메인 메모리 속도로 실행되는 것이 아니라 디스크와 비슷한 속도로 동작하게 할 수도 있다.

## 5. 페이지 폴트의 처리

다음 코드의 하드웨어 처리 과정에서, TLB 미스 발생 시, 세 가지의 중요한 경우가 있다는 것을 알 수 있다.

**페이지 오류 제어 흐름의 알고리즘(하드웨어)**

```pseudocode
VPN = (VirtualAddress & VPN_MASK) >> SHIFT
(Success, TlbEntry) = TLB_Lookup(VPN)
if (Success == True)	// TLB 히트
	if (CanAccess(TlbEntry.ProtectBits) == True)
		Offset = VirtualAddress & OFFSET_MASK
		PhysAddr = (TlbEntry.PFN << SHIFT) | Offset
		Register = \gndx{AccessMemory}(\gndx{PhysAddr})
	else
		RaiseException(PROTECTION_FAULT)
else
	PTEAddr = PTBR + (VPN * sizeof(PTE))
	PTE = \gndx{AccessMemory}(\gndx{PTEAddr})	// AccessMemory(PTEAddr)
	if (PTE.Valid == False) // 프로그램 버그 등으로 잘못된 주소를 접근하는 경우
		RaiseException(SEGMENTATION_FAULT)
	else
		if (CanAccess(PTE.ProtectBits) == False)
			RaiseException(PROTECTION_FAULT)
		else if (PTE.Present == True) // 페이지가 존재하며 유효한 경우
			// 하드웨어를 기반으로 한 TLB를 가정함
			TLB_Insert(VPN, PTE.PFN, PTE.ProtectBits)
			RetryInstruction()
		else if (PTE.Present == False) // 페이지가 유효하지만 존재하지 않는 경우
			RaiseException(PAGE_FAULT)
```

1. **(18-21 라인) 페이지가 존재하며 유효한 경우**

   이 경우, TLB 미스 핸들러가 PTE에서 PFN을 가져와서 여러 차례 명령어를 재시도한다(이 때는 TLB 히트가 된다).

2. **(22-23 라인) 페이지가 유효하지만 존재하지 않는 경우**

   페이지 폴트 핸들러가 반드시 실행되어야 한다. 프로세스가 사용할 수 있는 제대로 된 페이지이기는 하지만 물리 메모리에 존재하지 않기 때문이다.

3. **(13-14 라인) 프로그램 버그 등으로 잘못된 주소를 접근하는 경우**

   이 경우 PTE의 다른 비트는 의미가 없다. 하드웨어는 이 무효한 접근이 운영체제의 트랩 핸들러에 의해서 처리되도록 한다. 이 때 문제를 일으킨 프로세스는 종료될 수 있다.

다음 코드에서는 운영체제가 페이지 폴트를 처리하는 과정을 대략적으로 볼 수 있다.

**페이지 오류 제어 흐름의 알고리즘(소프트웨어)**

```pseudocode
PFN = FindFreePhysicalPage()
  if (PFN == -1)		// 비어있는 페이지 못 찾음
    PFN = EvictPage()		// 교체 알고리즘 실행
    // \gndx{DiskRead}(\gndx{PTE.DiskAddr, PFN})		// 대기 (I/O를 기다리기) (책 버전)
    DiskRead(PTE.DiskAddr, pfn)		// 대기 (I/O를 기다리기)
    PTE.present = True		// 존재한다고 페이지 테이블에 갱신
    PTE.PFN = PFN		// 비트와 변환 (PFN)
    RetryInstruction()		// 명령어 재시도
```

## 6. 교체는 실제 언제 일어나는가

여태까지는 메모리에 여유 공간이 고갈된 후에 교체 알고리즘이 작동하는 것을 가정했다.

이 방법은 그리 효율적이지 않다.

또 다양한 이유로 인해, 운영체제는 항상 어느 정도의 여유 메모리 공간을 확보하고 있어야 한다.

메모리에 항상 어느 정도의 여유 공간을 비워두기 위해서, 대부분의 운영체제들은 여유 공간에 관련된

- 최댓값(high wateramrk, HW)
- 최솟값(low watermark, LW)

를 설정하여 교체 알고리즘 작동에 활동한다.

동작 방식은 다음과 같다.

1. 운영체제가 여유 공간의 크기가 최솟값보다 작아지면

2. 여유 공간 확보를 담당하는 백그라운드 쓰레드가 실행된다.

3. 이 쓰레드는 여유 공간의 크기가 최댓값에 이를 때까지 페이지를 제거한다.

   이 백그라운드 쓰레드는 일반적으로 **스왑 데몬** 또는 **페이지 데몬** 이라고 불린다.

4. 충분한 여유 메모리가 확보되면 이 백그라운드 쓰레드는 슬립 모드로 들어간다.

한번에 여러개를 교체하면 성능 개선이 가능하다.

예를들어 많은 시스템들은 다수의 페이지들을 **클러스터(cluster)** 나 **그룹(group)** 으로 묶어서 한번에 스왑 파티션에 저장함으로써 디스크의 효율을 높인다.

클러스터링은 디스크의 탐색(seek)과 회전 지연(rotational delay)에 대한 오버헤드를 낮춰 성능을 상당히 높일 수 있다.

---

