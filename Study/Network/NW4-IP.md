# IP 주소체계

- 32비트의 이진수
- 사용 가능한 총 IP 주소의 개수는 2^32개 (모자란 갯수지만 잘 돌아간다.) (IPv6 로 넘어가는 중)
- 기본적으로 클래스로 구분되는 주소체계를 취한다.
- 호스트 컴퓨터 A는 인터넷 프로토콜 주소체계를 사용해서 호스트 컴퓨터 B에 데이터 패킷을 전송

---

IP 주소는 2가지로 구분된다.

- 네트워크 주소

  Internet 에서는 네트워크 주소로 라우팅

- 호스트 주소.

  network 내부에서는 호스트 주소로 라우팅 

---

### IP 주소의 클래스

- A 클래스
  - 첫 번째 바이트 (8비트) 까지가 네트워크 주소
  - 0~ 으로 시작
  - 전체 IP 주소에서 50%
- B 클래스
  - 두 번째 바이트 (16비트) 까지가 네트워크 주소
  - 10~ 으로 시작
  - 전체 IP 주소에서 25%
- C 클래스
  - 세 번째 바이트 (24비트) 까지가 네트워크 주소
  - 110~ 으로 시작
  - 전체 IP 주소에서 12.5%
- D 클래스
  - 1110~ 으로 시작
- E 클래스
  - 1111~ 으로 시작

D, E 클래스는 좀 다르다. 일반적인 주소 아님

---

네트워크 주소를 뺀 나머지 비트로 호스트 주소를 나타냄

- A: 2^24 개
- B: 2^16개
- C: 2^8개

---

## 서브넷 기법

- 서브넷이란?
  - 한 네트워크가 너무 커서 관리하기 힘들때 네트워크 안에 네트워크를 만들어서 관리한다.
  - 하나의 IP 네트워크 주소를 네트워크 내부에서 적절히 분할하여 실제로는 여러 개의 서로 연결된 지역 네트워크로 사용하는 것.
- 서브넷팅(Subnetting) 이란?
  - 자신의 회사나 조직에 할당된 네트워크 ID의 IP주소들을 가지고 내부적으로 여러 개의 서브넷을 만드는 과정

---

### 서브넷 마스크

- 서브넷 마스크: 하나의 IP 네트워크 주소를 다시 여러 IP 서브 네트워크로 나눔
- 클래스에 대한 표준 네트워크 마스크
  - 클래스 A (8개의 네트워크 비트) : 255.0.0.0
  - 클래스 B (16개의 네트워크 비트) : 255.255.0.0
  - 클래스 C (24개의 네트워크 비트) : 255.255.255.0

---

### 서브넷 마스크 예제

> 호스트 IP 주소가 128.66.12.1이고 서브넷 마스크가 255.255.255.0 일 때, 이들의 관계를 설명하라.

- 문제에서
  - IP: 128.66.12.1
  - Mask: 255.255.255.0
- 서브넷 128.66.12.0 상의 호스트는 1임.
- 128은 10000000 으로서 B 클래스를 나타냄 (기본 마스크는 255.255.0.0)
- 그러므로 128.66.12 까지가 네트워크 ID, 마지막 바이트인 1이 호스트 ID

---
