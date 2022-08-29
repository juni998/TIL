# 변성
> 제네릭을 잘 활용하기 위해서 변성에 대해 알아봅시다
<br>

## 변성
변성은 타입의 계층 관계에서 **서로 다른 타입 간에 어떤 관계가 있는지 나타내는 개념**이다

<br>

### 변성의 종류
> 변성의 종류에는 네 가지가 있다
- 공변(covariant)
- 반공변(contravariant)
- 이변량(bivariant)
- 무공변(불공변, invariant)
<br>

### 공변
공변은 **타입 `S`가 `T`의 하위 타입일 때 `Box[S]`와 `Box[T]`의 하위 타입인가?** 이것이 성립되면 해당 언어는 공변적이라고 한다

자바는 무공변이지만, **한정적 와일드 카드 타입** 기능으로 공변, 반공변을 지원하는 것처럼 컴파일러 트릭을 사용한다
`<? extends T>`, `<? super T>` 키워드가 바로 그것이다
이러한 제네릭의 변성을 적절히 사용하면 유용한 코드를 작성할 수 있게 된다

```java
public void pushAll(Collection<T> src) {
	for (T elem : src) {
		push(elem);
	}
}
```

위의 메소드를 아래와 같이 사용하면 컴파일 에러가 발생한다

```java
Stack<Number> stack = new Stack<Number>();
Collection<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
stack.pushAll(integers); // 에러 발생
```

이유는 제네릭은 공변을 지원하지 않기 때문이다
즉 `Collection<Integer>`는 `Collection<Number>`의 하위 타입이 아니다

<br>

하지만 `<? extend T>`를 사용하여 아래와 같이 바꿔주면, **공변**이 적용되어 컴파일 에러가 사라진다

```java
public void pushAll(Collection<? extends T> source)
```

자바는 **한정적 와일드 카드 타입**을 사용하여 공변이 사용 되게끔 할 수 있다.
`Collection<Integer>` 는 `Collection<Number>`의 하위 타입인가? 를 참이 되게 만드는 것이다

<br>

### 반공변
반공변은은 **타입 `S`가 `T`의 하위 타입일 때, `Box[S]`가 `Box[T]`의 상위 타입** 이 성립되면 **반공변**이다

<br>

반공변은 공변과 반대로 생각하면 된다 
- `Box[S]`가 `Box[T]`의 하위 타입 = 공변
- `Box[S]`가 `Box[T]`의 상위 타입 = 반공변

<br>

아래의 popAll 메소드는 현재 스택에 있는 요소들을 입력 파라미터로 주어주는 컬렉션에 옮겨 담는다

```java
public void popAll(Collection<T> dst) {
	while(!isEmpty()) {
		dst.add(pop());
	}
}
```

위의 메소드를 이용하여 `Stack<Number>`의 원소를 `Collection<Object>`에 담아야 한다고 했을때,
아래의 코드 또한 컴파일 에러를 발생시킨다

```java
Stack<Number> stack = new Stack<Number>();
Collection<Object> objects = ...;
stack.popAll(objects); // 에러 발생
```

이유는 `Collection<Object>`는 `Collection<Number>`의 하위타입이 아니기 때문(`Object`는 `Number`의 상위 타입)이다

반공변을 적용하기 위해서, 즉 `Collection<Number>` 타입이 기대되는 자리에 `Collection<Object`를 할당하려면
아래와 같이 `super` 키워드를 사용한 **한정적 와일드카드 타입**으로 시그니처를 바꿔야 한다

```java
public void popAll(Collection<? super T> dst)
```

<br>

## 사용지점변성과 선언지점변성
앞에서 살펴본대로 자바에는 한정적 와일드카드 타입을 통해 타입의 공변성 또는 반공변성을 지정할 수 있다
이렇게 타입 파라미터가 나타나는 지점에 변성을 정하는 자바의 방식을 사용지점변성(use-site variance) 이라고 한다

<br>

자바와 같이 JVM을 이용하는 언어인 코틀린에서도 사용자 지점 변성을 제공한다

<br>

`<? extend T>` 대신 `<out T>`
<br>

`<? super T>` 대신 `<in T>`

키워드를 사용하여 사용자 지점 변성을 제공한다

<br>

그리고 코틀린은 추가적으로 **선언지점변성**을 제공한다 선언 지점 변성은 클래스를 선언할 때 공변성을 지정한다
즉, 클래스 전역에 공변성을 지정할 수 있다
```kotlin
class Box<out T> (val data:T)
```

이러한 선언지점변성은 타입 파라미터마다 변성을 계속 지정해야 하는 사용지점변성의 단점을 개선한다

<br>

### 요약
1. 자바는 기본적으로 무공변이지만, 제네릭의 한정적 와일드카드 타입을 사용하여 공변, 반공변을 지원할 수 있다
2. `<? extends T>` 키워드를 사용하여 공변이 되게끔 할 수 있다
3. `<? super T>` 키워드를 사용하여 반공변이 되게끔 할 수 있다
4. 자바는 사용지점변성만 제공, 코틀린은 사용지점변성 그리고, 선언지점변성 제공
5. 선언지점변성은 타입 파라미터마다 변성을 계속 지정해야 하는 사용지점변성의 단점을 개선한다

<br>

### Reference
- https://sungjk.github.io/2021/02/20/variance.html
- https://asuraiv.tistory.com/16

