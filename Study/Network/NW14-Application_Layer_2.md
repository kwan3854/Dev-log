# Application Layer - 2

## 1. Cookies: keeping "state"

- 서버 (amazon)
  1. 사용자를 위해 쿠키 ID를 생성해서 사용자에게 보낸다. (set-cookie: 1678)
  2. Backend database에 해당 번호에 대한 정보를 저장할 공간을 만든다.
- 클라이언트
  1. 받은 ID 로 쿠키 생성 (amazon 1678)
  2. 다음부터 사이트에 접속시에 해당 사이트에 대한 쿠키파일이 있으면 header 에 번호 적어서 보냄. (cookie: 1678)

### 쿠키의 사용처

- autorization
- shopping carts
- recommendations
- user session state
- ...

### 쿠키와 프라이버시

- 쿠키는 사이트가 너에대해 엄청나게 많이 알게 해준다.
- 사이트에 이름이나 e-mail 을 적었을 수도 있다

## 2. Web caches (proxy server)

목표: 클라이언트의 리퀘스트를 오리진 서버의 개입 없이 만족하는 것.

- 프록시 서버는 보통 기관 내부에 있다. ISP 가 설치함. (예: 숭실대 내부)
- 보통 최근, 자주 이용된 것이 저장되어(캐싱)있다.
- 웹 서버의 역할과 웹 클라이언트의 역할 둘 다 한다.
  - 웹 서버 역학: 자료가 캐싱되어 있을 때 자료를 보내줌.
  - 웹 클라이언트 역할: 자료가 캐싱안되어 있을 때 직접 오리진 서버에 요청.

### 왜 web caching 을 사용하는가?

- 클라이언트의 리퀘스트에 대해 response time 을 줄이기 위해
- 트래픽을 줄이기 위해.
- content provider 가 후져도, 비교적 효과적으로 content를 전송할 수 있다.

### 캐싱의 예시

**가정**

- 평균 object size: 1M bits
- 평균 browsers to origin servers 의 request rate: 15/sec
- 평균 브라우저의 데이터 rate: 1*15 = 15 Mbps
- Institutional router 에서 어떤 origin server 까지의 RTT: 2 sec
- access link rate: 15 Mbps

**결과**

- LAN utiliation: 15%
- access link utilization = **100%**
- total delay = Internet delay + access delay + LAN delay = 2 sec + **minutes** + msecs

- access link 가 bottle neck 이 되고 있다.

### 캐싱의 예시: 개선 (fatter access link)

**가정 (변경)**

- access link rate: ~~15 Mbps~~ 150 Mbps

**결과**

- access link utilization = ~~100%~~ 10%
- total delay = Internet delay + access delay + LAN delay = 2 sec + ~~minutes~~ **msecs** + msecs
- 문제가 해결되었다.

- **그러나 access link 의 속도를 올리는 것은 비싸다!**

### 캐싱의 예시: 개선 (install local cache)

- web cache 는 비교적 싸다.
- web cache 용량이 클수록 hit율이 올라간다.
- hit 율이 올라갈 수록 delay는 줄어든다.

## 3. DNS: domain name system

- Distributed database

- Applicatrion-layer protocol:

  (http도 application-layer protocol의 일종)

  사람이 알아보는 주소를 라우터가 이해하는 주소로 변환하는 역할

  google.com -> IP 주소

### DNS services

- hostname -> IP address translation
- host aliasing
- mail server aliasing
- load distribution
  - 하나의 호스트네임을 여러 IP로 분산시킬 수 있음.

### 왜 DNS 를 중앙화 하지 않는가?

- single point of failure
- traffic volume
- distant centralized database
- maintenance
- **doesn't scale**

### DNS: a distributed, hierarchical database

- Root DNS Servers
- TLD (Top-level domain) Servers
- Autoritative Servers
- Local Servers

### DNS name resolution

- iterative query

  난 모르겠고, 쟤한테 물어봐

- recursive query

  난 모르겠고, 답만 알려줘

