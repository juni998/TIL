# 상속보다 조합(Composition)을 사용하자
> 상속보다 Composition을 사용해야 한다고 하는데, 자세히 알아보도록 하자
<br>

**상속과 컴포지션**
- 상속 : 하위 클래스가 상위 클래스의 특성을 재정의 한것 (IS-A)관계
- 컴포지션 : 기존 클래스가 새로운 클래스의 구성요소가 되는것 (HAS-A)관계
<br>

## 상속
### 상속은 왜 사용 할까?
1. 코드를 재사용함으로써 중복을 줄일 수 있다
2. 변화에 대한 유연성 및 확장성이 증가한다
3. 개발 시간이 단축된다
<br>

### 상속의 단점
> 상속을 잘못 사용하면 객체의 유연성을 해치고, 오류를 내기 쉬운 소프트웨어가 된다
1. 상위 클래스의 구현이 하위 클래스에게 노출되는 상속은 캡슐화를 위반한다
2. 캡슐화를 위반하면, 하위 클래스가 상위 클래스에 강하게 결합, 의존하게 된다
3. 강한 결합, 의존은 변화에 유연하게 대처하기 어려워진다
<br>
여기서 말하는 상속이란, 클래스가 다른 클래스를 확장하는 구현상속을 말한다
<br>

### 코드예시
```java
public class CustomHashSet<E> extends HashSet {
    private int addCount = 0;

    public CustomHashSet () {}

    public CustomHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }

    @Override 
    public boolean add(Object o) {
        addCount++;
        return super.add(o);
    }

    @Override
    public boolean addAll(Collection collection) {
        addCount += collection.size();

        return super.addAll(collection);
    }


    public int getAddCount() {
        return addCount;
    }
}

public class Main {
    public static void main(String[] args) {
        CustomHashSet<String> customHashSet = new CustomHashSet<>();
        List<String> strings = Arrays.asList("t1", "t2", "t3");
        customHashSet.addAll(strings);

        System.out.println("customHashSet.getAddCount() = " + customHashSet.getAddCount());
    }
}

```

HashSet에서 요소를 몇번 삽입했는지 알기 위해서 작성한 코드이다
<br>
<br>
main 메소드를 보면 우리는 3개의 요소가 담긴 List를 추가하였고,제대로 작성된 클래스라면 
<br>
getAddCount()의 갯수는 3개가 나와야 하지만, **출력결과를 보면 6이 나온것을 알 수 있다.**

<br>

HashSet의 addAll()메소드는 아래 코드로 실행된다
```java
public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }

```

AbstractCollection 클래스에서 addAll 메소드는 Collection 객체의 수만큼 add 메소드를 수행한다고 작성되어있는데,
우리가 작성한 CutomHashSet 클래스에서도 add 메소드를 overriding 했으므로 총 6번의 add가 수행되면서
**addCount의 값이 6이되는것을 확인할 수 있다.**

우리가 원하는 결과를 얻으러면 overriding한 add 메소드를 없애던가, addAll 메소드를 없애면 되지만 이는 추후에 HashSet 클래스가 변경되면 치명적인 오류를 발생할 수 있다.

 

즉, 상속의 개념은 우리가 편하게 사용하기 위해서 등장했지만, 객체지향 프로그래밍의 캡슐화에 위배될 가능성이 있는것이며, 코드의 유연성도 저하된다.
<br><br>

## 상속이 캡슐화를 위반 한다는게 뭘까?
> 자식 클래스가 부모 클래스의 메소드를 Overriding 하는 경우 캡슐화
<br>

### 캡슐화란?
> 외부에서 내부의 정보를 들여다 보지 못하게 하는 것

캡슐화란 객체의 속성과 행위를 하나로 묶고, 실제 구현 내용 일부를 외부에 감추어 은닉화 한다
<br>

### 그렇다면 은닉화란?
은닉화는 캡슐화로 인해서 얻어진 효과이다
객체를 사용하는 외부에서는, 객체가 어떻게 구성 되어있는지 정확히 알 필요도 없고, 알 필요도 없다
<br>

### 상속이 캡슐화를 깨는 경우

위에서 봤던 코드를 다시 한번 보자
```java
...
    @Override 
    public boolean add(Object o) {
        addCount++;
        return super.add(o);
    }

    @Override
    public boolean addAll(Collection collection) {
        addCount += collection.size();

        return super.addAll(collection);
    }
...
```

- CumstomHashSet의 add()와 addAll() 두 메소드는 AbstractCollection 클래스를 상속받아 Overriding하여 새로운 기능을 제공하고 있다
- 이런경우 부모는 캡슐화가 유지되고 있지 못하게 된다
  - 왜냐하면 외부에서 add()와 addAll()를 가져다 쓰기만 해야 하는데, 재 정의하고, 이를 바꾸어 쓰기 때문에 **은닉화의 개념에서 캡슐화가 깨졌다고 볼 수 있다**
<br><br>

## 그러므로 Composition을 사용하자
### Composition이란?
- 기존 클래스를 확장하는 대신 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 만든다
- 새 클래스의 인스턴스 메소드들은 인스턴스의 메소드들을 호출하여 결과를 반환한다
<br>

ForwardingSet을 구현한 InstrumentedSet 클래스

```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override // 필요한 메소드들은 재정의했다.
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    // 새로운 메소드를 추가하기도 한다.
    public int getAddCount() {
        return addCount;
    }
}
```
<br><br>

Set을 implements하는 ForwardingSet
```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s; // 기존 클래스인 Set를 private 필드로 생성

    public ForwardingSet(Set<E> s) {
        this.s = s;
    }

    // Set의 메소드를 호출하는 메소드들 재정의
    @Override
    public void clear() { s.clear(); }

    @Override
    public boolean contains(Object o) { return s.contains(o); }

    // ...
}
```

### Composition의 장점은?
- 한번 구현해두면 어떤 구현체라도 계층 가능하다
- 기존 생성자와 함께 사용 가능하다
<br>

## 요약
> 상속을 꼭 사용가능한 상황 외에는 Composition 사용 권장!
<br>

### 상속을 사용할 때
- 하위 클래스와 상위 클래스의 관계가 is-a 관계인가?
- 상속으로 불필요하게 내부 구현을 노출하지 않는가?
- 확장하려는 클래스의 API에 아무런 결함이 없는가?
- 결함이 있다면 하위 클래스까지 전파되어도 상관 없는가?
- 기존 행동을 부분적으로 보완하는 의미의 확장으로 사용하는가?
<br>

## Reference

- https://unluckyjung.github.io/oop/2021/03/17/Inheritance-and-Encapsulation/
- https://dev-cool.tistory.com/22
- https://yeonyeon.tistory.com/206

