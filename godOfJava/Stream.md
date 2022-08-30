# Stream
> JAVA8에 추가된 Stream에 대해 알아봅시다

<br>

스트림은 다음과 같은 불편함을 해결하기 위해 만들어졌다
- Collection을 사용하는 코드를 반복적으로 구현
- 병렬적으로 Collection을 처리하기 어려움

<br>

## 스트림의 구조
`list.stream().filter(x -> x > 10).count();`
- `stream()` : 스트림 생성
- `filter(x -> x > 10)` : 중개 연산
- `count()` : 종단 연산

<br>

- 스트림 생성 : 컬렉션의 목록을 스트림 객체로 변환한다. `stream()` 메소드는 `Collection` 인터페이스에 선언되어 있다
- 중개 연산 : 생성된 스트림 객체를 사용하여 중개 연산 부분에서 처리한다. 하지만, 아무런 결과를 리턴하지 못한다
- 종단 연산 : 중개 연산에서 작업된 내용을 바탕으로 결과를 리턴한다

<br>

> 중개 연산은 반드시 있어야 하는 것이 아니다 즉, 스트림에는 0개 이상의 중개 연산이 존재한다

<br>

### 스트림 연산의 종류
- `filter()` : 데이터를 조건으로 거를 때 사용
- `map()` : 데이터를 특정 데이터로 변환
- `forEach()` : for 루프를 수행하는 것처럼 각각의 항목을 꺼냄
- `sorted()` : 데이터 정렬
- `toArray()` : 배열로 변환
- `any` / `all` / `noneMatch()` : 일치하는 것을 찾음
- `collect()` : 원하는 타입으로 데이터를 리턴

<br>

스트림의 주요 메서드에 대해서 알아봅시다
<br>

## Stream forEach
`forEach()`를 알아보기 위해 학생의 정보를 담고있는 DTO를 생성한다

```java
public class StudentDTO {
    String name;
    int age;
    int scoreMath;
    int scoreEnglish;
}
```

<br>

그리고 학생의 정보를 추가하고, 출력하는 클래스를 생성한다
```java
public class StudentForEachSample {
    public static void main(String[] args) {
        StudentForEachSample sample = new StudentForEachSample();
        List<StudentDTO> students = new ArrayList<>();
        students.add(new StudentDTO("요다", 43, 99, 10));
        students.add(new StudentDTO("만두", 30, 71, 85));
        students.add(new StudentDTO("찐빵", 32, 81, 75));
        sample.printStudentNames(students);
    }

    public void printStudentNames(List<StudentDTO> students) {
        students.stream().forEach(student -> System.out.println(student.getName()));
    }
}
```

<br>

위 코드의 스트림 처리한 문장을 자세하게 살펴보자
<br>

`students.stream().forEach(student -> System.out.println(student.getName()));`
<br>

`forEach` 다음에 있는 `student`는 `students`라는 List 객체에 담겨 있는 하나의 `StudentDTO` 객체를 의미한다
<br>

이 문장은 아래와 같이 `forEach`문으로 구현해도 동일하다
<br>

`for(StudentDTO student : students) System.out.println(student.getName());`

<br>

## 메소드 참조
```java
forEach(x -> System.out.println(x));
```
<br>

앞의 예제에서 forEach의 출력 문장은 다음과 같이 처리할 수 있다
```java
forEach(System.out::println)
```

이 더블 콜론(::)은 메소드 레퍼런스(Method Reference)라고 부른다. 즉, 메소드 참조를 의미한다

<br>

### 메소드 참조의 종류
메소드 참조의 종류에는 4가지가 있다
- static 메소드 참조
- 특정 객체의 인스턴스 메소드 참조
- 특정 유형의 임의의 객체에 대한 인스턴스 메소드 참조
- 생성자 참조

<br>

### 예시
다음 예제는 두 개의 값을 전달받아 제곱 연산을 수행하는 `Math` 클래스의 메소드인 `pow()`메소드를 호출하는 람다 표현식이다
```java
(base, exponent) -> Math.pow(base, exponent);
```

<br>

위의 예제는 단순히 `pow()`메소드로 인수를 전달하는 역할만 하므로 메소드 참조를 사용하여 다음과 같이 간단히 표현할 수 있다
```java
Math::pow;
```

<br>

특정 인스턴스의 메소드를 참조할 때에도 참조 변수의 이름을 통해 메소드 참조를 사용할 수 있다
```java
MyClass obj = new MyClass;

Function<String, Boolean> func = (a) -> obj.equals(a); // 람다 표현식
Function<String, Boolean> func = obj::equals(a);       // 메소드 참조
```

<br>

## Stream map()
앞의 예제에서 본 `forEach()`는 종단 연산에 속한다
<br>

이제부터는 중개 연산에 대해 알아보자 일반적으로 가장 많이 사용되는 중개 연산은 `map()`과 `filter()`가 있다
<br>

예시로 다음과 같은 List가 있다 
```java
List<Integer intList = Arrays.asList(1, 2, 3, 4, 5);
```
`intList`에 있는 정수를 모두 3배수로 변환해서 출력하려고 한다

<br>

기존의 방식이라면 이렇게 처리할 수 있다 
```java
for(int value : intList) {
  System.out.println(value * 3);
}
```
이렇게 for 루프 문장을 포함하여 처리해야만 한다

<br>

이것을 스트림과 `map()`으로 처리하면 다음과 같다 
```java
intList.stream().map(x -> x * 3).forEach(System.out::println)
```
이렇게 `map(x -> x * 3)`으로 변환이 되면 
<br>

`map(x -> x * 3)`에서 `intList`의 {1, 2, 3, 4, 5}의 값이
<br>

{3, 6, 9, 12, 15}으로 바뀌고, 바뀐 값을 `forEach()`에서 출력하게 된다
<br>
이렇게 `map()`을 사용하면 스트림에서 처리하는 값들을 중간에 변경할 수있다

<br>

## Stream Collector()
아래의 예제에서 이름만을 List로 뽑아 내고 싶을 경우 어떻게 해야 할까?
```java
List<StudentDTO> students = new ArrayList<>();
students.add(new StudentDTO("요다", 43, 99, 10));
students.add(new StudentDTO("만두", 30, 71, 85));
students.add(new StudentDTO("찐빵", 32, 81, 75));
```

<br>

다음과 같이 Stream의 `collect()`를 사용하여 간단히 뽑아낼 수 있다
```java
List<String> nameList = studentList.stream()
  .map(student -> student.getName()).collect(Collectors.toList());
```

<br>

이와 같이 `collect()` 메소드를 사용하면 List로 변환이 가능하다

<br>

## Stream filter()
아래의 예제에서 수학 점수가 80점 이상의 점수를 갖는 사람의 이름이 출력되게 하려면 어떻게 해야할까?
```java
List<StudentDTO> students = new ArrayList<>();
students.add(new StudentDTO("요다", 43, 99, 10));
students.add(new StudentDTO("만두", 30, 71, 85));
students.add(new StudentDTO("찐빵", 32, 81, 75));
```

<br>

다음과 같이 `filter()` 메소드를 사용하여 간단히 뽑아낼 수 있다
```java
studentList.stream()
  .filter(student -> student.getScoreMath() > cutLine)
  .forEach(student -> System.out.println(student.getName()));
```

<br>

이와 같이 `studentList`를 스트림 처리 하고 수학 점수가 `cutLine`보다 큰 학생만 `forEach()` 구문으로 이동한다
<br>

즉 `filter()`는 `if`문처럼 스트림 내에서 필요한 데이터를 걸러서 처리할 때 사용한다

