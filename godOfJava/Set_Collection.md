# Set 컬렉션
> Set에 대한 이해와 HashSet, TreeSet, LinkedHashSet의 비교

<br>

## Set
- 순서를 유지하지 않는 데이터의 집합
- 데이터 중복을 허용하지 않는다

<br>

### Set의 클래스
- HashSet : 순서를 보장하지 않으며 가장 빠르다
- TreeSet : 트리 타입으로 값이 저장되며, 데이터 추가, 삭제에는 시간이 더 걸리지만 검색과 정렬이 더 뛰어나다
- LinkedHashSet : 연결된 목록 타입으로 구현된 해시 테이블에 데이터를 저장하며, 저장된 순서에 따라서 값이 정렬된다

<br>

#### 시간복잡도
- HashSet : O(1)
- TreeSet : O(log n)
- LinkedHashSet : O(1)

<br>

## HashSet
- HastSet은 Set인터페이스를 구현한 가장 대표적인 컬렉션이다
- 중복된 데이터를 저장할 수 없으며 순서를 유지하지 않는다

<br> 

### 선언
- `Set<T> 객체명 = new HashSet<T>();`
- `HashSet<T> 객체명 = new HashSet<T>();`

<br>

### HashSet의 사용법과 예제
```java
public class HashSetTest {

    public static void main(String[] args) {
        HashSet<String> set = new HashSet<>();

        set.add("a");
        set.add("f");
        set.add("i");
        set.add("f"); //중복
        set.add("g");
        set.add("a"); //중복

        //한번 생성 후 재사용 할 경우 재생성 필요
        Iterator<String> iter = set.iterator();

        while (iter.hasNext()) {
            System.out.println("iter.next() = " + iter.next());
        }

    }
}
```

<br>

결과
```
iter.next() = a
iter.next() = f
iter.next() = g
iter.next() = i
```

결과를 보면 중복을 제거하고, add() 순서와 상관없이 데이터가 삽입된 것을 알 수 있다

<br>

### Load Factor
HashSet() 생성자의 설명을 보면 데이터를 저장할 수 있는 16개의 공간과 0.75의 로드 팩터를 갖는 객체를 생성한다 라고 하는데,
여기서 로드팩터란 (데이터의 개수) / (저장 공간) 을 의미한다. 만약 데이터의 개수가 증가하여 로드 팩터보다 커지면, 저장 공간의 크기는 증가되고
해시 재정리 작업(rehash)을 해야 한다 데이터가 해시 재정리 작업에 들어가면 내부에 갖고 있는 자료구조를 다시 생성해야 하므로 성능에 영향이 발생한다

<br>

로드 팩터의 값이 클수록 공간은 넉넉하지만, 데이터를 찾는 시간은 증가하게 된다. 따라서 초기 공간 개수와 로드 팩터는 데이터의 크기를 고려하여 산정하는 것이 좋다

<br>

## TreeSet
- TreeSet은 이진 검색 트리 형태로 데이터를 저장하는 클래스이다.
- 중복된 데이터를 허용하지 않으며, 정렬된 위치에 저장한다
- 값에 따라 오름차순으로 정렬

<br>

### TreeSet 사용법과 예제
```java
public class TreeSetTest {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<Integer>();

        set.add(7);
        set.add(3);
        set.add(8);
        set.add(1);
        set.add(1);

        Iterator<Integer> iter = set.iterator();

        while (iter.hasNext()) {
            System.out.println("iter.next() = " + iter.next());
        }
    }
}
```

<br>

결과
```
iter.next() = 1
iter.next() = 3
iter.next() = 7
iter.next() = 8
```
결과를 보면 중복을 제거하고, add() 순서와 상관없이 오름차순으로 자동 정렬되는것을 볼 수 있다

<br>

## LinkedHashSet
- 저장 순서를 유지하고 관리한다

<br>

### LinkedHashSet의 사용법과 예제
```java
public class LinkedHashSetTest {
    public static void main(String[] args) {
        LinkedHashSet<String> set = new LinkedHashSet<>();

        set.add("c");
        set.add("z");
        set.add("a");
        set.add("g");
        set.add("z"); //중복
        set.add("g"); //중복

        Iterator<String> iter = set.iterator();

        while (iter.hasNext()) {
            System.out.println("iter.next() = " + iter.next());
        }
    }
}
```

<br>

결과
```
iter.next() = c
iter.next() = z
iter.next() = a
iter.next() = g
```
결과를 보면 중복을 제거하고, add()된 순서로 저장되는것을 볼 수 있다

