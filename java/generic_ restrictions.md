# 제네릭 제약
> 제네릭 제약 (wildcard, extends, super) 에 대해 알아봅시다

<br>

## 제네릭 제약의 필요성
제네릭 타입을 외부 클래스에서 호출 시 파라미터를 각각 `Integer`, `String`으로 보내게 되면
T는 보낸 타입에 따라 `Integer`, `String`이 된다

<br>

만약, `Integer` 타입으로 만든 클래스에 문자열 파라미터가 들어가면 어떻게 될까?
컴파일 과정에서 오류가 발생하지 않고, 서비스 호출 시점에 오류가 발생하게 된다

<br>

이러한 오류를 피하고자 제네릭에 **제한**을 걸어줄 수 있다.

<br>

## 제네릭 제약 방법과 와일드카드
제네릭의 제약 방법에는 `extends`, `super`, `wildcard` 세 가지를 이용할 수 있다

<br>

- `<?>`(wildcard) : 모든 타입이 가능하다는 의미이다
- `extends T` : **상한 경계**로 T와 T의 자식 타입에 대해서만 지정이 가능하다
- `super T` : **하한 경계**로 T와 T의 부모 타입에 대해서만 지정이 가능하다

<br>

## 제네릭의 사용 - extends
extends는 `<K extends T>, `<? extends T>` 2가지 형태로 사용된다

### `<K extends T>`
- 타입 파라미터에 지정되는 구체적인 타입을 제한할 때 사용한다
- 클래스 선언시, 메소드의 타입 파라미터에 사용이 가능하다
- T와 T의 자식 타입에 대해서만 가능하도록 제한한다

<br>

```java
//클래스 타입 형태로 사용
public class makeFood<T extends food> {
  ...
}

//메소드의 타입 파라미터 형태로 사용
public <T extends Number> int add(T t) {
  ...
}
```

<br>

### `<? extends T>`
- 참조형 매개변수의 자료형을 T와 T의 자식 타입에 대해서만 가능하도록 제한한다
- 메소드의 매개변수, 리턴 타입에 사용 가능하다

<br>

```java
//메소드의 매개변수에 사용
public void printData(List<? extends food> list) {
  ...
}

//메소드의 리턴 타입에 사용
public List<? extends food> printData(){
  ...
}
```

<br>

## 제네릭의 사옹 - super
super는 `<? super T>` 형태로만 사용이 가능하다

<br>

### `<? super T>`
- 참조 매개변수의 자료형을 T와 T의 부모 타입에 대해서만 가능하도록 제한한다
- `<? extends T>`와 동일하게 매개변수, 리턴타입에 사용 가능하다

<br>

## Reference
- https://yarisong.tistory.com/48
