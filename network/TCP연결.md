# TCP연결 생성 과정과 해제 과정

<br>

## 3-Way Handshake와 4-way Handshake
`3-Way handshake` 는 TCP의 접속 `4-way handshake`는 TCP의 접속 해제 과정이다

<br> 

### PORT 상태 정보
- CLOSED : 포트가 닫힌 상태
- LISTEN : 포트가 열린 상태로 연결 요청 대기중
- SYN_RCV : SYNC 요청을 받고 상대방의 응답을 기다리는 중
- ESTABLISHED : 포트 연결 상태

<br>

### 플래그 정보
- SYN / 000010
  - 연결 설정. Sequence Number를 랜덤으로 설정하여 세션을 연결하는데 사용, 초기 Sequence Number를 전송한다
- ACK / 010000
  - 응답 확인. 패킷을 받았다는 것을 의미한다
- FIN / 000001
  - 연결 해제. 세션 연결을 종료시킬 때 사용하며, 더 이상 전송할 데이터가 없음을 의미한다

<br>

## TCP의 3-Way Handshake
- TCP통신을 이용하여 데이터를 전송하기 위해 네트워크 연결 설정을 하는 과정
- 양쪽 모두 데이터를 전송할 준비가 됐다는 것을 보장하고, 실제로 데이터 전달이 시작하기 전에 한 쪽이 다른 쪽이 준비 되었다는 것을 알 수 있도록 한다
- TCP/IP 프로토콜을 이용해서 통신을 하는 응용 프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정

<br>

### 작동 방식
- Client는 Server와 연결하기 위해 3-Way Handshake를 통해 연결 요청을 하게 된다
- 우리가 일반적으로 생각하는 Clent와 Server는 모두 서로 연결 요청을 먼저 할 수 있기 때문에, 연결 요청을 시도한 자가 Client, 연결 요청을 받은 수신자가 Server라고 보면 된다

<br>

1. 클라이언트는 서버와 커넥션을 연결하기 위해 SYN을 보낸다
2. 서버가 SYN을 받고, 클라이언트로 받았다는 신호인 ACK와 SYN패킷을 보낸다
3. 클라이언트는 서버의 응답은 ACK와 SYN 패킷을 받고, ACK를 서버로 보낸다

<br>

## TCP의 4-Way Handshake
- 연결을 해제하는 과정 여기서는 FIN 플래그를 이용한다
<br>

### Termination의 종류
TCP는 대부분의 connection-oriented 프로토콜과 같은 두 가지 연결 해제 방식이 있습니다
<br>

#### 1. Graceful connection release(정상적인 연결 해제)
- 정상적인 연결 해제에서는 양 쪽의 커넥션이 서로 모두 커넥션을 닫을 때까지 연결되어 있다
<br>

#### 2. Abrupt connection release(갑작스런 연결 해제)
- 갑자기 한 TCP 엔티티가 연결을 강제로 닫는 경우
- 한 사용자가 두 데이터 전송 방향을 모두 닫는 경우

