# Map
> Map과 HashMap, TreeMap, LinkedHashMap에 대하여

<br>

## Map
- Map은 키(Key)와 값(Value)으로 이루어져 있다
- 키 없이 값만 저장될 수 없다
- 값 없이 키만 저장될 수 없다
- 키는 해당 Map에서 고유해야만 한다
- 값은 중복되어도 상관 없다

<br>

### Map의 인터페이스를 구현한 클래스
- HashMap
- TreeMap
- LinkedHashMap

<br>

## HashMap
- 검색과 삽입에 **O(1)** 시간이 소요된다
- 키의 순서는 **무작위**로 섞여 있다
- 구현은 **연결리스트**로 이루어진 배열로 구현되어 있다
- **null** key와 null value 모두를 **허용**한다

<br>

### HashMap 구현 예제
```java
public class HashMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();

        map.put("c", "1");
        map.put("a", "1");
        map.put("f", "1");
        map.put("e", "1");

        for (String s : map.keySet()) {
            System.out.println(s);
        }
    }
}
```

<br>

결과
```
a
c
e
f
```

내부 hash 값에 따라서 키 순서가 정해지기 때문에 특정 규칙 없이 출력된다

<br>

## TreeMap
- 검색과 삽입에 **O(log N)** 시간이 소요된다
- 키의 순서는 **정렬**되어 있다.
- 구현은 **Red-Black Tree**로 구현되어 있다.

<br>

### TreeMap 구현 예제
```java
public class TreeMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new TreeMap<>();

        map.put("c", "1");
        map.put("a", "1");
        map.put("f", "1");
        map.put("e", "1");

        for (String s : map.keySet()) {
            System.out.println(s);
        }
    }
}
```

<br>

결과
```
a
c
e
f
```

키 값이 알파벳 순서대로 정렬된다.

<br>

## LinkedHashMap
- 검색과 삽입에 **O(1)** 시간이 소요된다
- 키의 순서는 **삽입한 순서대로 정렬**되어 있다
- 구현은 **양방향 연결 버킷(double-linked bucket)** 으로 구현되어 있다

<br>

### LinkedHashMap 구현 예제
```java
public class LinkedMapTest {
    public static void main(String[] args) {
        Map<String, String> map = new LinkedHashMap<>();

        map.put("c", "1");
        map.put("a", "1");
        map.put("f", "1");
        map.put("e", "1");

        for (String s : map.keySet()) {
            System.out.println(s);
        }
    }
}
```

<br>

결과
```
c
a
f
e
```
키 값은 입력 순서대로 출력된다.

<br>

### accessOrder
먼저 LinkedHashM?ap 생성자를 살펴보면 다음과 같다
```java

public LinkedHashMap(int initialCapacity, float loadFactor) {
     super(initialCapacity, loadFactor);
     accessOrder = false;
 }
 
 public LinkedHashMap(int initialCapacity) {
     super(initialCapacity);
     accessOrder = false;
 }

 public LinkedHashMap() {
     super();
     accessOrder = false;
 }
  
public LinkedHashMap(Map<? extends K, ? extends V> m) {
    super();
    accessOrder = false;
    putMapEntries(m, false);
}

public LinkedHashMap(int initialCapacity,float loadFactor, boolean accessOrder) {
    super(initialCapacity, loadFactor);
    this.accessOrder = accessOrder;
}
```

<br>

HashMap과 별도로 LinkedHashMap에서는 accessOrder라는 값을 가지고 있고, 기본적으로 false로 설정되어 있다
true : 최근에 접근한 요소를 가장 마지막에 표기
false : 기본값, 입력한 순서대로 표기

<br>
HashMap에는 존재하지 않고, LinkedHashMap에는 다음과 같은 메소드가 구현되어 있다

```java

void afterNodeAccess(Node<K,V> p) { } // 데이터에 접근했을때
void afterNodeInsertion(boolean evict) { } // 데이터 추가 후 동작
void afterNodeRemoval(Node<K,V> p) { } // 데이터가 삭제되었을 때

```

<br>

위의 메소드 중에서 `afterNodeAccess()` 메소드를 자세히 보면 다음과 같이 구현되어 있는데,

```java
    void afterNodeAccess(Node<K,V> e) { // move node to last
        LinkedHashMap.Entry<K,V> last;
        if (accessOrder && (last = tail) != e) {
            LinkedHashMap.Entry<K,V> p =
                (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
            p.after = null;
            if (b == null)
                head = a;
            else
                b.after = a;
            if (a != null)
                a.before = b;
            else
                last = b;
            if (last == null)
                head = p;
            else {
                p.before = last;
                last.after = p;
            }
            tail = p;
            ++modCount;
        }
    }
```

`afterNodeAccess()`메소드는 accessOrder가 true로 접근했을 때 key의 순서를 변경해주는 작업을 진행한다

<br>

## 요약
- 특별한 이유가 없다면 검색 성능이 좋은 HashMap 사용
- 키 값을 정렬하고 싶다면 TreeMap 사용
- 입력 순서가 의미가 있다면 LinkedHashMap 사용
