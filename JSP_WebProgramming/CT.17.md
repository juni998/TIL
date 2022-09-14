# 서블릿

<br>

## 서블릿이란
클라이언트의 요청을 처리하고, 그 결과를 반환하는 Servlet 클래스의 구현의 규칙을 지킨 자바 웹 프로그래밍 기술
<br>

즉, `자바를 사용하여 웹을 만들기 위해 필요한 기술` 이며, `클라이언트가 어떠한 요청을 하면 그에 대한 결과를 다시 전송`해주어야 하는데 이러한 역할을 하는 자바 프로그램입니다
<br>

예시 : 어떠한 사용자가 로그인을 하려고 할 때. 서버는 클라이언트의 아이디와 비밀번호를 확인하고, 다음 페이지를 띄워주어야 하는데, 이러한 역할을 수행하는 것이 서블릿이다

<br>

### 서블릿의 특징
- 클라이언트의 요청에 대해 동적으로 작동하는 웹 어플리케이션 컴포넌트
- html을 사용하여 요청에 응답한다
- Java Thread를 이용하여 동작한다
- MVC 패턴에서 Controller로 이용된다
- HTTP 프로토콜 서비스를 지원하는 `javax.servlet.http.HttpServlet` 클래스를 상속받는다
- UDP보다 처리 속도가 느리다
- HTML 변경시 Servlet을 다시 컴파일 해야하는 단점이 있다

<br>
일반적인 웹 페이지는 정적인 페이지만 제공한다. 여기서 동적인 페이지를 제공할 수 있도록 도와주는 어플리케이션이 서블릿이다

<br>


### 서블릿의 동작 방식
<img width="820" alt="993A7F335A04179D20" src="https://user-images.githubusercontent.com/103838236/189881107-24308ad2-4d31-42d7-a6e9-432b3cf6900b.png">

<br>

- 클라이언트가 URL을 입력하면 `HTTP Request`가 Servlet Container로 전송한다
- 요청을 전송받은 Servlet Container는 `HttpServletRequest`, `HttpServletResponse` 객체를 생성한다
- web.xml을 기반으로 사용자가 요청한 URL이 어느 서블릿에 대한 요청인지 찾는다
- 해당 서블릿에서 service메소드를 호출한 후 클라이언트의 GET, POST 여부에 따라 `doGet()` 또는 `doPost()`를 호출한다
- `doGet()`, `doPost()` 메소드는 동적 페이지를 생성한 후 HttpServletResponse 객체에 응답을 보낸다
- 응답이 끝나면 `HttpServletRequest`, `HttpServletResponse` 객체를 삭제한다

<br>

## Servlet Container
- 서블릿을 관리해주는 컨테이너
