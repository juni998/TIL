# 세션 직접 만들어보기
> 세션에 대해 깊이 있는 이해를 위해
> 
> 로그인 구현시 사용할 세션을 직접 구현해보기

<br>

먼저 세션 관리는 크게 다음의 3가지 기능을 제공하면 된다

- 세션 생성
  - sessionId 생성 (임의의 추정 불가능한 값)
  - 세션 저장소에 sessionId와 보관할 값 저장
  - sessionId로 응답 쿠키를 생성해서 클라이언트에 전달

- 세션 조회
  - 클라이언트가 요청한 sessionId 쿠키의 값으로, 세션 저장소에 보관한 값 조회

- 세션 만료
  - 클라이언트가 요청한 sessionId 쿠키의 값으로, 세션 저장소에 보관한 sessionId와 값 제거

<br>

## 1. 회원 정보 Entity 클래스 생성

## 2. 세션 관리 클래스 생성

`SessionManager.class`

```java
public class SessionManager {
  public static final String SESSION_COOKIE_NAME = "mySessionId";
  
  //세션 저장소
  private Map<String, Object> sessionStore = new ConcurrentHashMap<>();
  
  ...
}
```

### 

<br>

### 세션 저장소를 `ConcurrentHashMap`으로 설정한 이유는?

<br>

## 3. 세션 생성 구현

```java
생략 ...

    public void createSession(Object value, HttpServletResponse response) {

        //세션 id를 생성하고, 값을 세션에 저장
        String sessionId = UUID.randomUUID().toString(); //추정 불가능한 랜덤 값 = UUID값
        sessionStore.put(sessionId, value);

        //쿠키 생성
        Cookie mySessionCookie = new Cookie(SESSION_COOKIE_NAME, sessionId);
        response.addCookie(mySessionCookie);
    }
```

추정 불가능한 랜덤의 값을 세션 id에 저장하고, 회원 정보를 value에 저장하게 된다

<br>

## 4. 쿠키 찾기와 세션 조회

세션 조회시 쿠키 값을 먼저 조회 후 세션 저장소에서 보관한 값을 조회 해야한다

```java
생략 ...
  //쿠키 값 찾기
  public Cookie findCookie(HttpServletRequest request, String cookieName) {
        Cookie[] cookies = request.getCookies();

        //쿠키가 없으면 null 리턴
        if (cookies == null) return null;

        return Arrays.stream(cookies)
                .filter(cookie -> cookie.getName().equals(cookieName))
                .findFirst()
                .orElse(null);

 }
```

매개변수로 들어온 `cookieName`의 값과 

