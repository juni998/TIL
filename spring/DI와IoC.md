# DI / IoC
<br>

## Spring의 IoC(제어의 역전, Inversion of Control)
- 일반적인 프로그램은 개발자가 직접 의존성을 만들고 관리한다.

- 의존성은 쉽게 말해 어떤 객체가 사용해야할 객체라고 할 수 있고, 이것을 직접 new 등을 써서 만들어 쓰면 의존성을 개발자가 직접 만들어 쓴다고 할 수 있다

```java
// OwerController가, 필요한 OwnerRepository의 객체를 직접 생성하는 경우
public class OwnerController {
  private OwnerRepository ownerRepositroy = new OwnerRepository();
}
```
<br>

### Inversion of Control - 제어의 역전
- IoC는 개발자가 `직접 의존성을 만들지 않고, 외부에서 의존성을 가져오는 것`을 의미한다

- 외부에서 내부로 의존성을 주입해주는 것을 DI(Dependency Injection) 라고 한다 `따라서 DI는 IoC의 일종이다`

```java
// OwnerController의 생성자에 OwnerRepository를 인자로 받고, owners에 담고 있다
// 이는 앞의 예시처럼 객체를 직접 생성하지 않고, 외부의 객체를 받고 있는 것
public OwnerController (OwnerRepository ownerRepositroy, VisitRepository visits) {
  this.owners = ownerRepository;
  this.visits = visits
}
```
<br>

## Bean과 스프링 IoC 컨테이너
스프링 IoC 컨테이너가 관리하는 객체들을 `Bean` 이라고 한다 `스프링은 이러한 Bean들의 의존성을 관리하고, 객체를 만들어주며, Bean으로 등록 해주고,`

`이렇게 만들어진 것들을 관리한다.` 개발자가 이 부분까지 신경쓰지 않아도, 프레임워크가 알아서 해준다

<br>

그리고 `스프링 IoC 컨테이너가 위와 같은 것들을 관리 해준다` 이러한 Bean을 담고 있는 스프링 IoC 컨테이너는 두 가지 중 하나를 사용한다

- `ApplicationContext` 혹은 `BeanFactory`
  - ApplicationContext는 BeanFactory를 상속 받으므로 같은 일을 한다

<br>

스프링에서 의존성 주입은 `반드시 Bean으로 등록된 객체들 끼리만 가능`하다. 스프링 IoC 컨테이너는 `Bean으로 등록되지 않은 객체에는 의존성 주입을 해주지 않는다`

<br>

## Bean으로 등록하는 방법은?
### Component Scanning
- `@Component` Annotation을 사용한다

- `개발자가 직접 작성한 Class를 Bean으로 등록해줄 때 사용한다`

- 여러 인터페이스들을 라이프 사이클 콜백이라고 부르는데, @Component 어노테이션이 붙은 모든 클래스를 찾아
그 클래스의 인스턴스를 만들고, Bean으로 등록해 주는 복잡한 작업을 하는 어노테이션 처리기가 스프링에 있다

<br>

## 의존성 주입(DI, Dependency Injection)
`DI`란 스프링이 다른 프레임워크와 차별화되어 제공하는 의존 관계 주입 기능으로,

`객체를 직접 생성하는게 아니라 외부에서 생성 후 주입 시켜주는 방식`이다

DI를 통해서 모듈 간의 결합도가 낮아지고 유연성이 높아진다

그리고 이러한 의존성 주입의 종류는 세 가지가 있다

<br>

## Field Injection (필드 주입)
변수 선언부에 @Autowired Annotation을 붙인다

```java
@Component
public class SampleController {
  @Autowired
  private SampleService sampleService;
}
```

하지만 `Field Injection은 사용을 추천하지 않는다`

<br>

## Field Injection을 사용하면 안되는 이유
> 단일책임(SRP)의 원칙 위반

`의존성을 주입하기 쉽다`

@Autowired 선언 아래 개수 제한 없이 무한정 추가할 수 있기 때문이다

<br>

> 의존성이 숨는다

`DI Container`를 `사용한다는 것은 Class가 자신의 의존성만 책임진다는게 아니라 제공된 의존성 또한 책임`진다

그래서 Class가 어떤 의존성을 책임지지 않을 때, 메소드나 생성자를 통해 확실히 커뮤니케이션이 되어야한다

하지만 `Field Injection`은 `숨은 의존성만 제공`해준다

<br>

> DI Container의 결합성과 테스트 용이성

`DI Freamwork`의 `핵심 아이디어`는 `관리되는 Class`가 `DI Container에 의존성이 없어야 한다`

DI Container없이도 `Unit Test에서 Instance화` 시킬 수 있고, `각각 나누어서 테스트`도 할 수 있다

Container의 결합성이 없다면 관리하거나 관리하지 않는 Class를 사용할 수 있고, 심지어 다른

DI Container로 전환할 수 있다

하지만 `Field Injection`을 사용하면 `필요한 의존성을 가진 Class를 곧바로 Instance화 시킬 수 없다`

<br>

> 불변성

`Field Injection`은 final을 선언할 수 없다 그래서 객체가 변할 수 있다

<br>

- `Field Injection`은 `스프링 컨테이너` 말고는 `외부에서 주입할 수 있는 방법이 없다`
- `Field Injection`은 `읽기 쉽고, 사용하기 편하다`는 것 말고는 `장점이 없다`

<br>

## Setter Injection (수정자 주입)
- `Setter Injection`은 `선택적인 의존성을 사용할 때 유용하다`
- `상황에 따라서 의존성 주입이 가능`하다

<br>

### Setter Injection은 set Method를 정의해서 사용한다
`Setter Injection`으로 의존관계 주입은 런타임시에 할 수 있도록 `낮은 결합도를 가지게 구현`되었다

하지만 `Setter Injection`을 통해서 `Service의 구현체를 주입 해주지 않아도 Controller 객체는 생성이 가능`하다

Controller 객체가 생성 가능하다는 것은 내부에 있는 Service의 Method를 호출 가능하다는 것인데,

`set을 통해 Service의 구현체를 주입해주지 않았으므로, NullpointException 이 발생한다`

주입이 필요한 `객체가 주입이 되지 않아도 얼마든지 객체를 생성할 수 있다는 것이 문제`다

하지만 이 문제를 해결 할 수 있는 방법이 `Constructor Injection`이다

<br>

## Constructor Injection (생성자 주입)
Constructor에 @Autowired 어노테이션을 붙여 의존성 주입을 받을 수 있다

<br>

### Constructor Injection을 사용해야 하는 이유
> Spring Framework Reference에서 권장하는 방법은 생성자 주입 방식이다

<br>

생성사 주입 방식이 좋은 이유는 `필수적으로 사용 해야하는 의존성 없이는 Instance를 만들지 못하도록 강제할 수 있기 때문`이다

> Spring 4.3ver 부터는 Class를 완벽하게 DI Framework 로부터 분리할 수 있다

**단일 생성자에 한해 @Autowired를 붙이지 않아도 된다**
- `클래스의 생성자가 하나`이고 그 생성자로 `주입받은 객체가 Bean으로 등록되어 있다면 @Autowired를 생략`할 수 있다

<br>

### 또한 앞서 살펴본 필드 주입의 단점을 장점으로 가져갈 수 있다

- null을 주입하지 않는 한 `NullpointException 은 발생하지 않는다`
  
  - 의존관계 주입을 하지 않은 경우에 Controller 객체를 생성할 수 없다
  
  - 즉, 의존관계에 대한 내용을 외부로 노출시킴으로 컴파일 타임에 오류를 잡아낼 수 있다

- final을 사용할 수 있다  
  
  - final 사용의 장점은 객체가 불변하도록 할 수 있는 점으로, 누군가가 `Controller 내부에서 Service 객체를 바꿔치기 할 수 없다`

- 의존성 주입하기가 번거로워 위기감을 느낄 수 있다
  
  - 생성자 주입의 경우 생성자의 인자가 많아지면 코드가 길어지며 `개발자로 하여금 위기감을 느끼게 해준다`
  
  - 이를 바탕으로 `SRP 원칙을 생각하게 되고, Refactoring을 하게 된다`

<br>

이러한 이유들로 `Constructor Injecton`을 권장한다

`Setter Injection`을 사용하려면, 합리적인 Default를 부여할 수 있고,

선택적인(Optional) 의존성을 사용할 때만 사용해야 한다
