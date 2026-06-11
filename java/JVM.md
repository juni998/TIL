### JVM 구성 요소
<img width="952" height="812" alt="image" src="https://github.com/user-attachments/assets/09483054-ddbe-4c53-a89e-cf0a84158646" />

### 클래스 로더(Class Loader)
#### 클래스 로더가 왜 필요한가?
Java 코드를 작성하고 `javac`로 컴파일하면 `.class`파일이 생긴다. 그런데 이 파일은 그냥 디스크 위에 있는 파일이다.
JVM이 이 파일을 실제로 **메모리에 올려서 실행 가능한 상태**로 만들어야 비로소 프로그램이 돌아간다.
<br>
이 역할을 하는 게 바로 **클래스 로더**이다.

> 디스크에 있는 `.class`파일을 JVM 메모리(런타임 데이터 영역)로 가져오는 배달부 역할

#### 클래스 로더의 3단계 과정

#### 1. 로딩 (Loading) - 파일을 찾아서 읽어라
클랫 로더가 `.class`파일을 찾아 바이트 코드로 읽고, 메서드 영역에 기본 정보를 저장하는 단계

<br>

이때 클래스 로더는 **3계층 구조**로 되어있다.
1. Bootstrap ClassLoader - `java.lang.String` 같은 JDK 핵심 클래스 담당
2. Extension ClassLoader - `javax.*` 등 확장 라이브러리 담당
3. Application ClassLoader - 내가 만든 클래스, 외부 라이브러리 담당
   
<br>

로딩 할 때 **항상 위 계층에 먼저 물어본다.** 이를 **부모 위임 모델(Parent Delegation Model)** 이라고 한다.

<br>

예를 들어 `String` 클래스를 로딩할 때, Application -> Extension -> Bootstrap 순으로 위임해서 결국 Bootstrap이 처리한다.
덕분에 누군가 악의적으로 `java.lang.String`을 가짜로 만들어도 교체가 불가능하며 **보안상 매우 중요한 설계**이다.

#### 2. 링킹 (Linking) - 로딩한 클래스를 검증하고 준비하라
링킹은 세 단계로 나뉜다.
#### 검증 (Verify)
"이 바이트 코드가 JVM 스펙에 맞게 올바르게 작성됐는가?" 를 검사한다. 손상된 파일이나 악의적으로 조작된 `.class` 파일을 걸러낸다.

#### 준비 (Prepare)
`static` 변수들에 기본 값을 할당한다. 아직 실제 값이 들어가는 것은 아니다.
``` java
static int count = 100;
// 준비 단계 : count = 0 (기본 값)
// 실제 값은 초기화 단계에서 대입
```

#### 분석 (Resolve)
클래스 파일 안의 심볼릭 참조를 실제 메모리 주소로 바꾼다.
예를 들어 `"java/lang/String"` 이라는 텍스트로 저장된 참조를 실제 메모리 주소로 연결하는 작업

#### 3. 초기화 (Initialization) - 진짜 값을 넣어라
`static` 변수에 실제 값이 대입되고, `static` 블록이 실행되는 단계이다.

``` java
public class Counter {
    static int count = 100;       // 여기서 100이 실제로 대입됨

    static {
        System.out.println("Counter 클래스 초기화!"); // 여기도 실행됨
    }
}
```

초기화는 **처음 사용될 때 딱 한 번만 실행**된다. 이미 초기화된 클래스는 다시 초기화하지 않는다.


#### 전체 흐름 정리

```
.class 파일
    ↓
① 로딩    → 바이트코드를 메모리에 올림 (메서드 영역)
    ↓
② 링킹
   - 검증  → 바이트코드 유효성 검사
   - 준비  → static 변수에 기본값 (0, null, false)
   - 분석  → 심볼릭 참조 → 실제 주소
    ↓
③ 초기화  → static 변수에 실제 값 대입, static 블록 실행
    ↓
 실행 가능 상태
```
