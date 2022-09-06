# In, OutputStream과 Reader, Writer의 차이점
<br>

> `InputStream`과 `Reader` / `OutputStream`과 `Writer`의 근본적인 차이는 데이터를 어떻게 다루는지?

<br>

## InputStream, OutputStream
- 데이터를 `byte` 단위로 입력하고 출력
- 바이너리 형태의 데이터를 다룰 때 사용

<br>

## Reader, Writer
- 데이터를 `char`문자 단위로 입력하고 출력
- 바이트 코드를 읽어서 문자로 변환하고 문자를 바이트 코드로 변환해서 출력하는 것을 처리
  - 기본 인코딩은 JVM의 default charset을 사용
  - 생성자 파라미터로 특정 인코딩 형식을 전달하여 생성할 수 있음
- 일반적으로 `.txt` 파일을 읽을 때는 `Reader`와 `Writer`의 구현 클래스 사용
