# 클래스와 객체, 인스턴스
> 클래스와 객체, 인스턴스의 개념 및 차이점을 알아보자
<br>

## 개념

### 클래스(Class)
- 객체를 만들어 내기 위한 **설계도** 혹은 틀
- 연관되어 있는 변수와 메소드의 집합
<br>

### 객체(Object)
- 소프트웨어 세계에 **구현할 대상**
- 클래스에 선언된 모양 그대로 생성된 실체
- "클래스의 인스턴스"라고도 불린다
- OOP의 관점에서 클래스의 타입으로 선언되었을 때 "객체"라고 부른다
<br>

### 인스턴스(Instance)
- 설계도를 바탕으로 소프트웨어 세계에 **구현된 구체적인 실체**
- 실체화된 인스턴스는 메모리에 할당된다
- 인스턴스는 객체에 포함된다고 볼 수 있다
- OOP의 관점에서 객체가 메모리에 할당되어 사용될 때 "인스턴스"라고 부른다
<br>

### 예시
```java
//클래스
public class Phone {
  ...
}

public class Main {
  public static void mian(String[] args) {
    Phone iPhone, galaxy; //객체
	
    //인스턴스화
    iPhone = new Phone(); // iPhone은 Phone 클래스의 인스턴스(객체를 메모리에 할당)
    galaxy = new Phone(); // galaxy는 Phone 클래스의 인스턴스(객체를 메모리에 할당)
  }
}
```
<br>

## 차이점

### 클래스 VS 객체
- 클래스는 **설계도**, 객체는 **설계도로 구현한 모든 대상**을 의미한다

<br>

### 객체 VS 인스턴스
- **클래스의 타입으로 선언되었을 때** 객체라고 부르고, 그 **객체가 메모리에 할당되어 실제 사용될 때** 인스턴스라고 부른다
- 객체는 **실체**, 인스턴스는 **관계**에 초점을 맞춘다