# Inner Class VS Static Nested Class
> Inner Class 와 Static Nested Class의 차이점과 언제 사용해야 하는지 알아보자
<br>

## Nested Class
> 먼저 Nested Class에 대해 알아보자
<br>

### Nested Class란?
- 클래스 안에 있는 클래스를 의미한다
- Static nested Class와 inner Class로 구분된다
  - 또한 inner Class는 Local inner Class와 anonymous inner Class로 구분된다
<br>

### 언제 사용하는가?
- 한 곳에서만 사용하는 클래스를 묶어서 처리할 필요가 있을 때
- 캡슐화가 필요할 때
- 소스의 가독성과 유지보수성을 높이고 싶을 때
<br>

## Inner Class VS Static Nested Class
**Inner Class**
- 익명 클래스(Inner Class)는 어떤 변수라도 참조할 수 있다

**Static Nested Class**
- Static Nested Class는 static 변수만 참조할 수 있다

### 인스턴스 생성 방법
```java
public class Test {
    class innerClass {

    }

    static class staticNestedClass {
      
    }

    public static void main(String[] args) {
        Test.innerClass innerClass = new Test().new innerClass(); // inner Class 생성 방법 new 연산자를 두 번 사용한다
        Test.staticNestedClass staticNestedClass = new Test.staticNestedClass(); // static Nested Class 생성 방법 
    }
}

```

1. Inner Class : 외부 클래스에 대한 인스턴스를 이용하여 내부 클래스의 인스턴스를 생성한다
2. static Nested Class : new 연산자 한 번을 사용하여 새로운 인스턴스를 생성한다, 외부 클래스에 대한 인스턴스가 필요 없다
<br>


### Inner Class로 인스턴스를 두번 생성 한다면?

```java
public class Test {
    class innerClass {

    }

    static class staticNestedClass {

    }
    public static void main(String[] args) {
        Test.innerClass innerClass1 = new Test().new innerClass();
        Test.innerClass innerClass2 = new Test().new innerClass();

        if (innerClass1 == innerClass2) {
            System.out.println("내부 클래스는 같은 참조");
        } else {
            System.out.println("내부 클래스는 다른 참조");
        }
    }
}

```

- 결과는 "내부 클래스는 다른 참조" 이다
- 클래스이기 때문에 다른 인스턴스를 가지게 된다
<br>

### static Nested Class로 인스턴스를 두 번 생성 한다면?


```java
public class Test {
    class innerClass {

    }

    static class staticNestedClass {

    }
    public static void main(String[] args) {
        Test.staticNestedClass staticNestedClass1 = new Test().staticNestedClass();
        Test.staticNestedClass staticNestedClass2 = new Test().staticNestedClass();

        if (staticNestedClass1 == staticNestedClass2) {
            System.out.println("static Nested Class는 같은 참조");
        } else {
            System.out.println("static Nested Class는 다른 참조");
        }
    }
}

```

- static Nested Class 결과 역시 다른 참조를 가진다.
- 다만 static이기 때문에 외부 클래스의 객체를 만들지 않고 사용 가능하다
<br>

### 그렇다면 차이점은 무엇인가?
- 위에서 말했던 것처럼 inner Class는 외부 클래스의 객체를 만들어야 사용할 수 있다
- staticNested Class는 외부 클래스의 객체가 존재하지 않아도 사용할 수 있다는 차이가 있었다. 
- 아래의 코드를 보면 staticNested Class에서 외부 클래스의 메소드에 접근하면 컴파일 에러가 발생한다.

```java
public class Test {
    
    void myTest() {
        
    }
    class innerClass {
        void innerClassMethod() {
            Test.this.myTest();
        }
    }

    static class staticNestedClass {
        Test.this.myTest(); //에러 발생
    }
```

- InnerClass는 상위 클래스의 인스턴스가 존재해야지 만들어 질 수 있다. 
- 그렇다면 Inner Class는 자신을 만들어준 인스턴스에 대한 '외부 참조'를 갖게 된다. 그리고 이 참조는 숨겨져 있어서 '숨은 외부 참조' 라고 불린다
- 반면에 staticNested Class는 외부 인스턴스 없이도 만들어 질 수 있기 때문에 '외부 참조'가 존재하지 않는다
<br>

> 외부 참조가 존재하기 때문에 Inner Class는 외부 클래스에게 접근이 가능하다는 장점이 있지만 **외부 참조로 오는 단점이 더 많다**
<br>


### 그렇다면 외부참조의 단점은?

1. 참조 값을 담아야 하기 때문에, 인스턴스 생성시 시간적, 공간적으로 성능이 낮아진다 .
2. 외부 인스턴스에 대한 참조가 존재하기 때문에, 가비지 컬렉션이 인스턴스 수거를 하지 못하여 메모리 누수가 생길 수 있다
<br>

## 요약
1. 내부 클래스는 static Nested Class로 생성해 주는것이 좋다
2. inner class를 생성해야 할 경우 외부 클래스로 빼도록 한다
