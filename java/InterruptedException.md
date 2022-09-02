# InterruptedException
> InterruptedException에 대한 학습
<br>

## Interrupt
- 자바에서 인터럽트는 스레드를 종료하기 위한 메커니즘
- 일반적으로 자바 프로그램은 커다란 작업을 작은 작업들로 나눠서 각각 스레드에 할당 후 실행한다.
  -  동시에 여러 스레드가 실행되기 때문에 전체 작업이 더 빨리 끝난다
<br>

### 사용자가 작업을 취소하거나, 전체 작업이 종료되어야 할 경우?
- 리소스를 정리한 다음 종료하라고 알려줘야 한다. 이 때 **인터럽트**를 사용한다
- 자바에서 한 스레드는 권한이 있다면 다른 스레드에 인터럽트를 보낼 수 있다 
<br>

### 인터럽트 사용
```java
public static void main(String[] args) throws InterruptedException {
    Thread thread = new SmallTaskThread();

    // 작업 스레드 시작
    thread.start();

    Thread.sleep(1000);
		
    // 작업 스레드에게 인터럽트 보냄
    thread.interrupt();
}
```
스레드를 `start()` 메소드로 생성 후 1초간 sleep 했다가 `interrupt()` 메소드를 이용하여 스레드에 인터럽트를 전달하는 클래스
<br>

### 인터럽트 상태 확인 메소드
인터럽트를 받은 스레드는 인터럽트 상태(Interrupt State)가 된다
<br>

스레드 종료와 스레드가 인터럽트가 됐는지 확인하는 방법은 다음과 같다
```java
if (Thread.currentThread().isInterrupted()) {
    /* 리소스 정리 코드 수행 */
}
```
<br>

### 블로킹 된 경우
다음과 같이 인터럽트가 종료됐는지 체크하고 종료 로직을 넣어놨지만 스레드 어딘가에 블로킹 되어 있다면 인터럽트 로직 실행은 몇 초간 심하면 영원히 실행되지 않을 가능성이 있다
```java
Thread.sleep(31536000000); //1년 sleep

if (Thread.currentThread().isInterrupted()) {
    /* 리소스 정리 코드 수행 */
}
```
<br>

스레드 실행을 종료하는 코드가 바로 아래 있지만 인터럽트 상태를 확인하려면 sleep에서 벗어나야 한다
<br>

하지만 자바에서는 블로킹 가능한 메소드들은 내부에서 스레드의 인터럽트 상태를 확인한다
<br>

만약 sleep을 하고 있는 동안 외부에서 인터럽트가 도착해 인터럽트 상태가 설정됐다면 `InterruptedException`이 발생한다
<br>

## InterruptedException
- 스레드들이 인터럽트를 주고 받을 때 인터럽트를 받는 스레드가 블로킹될 수 있는 메소드를 실행하면 `InterruptedException`이 발생한다
- `InterruptedException`은 checked Exception으로 `try-catch`로 잡아서 처리하거나, 메소드 시그니처에 명시해서 메소드 호출자에게 처리를 위임 해야한다

