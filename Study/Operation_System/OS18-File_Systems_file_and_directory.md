# File Systems: file and directory

> **핵심 질문: 어떻게 영속 장치를 관리하는가**
>
> 운영체제가 영속 장치를 어떻게 관리해야 할까?
>
> API 들은 어떤 것이 있는가?
>
> 구현의 중요한 측면은 무엇인가?

### 영속 장치 (Persistent Storage)

- 파워가 꺼져도 데이터를 보존한다.
  - 하드 디스크 드라이브 (HDD)
  - 솔리드 스테이트 드라이브 (SSD)
- storage 의 가상화에 두 가지의 핵심 추상화가 있다.
  - File
  - Directory

## 1. 파일과 디렉터리

### 파일 (File)

- 파일은 단순히 읽거나 쓸 수 있는 순차적인 바이트의 배열이다.

- 각 파일은 **저수준 이름(low-level name)** 을 갖고 있다.

  보통은 숫자로 표현되지만 사용자는 그 이름에 대해서 알지 못한다.

  이 저수준 이름을 **아이노드 번호(inode number)** 라고 부른다.

-  대부분의 시스템에서 운영체제는 파일의 구조를 모른다. (그림파일인지, c파일인지, 텍스트인지...)

  파일 시스템은 이 데이터를 안전하게 저장, 전달 하는 역할을 한다.

### 디렉터리 (Directory)

- 디렉터리도 저수준의 이름(아이노드 번호)를 갖는다.

- 파일과는 달리 내용이 구체적으로 정해져 있다.

  <사용자가 읽을 수 있는 이름, 저수준의 이름> 쌍으로 이루어진 목록을 가지고 있다.

  예) <"Documents", "10">, <"Desktop", "11"> ...

- 디렉터리의 각 항목은 파일 또는 다른 디렉터리를 가리킨다.

- **디렉터리 트리(directory tree)** , 또는 **디렉터리 계층(directory hierachy)** 를 구성할 수 있다.

- 디렉터리 계층은 **루트 디렉터리(root directory)** 부터 시작한다. (UNIX 에서는 '/' 로 표현)

  예) /Users/kwan/Downloads/dog.png

## 2. 파일 시스템 Basics

### 각각의 파일에는 아래의 것들이 들어있다.

- 파일 데이터
  - 바이트의 연속
  - 파일 시스템은 보통 어떤 데이터인지 신경 안쓴다.
- 파일 속성(attributes) (메타데이터 or inode)
  - 파일 사이즈
  - Owner
  - Access control lists
  - Timestamps
  - Block locations, ...

### 파일 이름

- 루트부터 해당 파일까지의 full pathname

  ex) open("/etc/passwd", O_RDONLY);

### Goals

- Performance
- Reliability
- Scalability

### Design issues

- 메타 데이터에 어떤 정보를 넣어놔야 하는가?

- 파일 이름으로부터 어떻게 메타 데이터를 위치시켜야 하는가?

  Pathname -> metadata

### 데이터 블록들을 어떻게 위치시켜야 하는가?

<Metadata, offset> -> Data block

### 메타 데이터와 데이터 블록들을 어떻게 관리해야 하는가?

allocation, reclamation, free space management, ... etc.

## 3. Pathname Translation

### open("/a/b/c", ...)

- '/' 루트 디렉터리를 연다 (항상 찾을 수 있다)
- 디렉터리 'a' 를 찾아서 'a' 의 위치를 가져온다.
- 디렉터리 'a'를 연다. 'b'를 찾는다. 'b'의 위치를 가져온다.
- 디렉터리 'b'를 연다. 'c'를 찾는다. 'c'의 위치를 가져온다.
- 파일 'c'를 연다.
- 각 과정에서 permission 체크가 이루어진다.

파일 시스템은 디렉터리 path를 타고 내려가는데 많은 시간을 소모한다.

따라서 OS가 퍼포먼스 증가를 위해 한번 열었던 파일의 위치를, prefix lookup 들을 캐시하고 있는다. (DRAM 에 캐시하고 있음)

파일 시스템은 open 명령을 받으면, 파일 위치를 찾아서 이 파일의 메타데이터를 저장해 놓은 struct 의 주소를 넘겨준다.

사실 이 주소가 그대로 가는게 아니라, fd table에 이 주소가 저장되고, fd table의 index 번호가 전달되게 되는 것.

## 4. Ensuring Persistence

파일 시스템 버퍼들을 메모리에 적는다. (DRAM 에 있던 정보를 Storage 에 저장.)

- 리눅스에서는 30초마다 자동으로 storage에 적는다.

- fsync() 를 하면, 우리가 Crtl+S 한것과 비슷하게, storage에 저장한다.

  모든 dirty data를 디스크에 저장하는것이다.

  **파일의 데이터 + 메타데이터 정보** 를 내리는 것이다.

  메타데이터를 내리는 부분이 오버헤드가 엄청나게 크다.

  (해당 파일의 데이터, 그 파일의 메타데이터와 관련된 모든 데이터를 다 내려야 함)

- fdatasync()

  메타데이터는 안내리고 파일 데이터만 내리는 것.

  fsync() 와 다르게 recursive 하게 계속 데이터 write 가 발생하지 않음. (메타 데이터 저장 안하니까)

```c
int fd = open("foo", O_CREAT | O_WRONLY | O_TRUNC);
// 파일 시스템 보고 파일을 찾아서 그 메타데이터 struct 주소를
// fd table 에 저장하고 해당 fd table index를 달라고 함
// 그 번호가 int fd에 저장됨

int rc = write(fd, buffer, size);
// 이제부터는 fd 값으로 파일에 접근하면 됨.
// buffer 에 적고싶은 내용을 적는다.
// 아직 DRAM 에만 저장되어 있고 storage 에는 저장되지 않은 상태
// 리눅스에서 pdflush 라는 데몬이 30초 마다 저장하지만, 보장되지는 않음

rc = fsync(fd);
// 정보를 다 내린다.

close(fd);
```

## 5. Hard Link VS Symbolic Link

### Hard link: 진짜로 파일 이름을 2개로 만드는 것.

```sh
ln old.txt new.txt
```

- 링크 카운트가 0이 되면 비로소 inode가 삭제된다.

  예) old.txt 를 삭제해도, new.txt 라는 이름으로 그대로 접근 가능. (파일은 그대로 살아있음)

### Symbolic (soft) link: 바로가기 만들기

```c
ln -s old.txt new.txt
```

- 윈도우 환경에서의 '바로가기' 와 동일하다.
- old.txt 를 삭제하면 new.txt 를 클릭해도 파일에 접근이 불가능하다. 파일이 삭제되었기 때문.
- 파일이 실제로 엄청 깊은 디렉터리에 있으면 접근하기 쉬운 곳에 바로가기 만들고 사용.

## File System 생성과 Mounting

### 파일 시스템의 종류

- FAT (옛날에 fat32 사용)
- exFAT (최근 윈도우 시스템에서 사용, 맥도 지원)
- NTFS (윈도우에서 사용)
- APFS (애플 맥os에서 사용)
- HFS
- EXT3
- EXT4
- ZFS
- ...

이런 파일 시스템을 생성해서,

특정 디렉토리에 마운트 한다.

### 예시

새로운 스토리지를 구매해서 리눅스에 연결하면,

/dev/sda 라고만 있을 것이다.

이것을 두개의 파티션

/dev/sda1

/dev/sda2

으로 나눈다.

파티션 /dev/sda1 을 EXT3 파일 시스템으로 만든다. (makefs)

아직 마운트 되지 않았다.

이 파일 시스템은 자체적으로 디렉터리 구성을 가지고 있다.

(루트 아래에 a와 b 디렉터리가 있고 각각 foo 라는 파일이 들어 있다. /a/foo , /b/foo)

 이 파일 시스템을 /home/users 라는 위치에 마운트하자.

```sh
prompt> mount -t ext3 /dev/sda1 /home/users
```

이제 새로운 파일 시스템을 기존의 디렉터리 경로를 통해 접근할 수 있다.

```sh
prompt> ls /home/users/
a b
prompt> cd /home/users/a
prompt> ls
foo
```

---

