# 예외 처리

<br>

## 초난감 예외 처리

### 예외 블랙홀

```java
try {
  ...
} catch (Exception e) {
  //예외를 잡고 아무것도 하지 않는 것  
}
```

- `catch` 블록에 아무것도 적지 않는 것

- 예외 발생 시 무엇이 문제인지도 모른 채 코드는 정상적으로 실행되지 않는 상태가 될 수 있다

<br>

> Exception 로그를 남길 때 `System.out`을 사용하지 않고 `logger`를 사용하는 이유?
> 
> `logger`와 달리 `System.out`은 `outputStream`을 사용하는데 **바이트 단위**로 읽기 때문에
> 성능적으로 `logger`보다 좋지 않다

<br>

### 그나마 덜 문제인 코드

```java
try {
  ...
} catch (Exception e) {
  e.printStackTrace();
}
```

- 예외가 무엇인지 적어도 프린트라도 하는 코드

<br>

## 올바른 예외처리 방법

> 크게 복구하는 방법과 단순히 알리기만 하는 방법으로 나누어진다

- 복구하는 방법
  - 예외 상황을 복구한다
  - ex) 몇 초, 몇 분뒤 다시 시도

- 알리기만 하는 방법
  - 운영자 혹은 개발자에게 어떤 에러가 발생했다고 명확히 알린다
  - ex) 메일, 문자, 슬랙 알림 등으로 알림

<br>

## 생각해봐야 할 코드

```java
public void method1() throws Exception {
  method2();
  ...
}

public void method2() throws Exception {
  method3();
  ...
}

public void method3() throws Exception ...
```

- 아무 생각없이 메소드 시그니처에 `Exception`만 붙인다

- 예외 블랙홀보단 낫지만 이 메소드를 호출하는 상위 스택이 전부 더러워진다
  - 하위 메소드에서 `Exception`을 던지면 하위 메소드를 호출하는 상위 메소드도 강제로 `Exception`을 던져야 한다

<br>

## 체크 예외와 언체크 예외

### 체크 예외의 특징

- `try ... catch` 문 작성을 강요한다

- `RuntimeException`을 상속하지 않는다

> 안정성을 위해 예외 처리를 강제 했더니 예외 블랙홀 같은 코드가 나오는 부작용이 생겼다

<br>

### 언체크 예외 특징

- `try ... catch` 문 작성을 강요하지 않는다

- `RuntimeException`을 상속한다

> 요즘 나오는 라이브러리는 오히려 언체크 예외가 더 많이 쓰인다고 한다

<br>

## 예외처리 방법 

### 예외 복구 

- 어떻게든 정상 상태로 돌려놓는 것

```java
int maxRetry = MAX_RETRY;

while(maxRetry --> 0) {
  try {
    ... // 예외가 발생할 수 있는 시도
    return; // 작업 성공
  }
  catch(SomeException e) {
    // 로그 출력, 정해진 시간만큼 대기
  }
  finally {
    // 리소스 반납, 정리 작업
  }
}

throw new RetryFailedException(); // 최대 재시도 횟수를 넘기면 직접 예외 발생
```

<br>

### 예외처리 회피

- 메소드 시그니처 뒤에 붙는 `throws Exception`을 통해 예외처리를 이 메소드를 호출한 곳으로 넘기는 것

- 이 메소드를 호출한 곳에서 예외를 처리하는 것이 더 낫다는 분명한 근거가 있을 때 사용해야 한다

> 빈 `try ... catch` 코드를 작성하는 것과는 다르다!

<br>

### 예외 전환

- 범용적인 예외를 더 구체적인 예외로 전환하여 예외를 던지는 방법

```java
public void add(User user) throws DuplicateUserIdException, SQLException {
  try {
    // JDBC를 이용해 user 정보를 DB에 추가하는 코드 또는
    // 그런 기능을 가진 다른 SQLException을 던지는 메소드를 호출하는 코드
  }
  catch(SQLException e) {
    // ErrorCode가 MySQL의 "Duplicate Entry(1062)"이면 예외 전환
    if (e.getERrorCode() == MysqlErrorNumbers.ER_DUP_ENTRY)
      throw DuplicateUserException();
    else
      throw e; // 그 외의 경우는 SQLException 그대로
  }
}

```

<br>

- 예외를 처리하기 쉽게 단순히 만드는 포장 방법

```java
try {
  ...
} catch (NamingException ne) {
  throw new EJBException(ne);
} catch (SQLException se) {
  throw new EJBException(se);
} catch (RemoteException re) {
  throw new EJBException(re);
}

```

- `EJBException`은 `RuntimeException`을 상속한 클래스로 예외 처리를 더 쉽게 할 수 있도록 포장하는 방식의 예

- `RuntimeException`을 상속하면 언체크 예외가 된다

<br>

## 예외처리 전략

### 런타임 예외의 보편화

- 어차피 복구 못할 예외라면, 그냥 런타임 예외로 변경하여 불필요한 `throws`를 작성하지 않아도 되도록 변경하자

<br>

### 애플리케이션 예외로 처리하기

- 복구 가능한 예외 혹은 꼭 한번 생각해봐야 할 예외를 체크 예외로 처리하여 무엇인가 조치를 취하게 하자

<br>

### 어떤 전략이 좋은가?

- `JdbcTemplate`과 스프링 프레임워크의 대부분의 구현은 런타임 예외의 보편화 전략을 따르고 있다

- 그러나 반드시 예외를 처리하는 것을 고려해야 하는 중요한 비즈니스의 경우에는 애플리케이션 예외 방식을 사용하자

<br>

### `DataAccessException`을 통한 예외 전환

- `SQLException`은 재시도 한다고 해서 복구되기는 어려운 예외이며 체크 예외이다.

- `JdbcTemplate`는 `DataAccessException`을 통해 `SQLException`을 런타임 예외로 전환해 불 필요한 `catch/throws`를 줄여준다

<br>

### JDBC 예외처리의 어려움

- 대표적으로 DB를 제공하는 벤더 문제가 있다 MySQL, Oracle, MSSQL 등 다양하며, 비슷하지만 각각의 SQL 문법이 있다

#### DataAccessException 을 통한 해결책

- `DataAccessException` 는 벤더별 에러코드를 파악하여, 통합 예외를 제공한다

<br>

#### 통합 예외를 제공하는 이유

- 높은 추상 수준의 코드를 이용하여 DB 벤더가 바뀌더라도 자바 코드 자체는 바꾸지 않아도 되도록 만들어준다

<br>

### 사용자 정의 예외로 예외 구체화

```java
public class DuplicateUserIdException extends RuntimeException{
    public DuplicateUserIdException(Throwable cause) {
        super(cause);
    }
}

public void add(User user) throws DuplicateUserIdException {
    try {
        this.jdbcTemplate.update("insert into users(id, name, password, level, login_count, recommend_count) values (?, ?, ?, ?, ?, ?)"
                , user.getId()
                , user.getName()
                , user.getPassword()
                , user.getLevel().intValue()
                , user.getLoginCount()
                , user.getRecommendCount()
        );
    } catch (DuplicateKeyException e) {
        throw new DuplicateUserIdException(e);
    }
}

```

- 예외를 한번 더 감싸서 더 구체적으로 만들 수 있다

<br>













