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


