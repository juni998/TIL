# 캐릭터 타입 인코딩
> 캐릭터 타입의 인코딩 관련하여 학습
<br>

## char타입의 2바이트는 어떻게 인코딩 될까?
- JAVA에서 char타입은 2Byte 즉 UTF-16으로 인코딩 된다
```java
...
  public static void main(String[] args) {
    System.out.println(Character.BYTES + "BYTE"); //결과 : 2BYTE
  }
}
```
<br>

## U+1F408 🐈는 char타입 상수로 쓸 수 있을까?
1. JAVA에서의 char타입의 최대 크기는 2BYTE로 0xFFFF
2. 🐈이모지의 크기는 0x1F408 이며, 이모지의 크기는 2BYTE를 초과한다
3. 🐈이모지의 크기가 char타입의 최대 크기를 초과하기 때문에 char타입의 상수로 사용할 수 없다
<br>

## var x = ‘🐈’ , var x = “🐈” 둘 중 한쪽은 컴파일이 안되는 이유는?
1. JAVA10에서 도입된 var는 변수를 선언할 때 타입을 생략할 수 있으며, 컴파일러가 타입을 추론한다.
2. JAVA에서 ' '(홑 따옴표)로 선언하면 char타입으로 선언되어 문자 하나만 입력할 수 있다
3. var x = "🐈” 로 선언하면 정상적으로 이모지가 출력 된다
4. 하지만 var x = '🐈'로 선언하면 이모지의 크기가 char타입의 최대 크기를 초과하기 때문에 char타입의 상수로 사용할 수 없다
```java
...
  public static void main(String[] args) {
    var x = "🐈";
    var y = '🐈';
    
    System.out.println(x); //정상 출력
    System.out.println(y); //캐릭터 타입 범위를 초과했다는 에러가 발생한다
  }
} 
```
