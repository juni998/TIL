# JSP로 시작하는 웹 프로그래밍
> 다룰 내용


<br>

### JSP 페이지의 구성 요소
- 디렉티브(Directive)
- 스크립트 : 스크립트릿(Scriptlet), 표현식(Expression), 선언부(Declaration)
- 표현 언어(Expression Language)
- 기본 객체(Implicit Object)
- 정적인 데이터
- 표준 액션 태그(Action Tag)
- 커스텀 태그(Custom Tag)와 표준 태그 라이브러리(JSTL)

<br>

### 디렉티브
- JSP 페이지에 대한 설정 정보를 지정할 때 사용된다

<br>

#### 선언 방법
```html
<%@ 디렉티브이름 속성1="값1" 속성2="값2" ... %>
```

<br>

#### 예시
```html
<%@ page contextType="text/html; charset=utf-8" %>
```
<br>

- `page` : 디렉티브 이름
- `contentType` : 속성
- `text/html; charset=utf-8` : contextType 속성의 값

<br>

#### JSP 제공 디렉티브
- page : JSP 페이지에 대한 정보를 지정
- taglib : JSP 페이지에서 사용할 태그 라이브러리 지정
- include : JSP 페이지의 특정 영역에 다른 문서를 포함시킴

<br>

### 스크립트 요소
- 표헌식 : 값을 출력한다
- 스크립트릿 : 자바 코드를 실행한다
- 선언부 : 자바 메소드를 만든다

<br>

## page 디렉티브의 주요 속성
- contentType
- import
- session
- buffer
- autoFlush
- info
- errorPage
- isErrorPage
- pageEncoding
- isELIgonred
- deferredSyntaxAllowedAsLiteral
- trimDirectiveWhitespaces

<br>

## contentType
- `charset` 디폴트 값 : ISO-8859-1
  - 영어와 서유럽어의 문자만 포함, 한글 X

<br>

## pageEncoding
### 웹 페이지가 사용할 캐릭터 셋을 결정하는 기본 과정
1. 파일이 BOM으로 시작하지 않을 경우
- 기본 인코딩을 이용해서 파일을 처음부터 읽고, page 디렉티브의 pageEncoding 속성을 검색한다
  - pageEncoding 속성이 값을 갖고 있다면, 파일을 읽어올 때 속성값을 캐릭터 셋으로 사용
  - pageEncoding 속성이 없다면, contentType 속성을 검색, contentType 속성이 존재하고, charset을 이용해서 캐릭터 셋을 지정했다면, 파일을 읽어올 때 사용할 캐릭터 셋으로 charset에 지정한 값 사용
  - 모두 해당되지 않을 경우 ISO-8859-1 캐릭터 셋 사용

<br>

2. 파일이 BOM으로 시작할 경우
- BOM을 이용해서 결정된 인코딩을 이용하여 파일을 읽고, page 디렉티브의 pageEncoding 속성을 검색
- 만약 pageEncoding 속성의 값과 BOM을 이용해서 결정된 인코딩이 다르면 에러 발생

<br>

### BOM(Byte Order Mark)
BOM이란 UTF-8, UTF-16 같은 유니코드 인코딩에서 바이트의 순서가 리틀 엔디언(Little Endian)인지 빅 엔디언(Big Endian)인지의 여부를 알려주는 16Bit의 값

<br>

## request 기본 객체
### request 객체가 제공하는 기능
- 클라이언트와 관련된 정보 읽기
- 서버와 관련된 정보 읽기
- 클라이언트가 전송한 요청 파라미터 읽기
- 클라이언트가 전송한 요청 헤더 읽기
- 클라이언트가 전송한 쿠키 읽기
- 속성 처리 기능

<br>

### 클라이언트와 관련된 정보 및 서버 정보 읽기
- getRemoteAddr()
- getContentLength()
- getCharacterEncoding()
- getContentType()
- getProtocol()
- getMethod()
- getRequestURI()
- getContentPath()
- getServerName()
- getServerPort()

#### 예제
```jsp
...

클라이언트 IP = <%= request.getRemoteAddr() %> <br>
요쳥정보 길이 = <%= request.getContentLength() %> <br>
요청정보 인코딩 = <%= request.getCharacterEncoding() %> <br>
요청정보 컨텐츠 타입 = <%= request.getContentType() %> <br>
요청정보 프로토콜 = <%= request.getProtocol() %> <br>
요청정보 전송방식 = <%= request.getMethod() %> <br>
요청 URI = <%= request.getURI() %> <br>
컨텍스트 경로 = <%= request.getContextPath() %> <br>
서버이름 = <%= request.getServerName() %> <br>
서버포트 = <%= request.getServerPort() %> <br>

...
```

<br>

실행 결과
```
클라이언트 IP = 0:0:0:0:0:0:0:1
요청정보 길이 = -1
요청정보 인코딩 = null
요청정보 컨텐츠타입 = null
요청정보 프로토콜 = HTTP/1.1
요청정보 전송방식 = GET
요청정보 URI = /ch03/request.jsp
컨텍스트 경로 = /ch03
서버이름 = localhost
서버포트 = 8080
```

<br>

### request 기본 객체의 요청 파라미터 관련 메소드

| 메소드 | 리턴 타입 | 설명 |
| ---- | ------- | --- |
| getParameter(String name) | String | 이름이 name인 파라미터의 값을 구하고 존재하지 않을 경우 null 리턴 |
| getParameterValues(String name) | String[] | 이름이 name인 파라미터의 값을 배열로 구하고 존재하지 않을 경우 null 리턴 |
| getParameterNames() | java.util.Enumeration | 웹 브라우저가 전송한 파라미터의 이름 목록을 구함 |
| getParameterMap() | java.util.Map | 웹 브라우저가 전송한 파라미터의 맵을 구한다 |

<br>






