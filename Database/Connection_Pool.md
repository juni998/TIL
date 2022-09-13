# Conection Pool
> 커넥션 풀에 대해서 자세히 알아봅시다

<br>

## DB Connection
- DB를 사용하기 위해 DB와 애플리케이션 간 통신을 할 수 있는 수단
- DB Connection은 Database Driver와 Database 연결 정보를 담은 URL이 필요하다
- java의 DB Connection은 JDBC를 주로 이용하는데 URL 타입을 사용한다

<br>

## DB Connection 구조
- 클라이언트로서의 자바 프로그램이 직접 데이터베이스 서버로 접근하여 데이터를 액세스 하는 구조
- 자바 프로그램과 데이터베이스 서버 중간에 미들웨어 층을 두어, 그 미들웨어 층에게 비지니스 로직 구현부터 트랜잭션 처리, 리소스 관리 등을 전부 맡기는 구조

<br>

## Connection Pool의 장점
- DB 접속 설정 객체를 미리 만들어 연결하여 메모리상에 등록해 놓기 때문에 불필요한 작업(커넥션 생성, 삭제)이 사라지므로 클라이언트가 빠르게 DB에 접속 가능
- DB Connection 수를 제한할 수 있어서 과도한 접속으로 인한 서버 자원 고갈 방지가 가능하다
- DB 접속 모듈을 공통화 하여 DB 서버의 환경이 바뀔 경우 쉬운 유지보수가 가능하다
- 연결이 끝난 Connection을 재사용 함으로써 새로 객체를 만드는 비용을 줄일 수 있다

<br>

## Connection Pool의 유의사항
- 너무 많은 DB 접근이 발생할 경우 커넥션은 한정되어 있기 때문에 쓸 수 있는 커넥션이 반납될 때까지 기다려야한다
- 너무 많은 커넥션을 생성할 경우 커넥션 또한 객체이므로 많은 메모리를 차지하게 되고, 프로그램 성능을 떨어트리는 원인이 된다

<br>

즉, 커넥션 풀을 크게 설정하면 메모리 소모가 큰 대신 사용자의 대기 시간이 줄어들고, 커넥션 풀을 작게 설정하면 메모리 소모가 적은 대신 사용자의 대기 시간이 길어진다
따라서 사용량에 따라서 적정량의 커넥션 객체를 생성해 두어야 한다 

<br>

## Connection Pool이 커지면 무조건 성능이 좋아질까?
`Connection`의 주체는 `Thread`이므로 `Thread`와 함께 고려해야 한다

<br>

### Thread Pool의 크기가 Connection Pool의 크기보다 작을 경우
- `Thread Pool`에서 트랜잭션을 처리하는 `Thread`가 사용하는 `Connection`외에 남은 `Connection`은 실질적으로 메모리 공간만 차지하게 된다

<br>

### Thread Pool의 크기와 Connection Pool의 크기 모두 증가
- `Thread` 증가로 인해 더 많은 `Context Switching`이 발생한다
- Disk 경합 측면에서 성능 한계가 발생한다
  - 데이터베이스는 하드디스크 하나 당 하나의 I/O를 처리하므로 블로킹이 발생한다
  - 즉, 특정 시점부터는 성능적인 증가가 Disk병목으로 인해 미비해진다

<br>

Connection이 많다는 의미는 데이터베이스 서버에서 Thread를 많이 사용한다는 의미이다 이에 따라 Context Switching으로 인한 오버헤드가 더 많이 발생하기 때문에 Connection Pool을 아무리 늘리더라도 성능적인 한계가 존재한다

<br>

### 그렇다면 Connection Pool의 크기는 어느정도가 적당할까
Hikari CP 공식 문서에 의하면 `1 Connections = ((core_count) * 2) + effective_spindle_count` 로 정의하고 있다
<br>

- `core_count` : 현재 사용하는 서버 환경에서의 CPU 개수를 의미한다
  - `core_count * 2`를 하는 이유는 Context Switching 및 Disk I/O와 관련 있다
    - Context Switching으로 인한 오버헤드를 고려하더라도 데이터베이스에서 Disk I/O 보다 CPU 속도가 월등히 빠르다
    - 그러므로, Thread가 Disk와 같은 작업에서 블로킹되는 시간에 다른 Thread의 작업을 처리할 수 있는 여유가 생기고 여유 정도에 따라 멀티스레드로 작업을 수행할 수 있게 된다

- `effective_spindle_count` : 기본적으로 DB 서버가 관리할 수 있는 동시 I/O 요청 수이다
  - 하드디스크 하나는 하나의 `spindle`을 가진다
  - 디스크가 16개가 있는 경우 시스템은 동시에 16개의 I/O를 처리할 수 있다
