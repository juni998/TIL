## Stop The World
### STW 핵심 개념
> GC 작업을 위해 JVM이 모든 애플리케이션 스레드를 일시 정지시키는 것
STW는 선택이 아니다. **힙의 일관성(Heap Consistency)**을 보장하기 위한 필수 메커니즘이다.

```
STW가 보장하는 것 :
  GC가 보는 힙 상태 = 실제 힙 상태 (일관성)
  객체 참조 관계가 GC 작업 중 변하지 않음
  살아있는 객체를 절대 수거하지 않음 (Safety)
```

### 동작 원리
#### STW 없이 GC를 하면 생기는 3가지 치명적 문제
- 허상 참조 (Dangling Reference)
- 객체 소실 (Object Lost)
- 포인터 업데이트 중 불일치

#### 허상 참조
```
시나리오 :
  GC 스레드 : 객체 A를 "쓰레기"로 판단, 수거 예약
  앱 스레드 : 바로 그 순간 객체 B가 A를 참조하기 시작

  GC : A 수거 완료
  앱 : B.ref -> A (이미 없어진 메모리 주소 참조)
  결과 : Segmentation Fault 또는 NullPointerException

Java 코드로 보면 :
  // 앱 스레드
  Order order = new Order(); // A 생성
  // <- 이 순간 GC가 order를 수거
  order.setStatus("COMPLETED") // 이미 없는 객체에 접근
```

#### 객체 소실
```
Concurrent Mark 도중 참조 관계가 변경되는 경우:

초기 상태 :
  Root -> A(검정완료) -> B(회색) -> C(흰색)

앱 스레드가 실행 :
  A.ref = C // A에서 C로 직접 참조 추가
  B.ref = null // B에서 C 참조 제거

변경 후 :
  Root -> A(검정) -> C(흰색) // A는 이미 완료라 재스캔 안함
              B(회색) -> null

GC 스레드 입장 :
  "C는 흰색이고 아무도 마킹 안 했네 -> 수거"

결과 :
  C가 살아있는데 수거 됨 -> 데이터 유실
  이게 "Concurrent Mark의 동시성 문제"
  -> SATB가 이걸 해결하기 위해 나온 것이다.
```

#### 포인터 업데이트 중 불일치
```
Evacuation(객체 이사) 중 STW 없으면 :

GC : 객체 A를 주소 0x1000 -> 0x5000 으로 복사 중
앱 : B.ref = 0x1000 으로 참조 (이사 전 주소)
GC : 0x1000 해제 완료
앱 : B.ref 따라가면 -> 이미 해제된 메모리

               복사 시작    복사 완료
               ↓            ↓
0x1000: [데이터] → [Forwarding→0x5000] → [해제됨]
0x5000:              [데이터복사중]     → [데이터완성]

중간 상태에서 앱이 접근하면 어떤 상태를 보게 될지 예측 불가
```

### STW의 실제 구현 - SafePoint 메커니즘
STW는 즉각 멈춤이 아니고 **SafePoint**라는 안전한 지점까지 와서 멈춘다.
```
왜 즉각 멈추면 안 되나?
  스레드가 객체 참조를 반쯤 업데이트하다 멈추면:

  정상 : A.x = 10; A.y = 20; (원자적으로 완료)
  중간 : A.x = 10; [멈춤] A.y = ?? (불완전한 상태)

  GC가 이 불완전한 상태를 보게 됨 -> 잘못된 판단
```

#### SafePoint

```
SafePoint = JVM이 스레드를 안전하게 멈출 수 있는 실행 지점

조건 :
  힙의 모든 참조가 일관된 상태
  JVM이 스택의 모든 변수 타입을 알 수 있음 (OopMap)
  진행 중인 연산이 원자적으로 완료된 상태

SafePoint가 삽입되는 위치 (JIT 컴파일러가 자동 삽입) :
  - 메소드 호출 전 후
  - 루프 뒤 (loop backedge)
  - JNI 호출 경계
```

### STW 전체 타임라인
```
GC 트리거 발생 (Eden 꽉 참 등)
       ↓
JVM : SafePoint 플래그 ON
      (전역 메모리에 플래그 세팅)
       ↓
각 스레드 : 실행 중 주기적으로 플래그 확인
         ↓
      SafePoint 도달 시:
        자신을 일시정지
        JVM에 "나 멈췄어" 신고
       ↓
JVM : 마지막 스레드까지 멈춤 확인 (스핀 대기)
     ↓
모든 스레드 멈춤 확인 -> GC 작업 시작
     ↓
GC 완료
     ↓
JVM : SafePoint 플래그 Off
     ↓
모든 스레드 재개

[ 실제 STW 시간 = TTSP + GC 작업 시간 ]

    Time To SafePoint
    (스레드들이 SafePoint까지 오는 데 걸리는 시간)

```

### TTSP가 중요한 이유
```
GC 로그에서 보이는 STW 시간 = TTSP + 실제 작업 GC 작업

예시 :
    GC 로그 : "STW 200ms"

  실제 내역 :
      TTSP : 150ms  <-  스레드들이 SafePoint 오는 데 걸린 시간
      GC 작업 : 50ms  <- 실제 청소 시간

GC 튜닝만 해봤자 50ms만 줄일 수 있음 
```

### STW가 서비스에 미치는 영향
```
실무 장애 시나리오:

API 서버 (목표 응답시간 200ms):
  정상:    요청 → 처리 50ms → 응답
  STW 중:  요청 → [STW 500ms 대기] → 처리 50ms → 응답 (550ms)

연쇄 장애:
  STW 500ms
    → 요청 타임아웃 (200ms 초과)
    → 클라이언트 재시도
    → 요청 2배 증가
    → 힙 더 빨리 참
    → GC 더 자주 발생
    → STW 더 자주
    → 서비스 다운 💥

특히 위험한 환경:
  - MSA에서 다운스트림 서비스의 STW
    → 업스트림 서비스 연쇄 타임아웃
  - 실시간 결제/주문 처리 서버
    → 짧은 STW도 치명적
```

#### 안티패턴
``` java
// 안티 패턴 1 : 대용량 객체를 루프에서 반복 생성
// STW 빈도 폭증이 주범
for (int i = 0; i < 100000; i++) {
  // 매 반복마다 큰 객체 생성 -> Eden 빠르게 참 -> Young GC 폭발
  byte[] data = new byte[1024 *100]; // 100kb
  process(data);
  // data는 루프 끝나면 죽는 객체
}

// 해결 : 객체 재사용
byte[] data = new byte[1024 * 100]; // 한 번만 생성
for (int i = 0; i < 100000; i++){
  Arrays.fill(data, (byte) 0);
  process(data);
}

// ❌ 안티패턴 2: finalizer 사용
// finalizer가 있는 객체는 GC가 즉시 수거 못함
// STW 시간과 별개로 수거 지연 발생
public class Resource {
    @Override
    protected void finalize() {  // 절대 사용하지 말 것!
        cleanup();
    }
}

// ✅ 해결: try-with-resources
public class Resource implements AutoCloseable {
    @Override
    public void close() {
        cleanup();
    }
}

// ❌ 안티패턴 3: System.gc() 직접 호출
// Full GC를 강제 트리거 → 불필요한 긴 STW
System.gc(); // 절대 운영코드에 넣지 말 것

// ❌ 안티패턴 4: 힙을 너무 크게만 설정
// STW 시간은 살아있는 객체 수에 비례
// 힙이 크다고 STW가 짧아지는 게 아님
// 오히려 Full GC 시 더 오래 걸림
-Xmx64g  // 64GB? Full GC 나면 몇 분씩 멈출 수 있음
```

### STW가 필요한 이유
STW는 **힙의 일관성을 보장하기 위해 필요**하다. STW없이 GC를 실행하면 **세가지 문제**가 발생한다.
첫째, **GC가 쓰레기라 판단한 순간 앱이 해당 객체를 참조하기 시작하면 살아있는 객체가 수거된다.**
둘째, **Concurrent Marking중 참조 관계가 변경**되면 **살아있는 객체를 수거 대상으로 오판**할 수 있다.
셋째, Evacuation 중 **객체 이동이 완료되기 전에 앱이 이전 주소**로 **접근하면 해제된 메모리에 접근**하게 된다.
