# Application Layer - 1

프로그래머들 사이의 규약, 약속들에 대해 배운다.

- 어떤 소켓?
- 어떤 데이터 포멧?
- 어떤 프로세싱 순서?

## 1. 네트워크 어플리케이션의 규칙

### 네트워크 앱 생성

- 이런 프로그램을 작성한다.
  - 서로 다른 end 시스템에서 동작 하는 프로그램
  - 네트워크를 통해 통신하는 프로그램
  - ex) 브라우저를 통해 통신하는 웹 서버 소프트웨어

- 네트워크 코어에 대해서는 신경쓸 필요 없다.

  그것들은 알아서 잘 동작한다고 가정하고,

  end to end 만 생각한다.

### Client-server architecture

- **server**
  - always-on host
  - permanent IP address
  - data centers for scaling
- **clients**
  - server 와 통신
  - 필요할 때만 연결 할 수도 있음
  - dynamic IP 을 가질 수도 있음
  - 서로 직접 통신하지 않음

### Sockets

- 한 프로세스 안에 소켓이 여러 개 있을 수 있다.
- 프로세스는 프로세스 내부의 소켓을 통해 메시지를 보내고 받는다.

### App-layer protocol defines

- 데이터 포멧을 정의 해야 한다.
- 프로세스가 어떻게 메시지를 보내고 답할지 규칙을 정해야 한다.
- open protocols
  - HTTP, SMTP, FTP ...
- Proprietary protocols
  - Skype...

### App 은 어떤 transport (4계층) 소켓을 선택해야 할까?

- 데이터 보존의 관점

  - 파일전송, 웹 트랜젝션 같은 경우의 앱들은 100% 신뢰할 수 있는 데이터 전송이 필요하다.

    -> TCP

  - 오디오 같이 어떤 앱들은 약간의 loss 는 괜찮다.

    -> UDP

- 타이밍 관점

  - 인터넷 전화, 게임 같은 경우, low delay 가 필요하다

    -> UDP

- Throughput

  - 멀티미디어 같은 경우 최소한의 throughput 만 지속적으로 주는 것이 효과적이다.
  - 어떤 앱들은 탄력적으로 원하는 만큼의 throughput 을 조절해서 주는것이 좋다.

- 보안

  - encryption, data integrity, ...

## 2. Web 과 HTTP

- 웹페이지는 오브젝트들을 가지고 있다.

- 오브젝트란,

  HTML 파일, JPEG 이미지, Java applet, audio file, ... 등을 말한다.

- 웹페이지는 referenced object 를 가지고 있는 base HTML-file 을 포함한다.

- 각각의 오브젝트는 URL로 표현될 수 있다.

  www.someschool.edu/someDept/pic.gif

### HTTP

1. 클라이언트가 TCP connection 을 서버에게 개시한다(소켓을 만든다), port 80
2. 서버가 클라이언트로부터의 TCP connection을 수락한다.
3. HTTP 메시지들이 브라우저와 웹 서버사이에서 교환된다.
4. TCP 연결이 닫힌다.

HTTP 는 stateless 이다. (서버는 이전 클라이언트의 리퀘스트들을 보존하지 않는다)

### non-persistent HTTP

- 한번 오브젝트를 TCP connection 으로 보내고 다시 닫아버린다.
- 여러 개의 오브젝트들 다운로드 받아야 할 때는 여러번의 connection 이 필요하다.

1. 3-way handshake 를 통해 연결한다.

2. 3-way handshake 의 마지막 단계에서부터 클라이언트의 app layer data 를 보낼 수 있다.

3. app layer 의 데이터를 받은 HTTP 서버가 답을 보내고 다시 TCP conneciton 을 닫는다.

   (처음에는 index.html 파일을 보내주게 된다)

4. 클라이언트는 받을 것을 분석해, 다시 받아야 할 것이 있으면 다시 1번부터 진행한다.

한번의 non persistent HTTP 연결을 위해서는 2번의 왕복이 필요하다.

따라서,

`HTTP response time = 2RTT + file transmission time`

### persistent HTTP

- 한번의 TCP 연결에서 여러개의 오브젝트들을 보낼 수 있다.

### HTTP request message

- 두 종류의 HTTP 메시지가 있다.
  1. request
  2. response
- HTTP request message 는 ASCII 로 되어 있어 사람이 읽을 수 있다.
  - 처음에 GET, POST, HEAD 등의 커멘드를 통해 request line 을 작성한다.
  - 각 라인 끝에 `\r\n` 을 붙여줘야 한다.

1. TCP 소켓 생성
2. 도메인 네임을 IP 주소로 변환
3. 만든 소켓으로 상대방 소켓으로 연결
4. request 메시지를 만들어서 보냄
5. response 메시지를 받아서 해석

- URL 을 이용할 때는 GET 을 사용함

### HTTP response message

`HTTP/1.1 200 OK\r\n`

이런식으로 온다.

- 200 OK
- 301 Moved Permanently
- 400 Bad Request
- 404 Not Found
- 505 HTTP Version Not Supported

