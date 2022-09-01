# Collectors
> Java Collectors에 대해 살펴보겠습니다.

<br>

## Stream.collect()
`Stream.collect()`는 데이터의 중간 처리 후 마지막에 원하는 형태로 변환해 주는 역할을 한다.
`collector`는 아래와 같은 기능을 제공합니다
<br>

- `stream` 요소들을 `list`, `set`, `map` 자료형으로 변환
- `stream` 요소들을 결합
- `stream` 요소들의 통계
- `stream` 요소들의 그룹화와 분할
<br>

`Collectors`는 아래와 같이 가독성을 높이기 위해 미리 `static import`를 사용하는 것이 일반적입니다
```java
import static java.util.stream.Collectors.*;
```
<br>

실습 예제는 아래의 List를 통해 진행합니다
```java
List<String> sampleList = Arrays.asList("a", "bb", "ccc", "dddd", "a");
```
<br>

## Collectors.toList()
모든 `Stream`의 요소를 `List`인스턴스로 수집하는데 사용합니다
<br>

아래는 Stream의 모든 요소를 `List` 인스턴스로 수집하는 예제입니다
```java
List<String> result = sampleList.stream().collect(toList());
```
<br>

## Collectors.toSet()
모든 `Stream`의 요소를 `Set`인스턴스로 수집하는데 사용합니다
특정 `Collection`을 구현하려면 `toCollection`을 사용하면 됩니다
<br>

아래는 Stream의 모든 요소를 `Set` 인스턴스로 수집하는 예제입니다
```java
List<String> lists = sampleList.stream().collect(toCollection(LinkedList::new))
Set<String> sets = sampleList.stream().collect(toCollection(TreeSet::new))
```

<br>

## Collectors.toMap()
모든 `Stream`의 요소를 `Map`인스턴스로 수집하는데 사용합니다 이렇게 하려면 두 가지 기능을 제공해야 합니다
<br>

- `keyMapper`
- `valueMapper`
<br>

`keyMapper`는 Stream요에서 `Map`의 `Key`를 추출하는데 사용하며, `valueMapper`는 지정된 키와 관련된 값을 추출하는데 사용합니다
<br>

아래는 문자열을 `key`로 저장하고, 길이를 `value`로 하여 `Map`에 요소를 수집하는 예제입니다
```java
Map<String, Integer> result = sampleList.stream().collect(toMap(Function.identity(), String::length))
```
<br>

`Function.identity()`는 stream에 전달된 요소의 값을 반환합니다
만약 중복 요소가 포함되어 있으면 중복을 자동으로 필터링 하지 않습니다 따라서 중복 키가 보이면 즉시 `IllegalStateException`이 발생합니다
```java
List<String> listWithDuplicates = Arrays.asList("a", "bb", "c", "d", "bb");
assertThatThrownBy(() -> { listWithDuplicates.stream()
    .collect(toMap(Function.identity(), String::length));})
    .isInstanceOf(IllegalStateException.class);
```
<br>

key 충돌이 있는 경우 다른 시그니처와 함께 사용해야 합니다
<br>

여기서 `isInstanceOf()`는 충돌 처리 방법을 지정할 수 있습니다
