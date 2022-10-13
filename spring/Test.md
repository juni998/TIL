# Test에 대하여

<br>

## 테스트가 중요한 이유

> 스프링의 핵심은 DI + Test
> 
> 테스트는 스프링을 학습하는 데 있어 가장 효과적인 방법

테스트의 작성은 스프링의 다양한 기술을 활용하는 방법을 이해하고 검증하고, 실전에

적용하는 방법을 익히는 데 효과적으로 사용될 수 있다

<br>

또한 테스트가 없었다면, 코드를 수정하고 설계를 개선해나가는 과정에서 확신이 없었을 것

<br>

## 단위테스트

> 테스트하고자 하는 대상이 명확하다면 그 대상에만 집중해서 테스트하는 것이 바람직하다

한꺼번에 너무 많은 것을 몰아서 테스트하면 테스트 수행 과정도 복잡해지고,

오류가 발생했을 때 정확한 원인을 찾기 힘들어진다

그렇기 때문에 작은 단위의 코드에 대해 테스트를 수행하는 `단위 테스트`를 수행한다

<br>

### 단위 테스트 란?

충분히 하나의 관심에 집중해서 효율적으로 테스트할 만한 범위의 단위

<br>

### 단위 테스트가 필요한 이유

개발자가 설계하고 만든 코드가 원래 의도한 대로 동작하는지를 개발자 스스로 빨리 확인받기 위해

<br>

## 단위 테스트 결과의 일관성

> 코드에 변경사항이 없다면 단위 테스트는 항상 동일한 결과를 내야 한다

<br>

## JUnit
> JUnit 프레임워크가 요구하는 조건 두 가지를 따라야 한다

1. 메소드가 public으로 선언돼야 한다

2. 메소드에 @Test 라는 애노테이션을 붙여줘야 한다

<br>

### JUnit의 수행

> 테스트 메소드를 실행할 때마다 테스트 캘르스의 오브젝트를 새로 만든다

@Test 가 붙은 테스트 메소드를 두 개 갖고 있다면, 테스트가 실행되는 중에 JUnit은 이 클래스의 오브젝트를 두 번 만든다

<br>

### 왜 오브젝트를 새로 만들까

> 각 테스트가 서로 영향을 주지 않고 독립적으로 실행됨을 확실히 보장해주기 위해

덕분에 인스턴스 변수도 부담없이 사용이 가능하다

<br>

### 픽스처

> 테스트를 수해하는 데 필요한 정보나 오브젝트

일반적으로 픽스처는 여러 테스트에서 반복적으로 사용기 때문에 @Before 메소드를 이용해 생성해두면 편리하다

<br>

### @BeforeClass

> 테스트 클래스 전체에 걸쳐 딱 한번만 실행되는 스태틱 메소드

`이 메소드에 어플리케이션 컨텍스트를 만들어 저장`하게 되면 `테스트 오브젝트가 만들어질 때`

`딱 한 번만 애플리케이션 컨텍스트를 만들어두고, 테스트 오브젝트가 만들어질 때마다` 

특별한 방법을 통해 `애플리케이션 컨텍스트 자신을 테스트 오브젝트의 특정 필드에 주입해준다`

<br>

### @Autowired
> @Autowired가 붙은 인스턴스 변수가 있으면, 테스트 컨텍스트 프레임워크는
>
> 변수 타입과 일치하는 컨텍스트 내의 빈을 찾고, 일치하는 빈이 있으면 인스턴스 변수에 주입해준다

<br>

## 포괄적인 테스트

> 한 가지 결과만 검증하고 마는 것은 상당히 위험하다. 마치 죽은 시계가 하루에 두 번은 정확히 맞는다는 것과 같다

<br>

### 항상 부정적인 케이스를 먼저 만드는 습관을 들이는 것이 좋다

개발자는 빨리 테스트를 만들어 성공하는 것을 보고 다음 기능으로 나아가고 싶어하기 때문에,

긍정적인 경우를 골라서 성공할 만한 테스트를 먼저 작성하게 되는데 이는 위험하다

<br>

## 테스트 주도 개발 (TDD, Test Driven Development)

> 만들고자 하는 기능의 내용을 담고 있으면서 만들어진 코드를 검증도 해줄 수 있도록
>
> 테스트 코드를 먼저 만들고, 테스트를 성공하게 해주는 코드를 작성하는 방식의 개발 방법

<br>

### TDD 기본 원칙

> 실패한 테스트를 성공시키기 위한 목적이 아닌 코드는 만들지 않는다

위의 원칙을 따랐다면 만들어진 모든 코드는 빠짐없이 테스트로 검증된 것이라고 볼 수 있다

<br>

개발자는 새로운 기능을 가진 코드를 만들려고 하는 순간, 

"이런 조건하에서 이런 작업을 하면 이런 결과가 나올 것이다" 라는 식으로 기능 정리를 한다

이러한 머릿속으로 복잡하게 진행하던 작업을 실제 코드로 끄집어 내놓으면 이게 바로 `TDD`가 된다

<br>

## DI와 테스트

> 굳이 인터페이스를 사용하고 DI를 통해 주입해주는 방식을 이용해야 하는 이유

1. 소프트웨어 개발에서 절대로 바뀌지 않는 것은 없다

2. 구현 방식이 바뀌지 않더라도 인터페이스를 두고 DI를 적용하게 해두면 다른 차원의 서비스 기능을 도입할 수 있다

3. 효율적인 테스트를 손쉽게 만들기 위해서라도 DI를 적용해야 한다

<br>

### DI를 이용한 테스트 방법 선택

1. 스프링 컨테이너 없이 테스트할 수 있는 방법을 최우선으로 고려

> 테스트 수행 속도가 가장 빠르고 테스트 자체가 간결하다

<br>

2. 여러 오브젝트와 복잡한 의존관계를 갖고 있는 오브젝트를 테스트해야 할 경우

> 스프링의 설정을 이용한 DI방식의 테스트를 이용하면 편리하다

<br>

3. 예외적인 의존관계를 강제로 구성해서 테스트해야 할 경우

> 컨텍스트에서 DI 받은 오브젝트를 다시 테스트 코드로 수동 DI 해서 테스트하는 방법을 사용한다

<br>

## 학습 테스트
> 학습 테스트의 목적은 자신이 사용할 API나 프레임워크의 기능을 테스트로 보면서 사용 방법을 익히는 것

<br>

### 학습 테스트의 장점

1. 다양한 조건에 따른 기능을 손쉽게 확인 가능

<br>

2. 학습 테스트 코드를 개발 중에 참고 가능

> 다양한 기능에 대한 테스트 코드는 좋은 참고 자료가 된다

<br>

3. 프레임워크나 제품을 업그레이드할 때 호환성 검증을 도와줌

> 새로운 버전의 프레임워크나 제품을 학습 테스트에만 먼저 적용해 본다면
> 
> 기존에 사용했던 API나 기능에 변화가 있거나 업데이트된 제품에 버그가 있다면
> 
> 학습 테스트를 통해 미리 확인할 수 있다

<br>

4. 테스트 작성에 대한 좋은 훈련이 된다

<br>

5. 새로운 기술을 공부하는 과정이 즐거워진다

> 문서에 딱딱하게 쓰여진 기능이 테스트 코드를 통해 실제로 동작하는 모습을 보는 건 즐겁다
> 
> 스프링 배포판의 압축을 풀어보면 테스트 코드를 발견할 수 있는데, 잘 살펴보면 중요한 정보를 많이 얻을 수 있다

<br>

## 버그 테스트

> 오류가 있을 때 그 오류를 가장 잘 드러내줄 수 있는 테스트

<br>

### 버그 테스트의 필요성

1. 테스트의 완성도를 높여준다

> 미처 검증하지 못했던 부분이 있기 때문에 오류가 발생한 것.
>
> 이에 대해 테스트를 만들면 불충분했던 테스트를 보완해준다

<br>

2. 버그의 내용을 명확하게 분석하게 해준다

> 버그가 있을 때 테스트로 만들어서 실패하게 하려면 어떤 이유 때문에
> 
> 문제가 생겼는지 명확하게 알아야 하기 때문이다

<br>

3. 기술적인 문제를 해결하는 데 도움이 된다




