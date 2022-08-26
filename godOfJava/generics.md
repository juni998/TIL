# 제네릭
> 제네릭에 대해 알아봅시다
<br>

## Generic 이란?
- JAVA에서 제네릭이란 데이터의 타입을 일반화 한다는 의미이다
- 제네릭은 클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법이다
<br>

### 컴파일 시에 미리 타입 검사를 수행하면 가지는 장점
1. 클래스나 메소드 내부에서 사용되는 객체의 타입 안정성을 높일 수 있다
2. 반환 값에 대한 타입 변환 및 타입 검사에 들어가는 노력을 줄일 수 있다
<br>

### 예제
DTO를 3개의 인스턴스로 받아서 각각 String, StringBuffer, StringBuilder로 꺼내오려고 한다
<br>

DTO 생성
```java
public class CastingDTO {
    private Object object;

    public void setObject(Object object) {
        this.object = object;
    }

    public Object getObject() {
        return object;
    }
}
```

<br>

Main 클래스
```java
public class GenericSample {

    public static void main(String[] args) {
        GenericSample genericSample = new GenericSample();
        genericSample.checkCastingDTO();
    }

    public void checkCastingDTO() {
        CastingDTO dto1 = new CastingDTO();
        CastingDTO dto2 = new CastingDTO();
        CastingDTO dto3 = new CastingDTO();
        
        dto1.setObject(new String());
        dto2.setObject(new StringBuffer());
        dto3.setObject(new StringBuilder());
       
    }
}
```
이와 같이 코드를 작성 후 각 객체의 getObject() 메소드를 호출하면, 리턴 값으로 넘어오는 타입은 Object이다.
따라서 아래와 같이 형 변환을 해야하는 단점이 있다

<br>


```java
String temp1 = (String)dto1.getObject();
StringBuffer temp2 = (StringBuffer)dto2.getObject();
StringBuilder temp3 = (StringBuilder)dto3.getObject();
```
이러한 단점을 보완하기 위해서 Generic이라는 것이 있다

<br>

### Generic 사용법
DTO를 Generic으로 선언하여 변경
```java
public class CastingDTO<T> {
    private T object;

    public void setObject(T obj) {
        this.object = obj;
    }

    public Object getObject() {
        return object;
    }

}
```

<br>

Main 클래스
```java
public class GenericSample {

    public static void main(String[] args) {
        GenericSample genericSample = new GenericSample();
        genericSample.checkGenericDTO();
    }

    public void checkGenericDTO() {
        CastingDTO<String> dto1 = new CastingDTO<>();
        CastingDTO<StringBuffer> dto2 = new CastingDTO<>();
        CastingDTO<StringBuilder> dto3 = new CastingDTO<>();


        dto1.setObject(new String());
        dto2.setObject(new StringBuffer());
        dto3.setObject(new StringBuilder());
    }
}
```
이와 같이 제네릭 타입을 각각 지정해줬기 때문에 형 변환을 할 필요가 없어졌다.

<br>

## <> 안에 들어가는 T는 무엇인가?
### 제네릭 타입의 이름 규칙
제네릭 타입 <> 안에 들어가는 이름엔 다음과 같이 규칙이 있다
- E : 요소
- K : 키
- N : 숫자
- T : 타입
- V : 값
- S, U, V = 두 번째, 세 번째, 네 번째에 선언된 타입

<br>

> 마지막 규칙을 보면 제네릭 타입을 여러개 지정 가능하다는 것을 예측할 수 있다

<br>

### Reference
- 자바의 신 2권


