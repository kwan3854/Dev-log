# File System Implementation (파일 시스템 구현)

이번에는 **VSFS(Very Simple File System)** 라고 하는 간단한 파일 시스템 구현에 대해 소개한다.

이 파일 시스템은 UNIX 파일 시스템을 단순화 한 것이다.

> **핵심 질문: 어떻게 간단한 파일 시스템을 만들 것인가**
>
> 간단한 파일 시스템을 어떻게 만들 수 있을까?
>
> 다스크 위에는 어떤 자료 구조가 필요할까?
>
> 그러한 자료 구조는 어떤 정보를 추적해야 하는가?
>
> 그 자료 구조들은 어떻게 접근되어야 하는가?

## 1. 생각하는 방법

- 파일 시스템의 **자료 구조 (on-disk structures)**
  - 파일 시스템이 파일과 디렉토리를 어떻게 표현하는가?
  - 파일 시스템의 메타데이터를 어떻게 관리하는가?
- **접근 방법 (access method)**
  - 다양한 파일 시스템 API 들이 어떤 단계를 거쳐서 동작하는가?
  - open(), read(), wite(), close(), ...

## 2. 전체 구성

가장 먼저 해야 할 것은 디스크를 **블럭(block)** 들로 나누는 것이다.

일반적으로 사용되는 크기인 4KB 의 크기, 단일 사이즈로 블럭을 만들겠다.

### Very Simple File System

- 디스크를 블럭으로 나눈다. (ex. 4KB)

- 대부분의 디스크 블럭들은 user data 를 저장하는데 사용된다.

  -> **데이터 영역(data region)**

- 디스크의 아주 작은 부분만이 파일시스템의 **메타 데이터** 로 사용되기 위해 배정되어 있다.

  -> ex) **아이노드(inode)**

> - Data block: 실제 사용자 데이터가 위치
> - Data bitmap: 각 data block 들이 사용중인지 아닌지 표시
>
> - Inode block: 각각의 데이터 블록들, 즉,  file들의 메타 데이터
> - Inode bitmap: 각 inode block 들이 사용중인지 아닌지 표시
>
> - Super block: 파일 시스템 전체에 대한 메타 데이터

### 각각의 아이노드들은 파일의 메타 데이터를 가지고 있다

- 아이노드의 사이즈는 고정 (보통, 128B ~ 256B)

- 4KB 블록은 16개의 아이노드를 가질 수 있다. (아이노드가 256B 라고 했을 때)

- 총 5개의 아이노드 블록이 있을 때, 총 80개의 아이노드가 있는 것이다.

  (80 = 파일의 최대 갯수)

### 비트맵(Bitmaps): 어떤 블럭이 사용중인가 아닌가?

- Data bitmap & Inode bitmap

  - 각각의 비트는 대응하는 block/inode 가 free (0) 인지 in-use (1) 인지 표시한다.

  - 하나의 데이터 비트맵 블록은 최대 32K 의 데이터 블록들(or inodes) 를 지원한다.

    -> 한 블록의 크기 = 4KB = 4096Byte = 4096*8 =약 32Kbit = 약 32,000 개 표현 가능

  - Data bitmap (데이터 비트맵)

    데이터 영역의 블럭이 사용중인지 표시

  - Inode bitmap (아이노드 비트맵)

    아이노드 테이블에 있는 아이노드들이 사용중인지 표시

### Superblock

Superblock 은 파일 시스템 전체에 대한 메타데이터를 가지고 있다.

- 파일 시스템의 type
- 블록 사이즈
- 총 블록의 수
- 아이노드의 수
- 아이노드 테이블의 시작 주소
- 데이터의 수, 아이노드 비트맵의 수, ...

만약 슈퍼 블록이 손상되면 파일 시스템 전체가 깨진다.

그래서 대부분의 파일 시스템은 슈퍼 블록을 여러개 복사 해 둔다.

## 3. Allocation Strategies

- 어떻게 파일들을 디스크 블록들에 매핑하는가?
  - 가변 크기의 주소 공간을 physical memory 에 매핑하던 것과 비슷하다.
  - 같은 전략: 물리적인 자원을 추상화하여 매핑한다.
- Issues (고려해야 할 점들)
  - fragmentation (대부분 external)
  - 파일이 시간이 지남에 따라 커질(grow) 수 있어야 한다.
  - sequential access 의 성능
  - random access 로 데이터 블록을 찾는 속도
  - 데이터 블록들을 track 하는데 사용되는 metadata의 오버헤드

### Contiguous Allocation

각각의 파일들을 디스크에 연속된 블록으로 할당한다.

- 메타데이터: <starting block #, length>
- CD-ROM 에서 많이 사용된다.
- **장점:**
  - sequential access 에 성능이 매우 좋다.
  - random access 시 필요한 연산이 적다.(단순)
  - 메타 데이터 오버헤드가 작다.
- **단점:**
  - 끔찍한 external fragmentation
  - 파일이 grow 하기 위해 파일의 위치를 옮겨야 할 수도 있다.

### Linked Allocation

고정 크기의 블록들을 Linked-list 형태로 할당한다.

- 메타데이터: <starting block #>
- 각각의 블록들은 다음 블록의 pointer 를 가지고 있다.
- ex) TOPS-10, Alto

- **장점:**

  - external fragmentation 이 없다
  - 파일이 쉽게 grow 할 수 있다.

- **단점:**

  - random access 성능이 구리다.
  - 블럭마다 pointer 가 들어가서 낭비가 있다.
  - fragile: 한 블럭의 포인터만 손상되어도 그 뒤로는 다 접속불가 상태가 된다.

- Sequential access 성능은 데이터의 상태에 따라 성능이 달라진다.

  -> 연속적으로 붙어있으면 성능이 좋고, 멀리 떨어져 있으면 성능이 낮다.

### File Allocation Table (FAT)

Linked allocation 의 한 종류

- 디스크상의 모든 파일의 linked-list 정보를 FAT 에 유지한다.
- FAT 은 통째로 메인 메모리에 캐싱된다. (디스크 접속을 피하기 위해)
- 메타데이터: <starting block #> + FAT
- ex) MS-DOS, Windows (FAT12, FAT16, FAT32)

- **장점:**

  - random access 성능이 향상되었다.

- **단점:**

  - 만약 파일 시스템 크기가 커진다면?

    (만약 1TB 파일 시스템이면, FAT 만 수GB 크기가 된다. RAM 개많이 먹는다.)

### Indexed Allocation

고정 크기의 블록들을 각 파일에 할당한다.

- 메타데이터: 블록 포인터들의 배열

- 각각의 블록 포인터는 대응하는 데이터 블록을 가리킨다.

  (각 inode 안에 block pointer 배열이 있다)

- **장점:**

  - external fragmentation 이 없다.
  - 파일이 쉽게 최대 파일 사이즈까지 자랄 수 있다.

- **단점:**

  - Random access 가 지원된다.

  - 메타 데이터에 대한 오버헤드가 크다.

    (사용되지 않는 포인터에 대해 낭비되는 공간이 많다. 대부분의 파일은 크기가 작다.)

### Multi-level Indexing

Indexed allocation 의 한 종류이다.

- 데이터 블록을 가리키는 포인터들의 hierarchy 를 동적으로 할당한다.

- 메타데이터: direct pointers + indirect pointers

  실제 아이노드상에서 direct blocks 들의 공간이 제일 많이 할당 되어 있다.

- ex) Unix FFS, Linux Ext2/3

- 사용되지 않는 포인터들에 대해 공간을 낭비하지 않는다.

- indirect block 들의 포인터를 주소 연산을 위해 읽어야 한다.

  (extra disk read 가 필요하다)

  (indirect block 들을 메인 메모리에 캐시해 둔다)

### Multi-level Indexing 계산 해보기

- 한 블럭의 크기: 4KB

- 디스크 주소: 4Byte

- 12개의 direct 포인터

- single indirect 한 블럭을 추가했을 때:

  - 한 블럭에 담을 수 있는 포인터: 4KB / 4B = 1024개

  - 최대 파일 크기: 4KB * (12 + 1024) = 4144KB

- double indirect 한 블럭을 추가했을 때:

  - 최대 파일 크기: 4KB* (12+ 1024 + 1024^2) = 약 4GB

- triple indirect 한 블럭을 추가했을 때:

  - 최대 파일 크기: 4KB* (12+ 1024 + 1024^2 + 1024^3) = 약 4TB

### Extent-based Allocation

각 파일을 여러개의 연속적인 덩어리로 할당한다.

- 덩어리들을 multi-level tree 구조로 정렬한다.

- 각 leaf node: <starting block #, extent size>

- Example: Linux Ext4

- **장점**

  - 용납가능한 수준의 external fragmentation

  - 준수한 sequential 성능

  - 비교적 작은 메타데이터 오버헤드

    multi-level indexing 과 비교해, 모든 각각의 블럭들의 포인터를 기억할 필요 없이,

    덩어리의 시작 주소와 덩어리의 크기만 기억하면 된다.

- **단점?**

  - Random access 시 약간의 연산이 필요하다.

## 4. Directory Organization (디렉토리 구조)

- 공통 디자인
  - 디렉토리는 특별한 종류의 파일이다. (디렉토리 엔트리들을 가지고 있음)
  - 큰 디렉토리는 데이터 블록 여러개를 사용한다.
  - 아이노드의 비트를 이용해 디렉토리를 파일과 구분한다.
- 다양한 format 이 사용될 수 있다.
  - Table (고정 길이 엔트리) 또는 linear list
    - entry 를 찾는데 linear search 를 해야 한다.
  - Tree
    - 평균 search 시간을 줄이기 위해, sorted directory listing 을 쉽게 만들기 위해 엔트리가 정렬될 수 있다.
  - Hash table
    - 빠르다, 그러나 파일의 갯수가 증가함에 따라 증가할 수 있어야 한다.

## 5. Reading a File

실행과정(access path)을 이해 해 보자.

open("/foo/bar", O_RDONLY) 를 실행한다고 가정해보자.

1. 파일 시스템은 파일에 대한 전체 경로명을 갖고 있다.

   파일 시스템은 경로명을 따라가서(traverse) 원하는 아이노드를 찾는다.

2. 루트 디렉터리'/' 에서 시작한다.

   대부분의 Unix 시스템에서 root inode number 는 2 이다.

3. 파일 시스템은 inode number 2 를 포함한 블록을 읽는다.

4. 아이노드에서 데이터 블럭의 포인터를 추출한다. (루트 디렉토리의 내용이 들어있다)

5. foo 라는 항목을 찾는다.

   디렉토리에 파일의 갯수가 많으면, 한 블럭에 다 저장할 수 없어서 여러 블럭에 저장되어있을 수 있다.

   그렇다면 여러 블록들을 뒤져서 찾아야 할 수도 있다.

6. foo 의 아이노드 번호를 찾는다.

7. foo의 아이노드를 찾는다.

8. 같은 방법으로 bar 를 찾는다.

9. permission 을 확인하고, file descriptor 를 이 프로세스에 할당한다.

   그리고 file descriptor 를 user 에게 리턴한다.

### read() 의 issue

open() 이후에는, read() 시스템 콜을 통해 파일을 읽는다.

첫 번째 읽기는 아이노드를 통해 해당 블럭의 디스크 상의 위치를 파악한 후 해당 블럭을 읽는다.

- 파일을 읽은 후 파일을 마지막으로 읽은 시간을 아이노드에 기록한다.

- read() 는 open-file-table 에서 해당 파일 디스크립터에 대한 오프셋을 갱신한다.

  (파일 오프셋은 파일을 읽거나 쓸 때, 해당 작업을 수행할 위치를 저장하는 변수이다.)

### 파일을 close 할 때

할당된 파일 디스크립터를 해제한다.

이 때 디스크 I/O 는 발생하지 않는다.

## 6. Writing a File

파일은 블록을 할당할 것이다. (block 을 overwrite 하는게 아니라면)

- 데이터 블록, 데이터 비트맵을 업데이트 해야 한다.

- 다섯 번의 I/O 를 발생시킨다.

  1. 데이터 비트맵을 읽는다.

  2. 비트맵에 write 한다.
  3. 아이노드를 읽는다
  4. 아이노드에 write 한다.
  5. 데이터 블록에 write 한다.

- 파일을 생성하기 위해 디렉토리에 공간을 할당해야 하는데, 높은 I/O traffic 을 발생시킨다.

## 7. Caching and Buffering

- 파일을 읽거나 쓰는 것은 많은 I/O 를 사용하기 때문에 비싸다.

  - 예를들어, pathname 이 길면 (/1/2/3/..../100/file.txt)
    - 디렉토리의 아이노드를 읽기 위해 한번, 그리고, 데이터를 읽기 위해 최소 한번의 읽기가 필요하다.
    - 파일 하나를 읽기 위해 말 그대로 수백번의 read 가 발생한다.

- I/O 트래픽을 줄이기 위해, 파일 시스템은 시스템 메모리 (DRAM) 을 공격적으로 캐시로 사용한다.

  - 초기의 파일시스템은 고정 사이즈의 캐시를 자주 사용되는 블럭들을 캐시하기 위해 사용했다.

    - 메모리의 static partitioning 은 낭비가 될 수 있다.

  - 현대의 시스템들은 dynamic partitioning approach, unified page cache 를 사용한다.

    -> 용도에 따라 DRAM을 process, buffer cache 등으로 구분하지 않고, page cache 라는 하나의 통합된 방식으로

    유동적으로 배정해서 사용.

- Read 시의 I/O 는 큰 cache 를 사용하면 피할 수 있다.

- 반면 Wirte 의 경우는 disk 에 영구적으로 남아야 하므로, cache 를 하더라도, I/O 를 줄이지 못한다.

- 그래서 파일 시스템은 write 성능 향상을 위해 write buffering 을 사용한다.

  - wirte 를 delay 한다. (I/O 작업들을 매번 다 하지 않고 약간 모아서 한번에 한다.)
  - write 를 buffering 함으로써, 파일시스템은 후속 I/O 를 스케줄 해둘 수 있다.

- 어떤 application 은 데이터를 **fsync()** 를 통해 디스크에 **force flush** 한다.

---

