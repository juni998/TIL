# Hashtable과 stack
> Hashtable과 stack에 대해서 알아보고, 이 둘의 문제점에 대해 알아봅시다

<br>

먼저 `hashtable`, `stack` 는 Thread-Safe를 보장하는 클래스이다
멀티 쓰레드가 아닌 단일 스레드 환경에서 이러한 컬렉션을 사용하는 경우, 성능에 악영향을 미친다고 한다

<br>

그러므로 다음과 같은 비동기 클래스를 사용하는 것이 좋다

`Stack` -> `Deque`

`Hashtable` -> `HashMap`


<br>

## Thread-Safe 하다는 것은 무엇일까?
멀티 스레드 프로그래밍에서 일반적으로 어떤 함수나 변수, 혹은 객체가 여러 스레드로부터 동시에 접근이 이루어져도 프로그램 실행에 문제가 없음을 뜻한다

<br>

## HashMap을 써야하는 이유?
1. `hashtable`은 Thread-safe하고, `hashMap`은 Thread-safe하지 않다는 특징이 있다 그렇기에 멀티 스레드 환경이 아니라면 `HashMap`의 성능이 더 좋다
2. `hashtable`은 key에 null값을 허용하지 않지만, `hashMap`은 key에 null값을 허용한다
3. `hashtable`은 보조해시를 사용하지 않고, `hashMap`은 보조해시를 사용한다 그러므로 `hashMap`이 성능상 이점이 있다

<br>

## Deque를 써야하는 이유? 
1. `Stack`또한 Thread-safe하고, `Deque`는 Thread-safe하지 않다 그렇기에 멀티 스레드 환경이 아니라면 `Deque`의 성능이 더 좋다
2. `Stack`클래스는 `Vector`클래스를 확장 하였다 `Stack`은 LIFO 구조로 한쪽 방향에서만 삽입과 삭제가 일어나야 하는데, `Vector` 클래스
를 확장하면 중간에서 데이터를 삽입과 삭제를 할 수 있게 된다 LIFO 구조를 만들기 위하여 `Deque`를 사용해야 한다



