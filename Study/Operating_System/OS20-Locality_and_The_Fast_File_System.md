# Locality and The Fast File System

## 1. How to Manage a Persistent Device

- Persistent storage
  - 정보를 영구적으로 저장, 파워가 나가도 데이터를 유지.
  - ex) Hard Disk Drive, Solid State Drive.
- Storage Management
  - OS가 persistent storage 를 어떻게 다루어야 하는가?
  - 어떤 API 들이 있는가?
  -  구현하는데 어떤 중요한 요소들이 있는가?
- 두 가지 storage virtualization 의 key 추상화 요소가 있다.
  - File
  - Directory

## 2. Old UNIX File System

- 간단한 구조를 사용했다. (VSFS와 비슷)
- 성능이 구렸다.
- 시간이 지날수록 성능이 더 구려졌다.
- 나중에는 disk bandwidth 에 2% 밖에 사용못하는 지경까지 갔다.

### Fragmentation

External fragmentation 이 심해서, 시간이 지날 수록 파일들이 조각조각 난다.

**어떻게 Disk 의 데이터를 정렬해야 성능을 개선할 수 있을까?**

## 3. FFS: Disk Awareness is the Solution

Oraganizing Structure: **The Cylinder Group**

- on-disk structure 를 바꾼다.
- 디스크를 Cylinder group 들로 나눈다.
- 파일과 디렉토리를 cylinder group 으로 할당한다.
- **아이노드 비트맵** 과 **데이터 비트맵** 이 실린더 그룹마다 있다.
- cylinder group 마다 **superblock** 이 있다. (reliability 때문에)

### Policies: How To Allocate Files and Dirs

- 연관되는 것들은 함께 둔다

- 디렉토리의 배치

  - Cylinder group 을 찾는다

    - 할당된 디렉토리의 수가 적고 (그룹들 간에 디렉터리에 대한 균형을 맞추기 위해),

      사용할 수 있는 아이노드의 수가 많은 (많은 파일들을 할당할 수 있으라고)

      실린더 그룹을 선택한다.

  - 디렉토리 데이터와 아이노드를 해당 그룹에 저장한다.

- 파일의 경우

  - **아이노드와 파일의 데이터 블럭을 같은 그룹에 할당** 하여 아이노드와 데이터 간의 긴 탐색을 방지.
  - **동일한 디렉터리 내의 모든 파일들은** 해당 디렉터리가 존재하는 실린더 그룹에 **함께 저장.**

## 4. Locality of Files

namespace 지역성이 실제 있는가?

> 두 open 사이의 거리와 빈도의 관련성

- 40% 의 파일이 같은 파일이거나 같은 디렉토리의 파일이었다.
- 따라서 FFS 는 합당하다.

---

