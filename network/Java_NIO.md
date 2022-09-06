# Java NIO

<br>

Java NIO는 세 가지 컴포넌트로 구성되어 있다
- Channel
- Buffers
- Selectors

<br>

## Channel
모든 IO는 `Channel`에서 시작한다
<br>

`Channel`은 `Stream`과 유사하다
<br>

파일에서 데이터를 읽을 수 있는 `FileChannel`이 있고, 네트워크를 통해 데이터를 주고 받는 `SocketChannel`도 있다

<br>

### NIO를 이용한 대표적인 채널
- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

<br>

### Stream과 Channel의 차이
- 스트림은 One-way이지만 채널은 양방향이다
- 채널은 비동기적으로 읽거나 쓸 수 있다
- 채널은 항상 버퍼로 데이터를 읽어들이거나 버퍼의 데이터를 쓰게 된다

<br>

## Buffers
버퍼는 본질적으로 메모리 블럭이다
<br>

메모리의 한 공간에 할당되어 있는 공간을 NIO Buffer 객체로 래핑한 것으로 메모리로의 접근, 사용을 추상화 해 사용하기 편한 메소드를 제공해준다
<br>

사용자는 버퍼에서 데이터를 읽고, 버퍼로 데이터를 저장한 다음 채널에 쓰거나 읽는다
