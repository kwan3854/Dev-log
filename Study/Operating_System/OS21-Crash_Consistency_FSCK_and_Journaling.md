# Crash Consistency: FSCK and Journaling

## 1. Crash-consistency Problem

### 개요

- 파일 시스템 데이터 구조는 항상 유지되어야만 한다.
  - 파워가 나가도 살아남을 수 있어야 한다.
- Crash-consistency problem
  - 파일 시스템이 직면한 큰 문제 중 하나.
  - crash 가 발생해도 어떻게 update 를 하는가
- 이를 극복하기 위한 접근법
  - FSCK (File System Checker)
  - Journaling

### 자세한 예시

- 하나의 데이터 블록을 이미 존재하는 파일에 append 한다고 하자.

- append 동작에 관계된 구조들
  - inode bitmap
  - data bitmap update
  - inode
  - data block

- 하나의 data block 을 append 하기 위해 3개의 on-disk structure 를 업데이트 해야 한다.
  - data bitmap
  - inode
  - data block
- 파일 시스템은 디스크에 세가지 개별적인 write 를 하게 된다.
  - inode, bitmap, datablock 에 각각.
  - write() 시스템 콜을 user 가 사용했다고 바로 write 가 발생하지는 않는다.
  - dirty inode, dirty data bitmap, new data 는 메인 메모리에 한동안 있는다.
  - 파일시스템은 주기적으로 write 를 발생시킨다. (5초 또는 30초 주기로)
  - 이러한 업데이트가 disk 에 진행되는 동안 방해를 받으면 crash 가 생길 수 있다.

### Crash Scenarios

1. Data block 만 디스크에 write 되었을 때

   - 어떤 아이노드도 그 블록을 가리키지 않는다.
   - 어떤 비트맵도 그 블록이 할당되었다고 표시하지 않는다.
   - **문제가 되지 않는다.**

2. Inode 만 디스크에 write 되었을 때

   - 디스크에서 쓰레기값을 읽어온다.
   - File-system consistency 에 문제 생김
     - On-disk bitmap 은 할당되지 않았다고 하는데, inode 는 할당되었다고 표시됨.
   - **문제가 된다.**

3. Bitmap 만 디스크에 write 되었을 때

   - 비트맵이 해당 데이터 블록이 할당되었다고 표시한다. 그러나 그것을 가리키는 아이노드는 없다.

   - File-system inconsistencty 문제 생김

   - **Space leak 이 발생**

     해당 데이터 블록은 파일 시스템에서 더이상 영원히 사용되지 못하게 된다.

4. Inode 와 bitmap 이 디스크에 write 되었으나, data block 은 안되었을 때

   - 파일 시스템의 메타데이터는 앞뒤가 맞는다.
   - 그러나 **해당 데이터 블록에는 쓰레기값이 있다.**

5. Inode 와 data block 은 디스크에 write 되었으나, bitmap 은 안되었을 때

   - 아이노드는 맞는 데이터를 가리킨다.
   - 하지만, **아이노드와 비트맵 사이에 앞뒤가 맞지 않게 된다.**

6. Bitmap 과 datablock 은 디스크에 write 되었으나, inode 는 안되었을 때

   - 데이터 블록은 write 되었고, bitmap이 해당 데이터 블록의 사용을 잘 표시한다.
   - 그러나, **아이노드와 비트맵사이에 앞뒤가 맞지 않게 된다.**
     - 해당 파일을 가리키는 아이노드가 없다.

### 발생하는 문제 요약

- 파일 시스템 data structure 에서의 inconsistency 발생
- Space leaks
- 사용자에게 쓰레기 데이터가 가게 되는 문제.

### 해결법

- 파일시스템을 한 상태에서 다른 상태로 atomic 하게 옮겨야 한다.
- 디스크는 한번에 4KB 씩 write 를 해서 쉽지 않다.

## 2. File System Checker (FSCK)

### 개요

- 초기 파일 시스템에서 사용되었다.
- Lazy approach
  - inconsistency 문제가 발생하게 내버려 두고, 문제가 발생하면 나중에 고치는 방식 (재부팅 할 때 고침)
- fsck tool
  - Inconsistency 문제를 찾고, 고친다.
  - 모든 문제를 고치진 못한다.
    - 파일 시스템이 consistent 해 보이지만, 아이노드가 쓰레기 데이터를 가리키는 경우가 있음.

### FSCK 가 무엇을 하는가 요약

- Superblock

  - 파일 시스템 사이즈가 할당된 블록들의 수 보다 큰지 확인한다.

    -> 만약 그렇다면 문제가 있는 것

  - 다른 대체 superblock 을 사용할지 결정한다.

    - superblock 의 복제본을 여러 개 가지고 있다.

- Free blocks

  - Inode, indirect blocks, doubly indirect blocks 들을 스캔한다.
  - 파일 시스템에서 어떤 블록들이 할당되어있는지 알아낸다.
  - 올바른 버전의 할당 비트맵을 만든다.
  - 아이노드와 비트맵 사이의 incosistency 를 해결한다.

- Inode state check

- Inode links

- Duplicates

- Bad blocks

- Directory checks

### FSCK 의 단점

잘 작동하는 FSCK 를 만들기 위해서는 파일 시스템에 대한 이해도가 매우 높아야 한다.

- 모든 예외 케이스에 대해 잘 동작하는 코드를 만드는 것은 어렵다.

- 너무 느리다:

  디스크의 용량이 커질수록 성능이 너무 떨어진다.

- High level 관점에서 FSCK 의 전제를 보면, 비이성적이다.

  - 집 열쇠를 찾으려고 매번 집 전체를 뒤져야 하는 꼴.

## 3. Journaling

### 개요

- Journaling file system 의 예
  - Cedar, ext3, ext4, reiserfs, JFS, XFS, NTFS
- 데이터베이스에서 사용하던 아이디어를 가져와서 사용한다. (write-ahead logging 방식을 가져옴)
  - 본체에 overwriting 하기 전에, 먼저 정해진 장소에 미리 한번 적고나서 본체에 적는다.
  - 만약 crash 가 발생하면, 무엇을 고쳐야 하는지, 어떻게 고쳐야 하는지를 미리 적어놓은 것을 보고 판단할 수 있다.
- 업데이트를 할 때 마다 매번 두번씩 일을 하게 된다. 그러나 recovery 에 필요하던 시간을 매우 많이 줄일 수 있다.

### Journaling 의 기본 동작법

- inode, bitmap, data block 정보를 미리 한번 적는다.
- 본체에 데이터를 적기 전에 미리 한번 메모를 하는 방식
  - checkpoint: 본체에 적기
  - commit: 미리 logging

### Commit

- Transaction Begin block (TxB)
  - transaction 의 시작점 표시
  - TID(Transaction ID), 마지막 블록의 주소, 등을 포함
- Middle three blocks
  - 실제 본체에 적어야 하는 내용과 완전히 같은 블록들이다.
  - Physical logging
- Transcaction End block (TxE)
  - Transaction 의 끝을 표시
  - TID 를 포함하고 있다.

### Checkpoint

- Transaction 이 안전하게 disk 에 이루어지면, 파일시스템에 본체에 overwrite 를 한다.
- 파일 시스템에 checkpoint 를 수행하기 위해
  - 적어 놨던 inode, bitmap, data block 들을 해당 위치에 write 한다.
  - 만약 write 가 완전히 성공적으로 이루어졌다면, 파일시스템은 성공적으로 checkpoint 를 수행한 것이다.

### Crash recovery

- 파일 시스템은 journal 의 데이터를 crash 부터의 복구에 사용한다.
- Recovery process 가 부팅시에 log를 스캔한다.
- Case 1. Committed transactions (journaling 은 다 했을 때)
  - 데이터 본체의 위치에 transaction 블록을 적으면 된다.
- Case 2. Pending transactions (journaling 다 안되었을 때)
  - TxE 블록이 없을 때, 이 경우라고 보면 된다.
  - transaction 을 그냥 안하고 스킵한다.

### Journal Write 시에 생각해봐야 하는 점들

- 어떤 방식으로 TxB, I[v2], B[v2], Db, TxE 를 journal 시에 write 할지

  - 한번에 하나씩 각각?
  - 모든 블록들을 한번에?

- 한번에 다섯블록을 전부 write 하는것은 빠르다. 그러나 위험하다.

  - Disk 는 자기 나름대로 write 작업시 scheduling 해서 작업한다.

    (우리가 생각한 순서대로 동작한다는 보장 없음)

  - 만약 TxB 를 적고 TxE 도 적고, 중간의 I[v2], B[v2] 까지 적었는데 crash 발생하면? (Db 는 못적음)

    - 문제가 발생할 수 있다. 반드시 나머지 다 적고나서 TxE 가 적게 순서가 정해져야 한다.

### Commit 을 두 동작으로 나눈다

1. 우선, TxE 블록을 제외한 모든 블록을 write 한다.
2. 앞선 write 가 끝난 후, TxE 블록을 write 한다.

### 데이터 매번 두번씩 적기 힘들다

- 복구가 이제 빨라졌다.
  - Journal 영역만 scan 하고 몇개의 transaction 만 수행하면 복구 가능하다.
  - 반면 FSCK 의 경우에는 디스크 전체를 스캔했어야 했다.
- 보통의 경우에는 파일시스템이 더 느려졌다.
  - 매번 데이터를 디스크에 두번 씩 write 해야 한다. write 동작은 비용이 비싼 동작이다.
- Journaling modes
  - Data journaling
    - 데이터와 메타데이터를 journal 한다.
  - **Ordered journaling (or metadata journaling)**
    - **메타데이터만 journal 한다.**
    - 현대의 상용 파일 시스템이 보통 사용하는 방식
    - **crash 시나리오 1번에서 데이터 블록만 적었을 때는 괜찮았던 점을 이용함.**

### Metadata Journaling

- User data 를 journal 에 적지 않는다는 점만 빼면 거의 똑같음

- 데이터 블록을 journal 에 적는 대신, 바로 본체쪽에 데이터 적음

  (overwrite 는 아니고, 기존 데이터는 냅두고 다른 위치에 따로 적음)

  - 데이터 블록을 두번 적던것을 한번만 적으면 됨
  - journaling 때문에 발생하던 I/O 부하를 줄일 수 있다.

- 그렇다면 data block 을 디스크에 언제 적어야 할까?

  - 반드시 본체에 데이터 블록 write 를 먼저 한다.
  - 그 이후 metadata 를 journaling 한다.
  - journaling 완료 전에 crash 발생하면, 이전 상태로 복구하면 됨.
  - journaling 완료 후에 crash 발생하면, 이후 상태로 복구.

- Data 를 적는 순서가 반드시 지켜져야 한다.

- 만약 데이터 블록 write 전에 transaction 이 완료되면?

  - 만약 데이터 블록 적는 도중이나 적기 전에 crash 발생하면,
  - 파일 시스템에 inconsistent 문제 발생
    - 아이노드가 결국 쓰레기 데이터를 가리키게 된다.

---

