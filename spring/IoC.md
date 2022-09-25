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
- IoC는 개발자가 직접 의존성을 만들지 않고, 외부에서 의존성을 가져오는 것을 의미한다

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

스프링에서 의존성 주입은 `반드시 Bean으로 등록된 객체들 끼리만 가능`하다. 스프링 IoC 컨테이너는 Bean으로 등록되지 않은 객체에는 의존성 주입을 해주지 않는다

<br>

## Bean으로 등록하는 방법은?
### Component Scanning
- `@Component` Annotation을 사용한다

- `개발자가 직접 작성한 Class를 Bean으로 등록해줄 때 사용한다`

- 여러 인터페이스들을 라이프 사이클 콜백이라고 부르는데, @Component 어노테이션이 붙은 모든 클래스를 찾아
그 클래스의 인스턴스를 만들고, Bean으로 등록해 주는 복잡한 작업을 하는 어노테이션 처리기가 스프링에 있다

