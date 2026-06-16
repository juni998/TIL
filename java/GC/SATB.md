## Snapshot At the beginning
### 핵심 개념
> STAB = Concurrent Mark 시작 지점의 객체 그래프를 논리적 기준으로 삼아, Tri-color Invariant 위반을 방지하는 알고리즘

#### Tri-color Invariant 위반 조건을 다시 보면
```
위반 조건 1 : 검정 객체 -> 흰색 객체 새 참조 추가
위반 조건 2 : 그 흰색 객체로 가는 모든 회색 경로 제거

두 조건 동시에 만족 -> 살아있는 객체 소실

SATB 해결 전략:
  조건 2를 원천 차단하자
  회색 경로가 제거될 때 그 값을 보존해두면
  흰색 객체는 항상 도달 가능성이 유지됨
```

### 동작 원리
#### SATB의 핵심 아이디어
```
Concurrent Mark 시작 지점:
  이 순간의 객체 그래프를 "논리적 스냅샷"으로 봄

스냅샷 기준 원칙:
  시작 지점에 살아있던 객체는 이번 마킹 사이클에서 절대 수거하지 않는다.

구현 방법:
  참조가 끊어지기 직전 값을 SATB 큐에 보존
  -> 끊어진 참조를 통해 도달하던 객체도 살아있다고 처리
```

#### SATB 큐 구조
```
SATB 큐는 스레드마다 하나씩 존재 (Thread-Local)

Thread-1 SATB Queue : [B, X, Y]
Thread-2 SATB Queue : [C, Z]
Thread-3 SATB Queue : [A]

왜 Thread-Local 인가?
  전역 큐 하나에 모든 스레드가 동시에 접근하면
  -> Lock 경합 발생 -> 성능 저하
  -> 각 스레드가 자기 큐에만 씀

큐가 가득 차면 :
-> 전역 SATB 버퍼 리스트에 flush
-> Concurrent Refinement 스레드가 처리
```

### 전체 SATB 처리 흐름
```
━━━━ Concurrent Mark 시작 ━━━━
  Initial Mark (STW):
    GC Root 직접 참조 -> 회색으로 표시
    모든 스레드의 SATB 큐 활성화

━━━━ Concurrent Mark 진행 ━━━━
  GC 스레드: 힙 순회하며 마킹
  앱 스레드: 실행 중, 참조 변경 시 SATB 큐에 기록

  [앱 스레드]                    [GC 스레드]
  a.child = c 실행하려 함        B, D, E 마킹 중...
      ↓
  Pre-Write Barrier:
    oldValue = a.child (= b)
    satbQueue.add(b)             ← b가 큐에 들어감
      ↓
  a.child = c 실행

━━━━ Remark (STW) ━━━━
  모든 스레드 멈춤
      ↓
  각 스레드의 SATB 큐 수집
  Thread-1: [b, x]
  Thread-2: [y, z]
  전역 버퍼: [m, n, o]
      ↓
  큐에 있는 모든 객체 재마킹
    b → 회색으로, b의 자식들 처리
    x, y, z, m, n, o 동일하게 처리
      ↓
  마킹 최종 확정

━━━━ 결과 ━━━━
  흰색 = 수거 대상 확정
  SATB 큐에 있던 객체들 = 살아있음으로 처리
```

### 구체적 시나리오
```
초기 상태 (Concurrent Mark 진행 중):

  ⬛A → 🔘B → ⬜C
                ⬜D

앱 스레드 실행:
  A.child = D;    // 1번 코드
  B.child = null; // 2번 코드

━━━━ 1번 코드 실행 시 ━━━━

Pre-Write Barrier:
  oldValue = A.child (현재 A의 child = B)
  satbQueue.add(B)     ← B 기록!

실제 변경:
  A.child = D

현재 상태:
  ⬛A → ⬜D
  🔘B → ⬜C    (A에서 B 참조 끊어짐)
  satbQueue: [B]

━━━━ 2번 코드 실행 시 ━━━━

Pre-Write Barrier:
  oldValue = B.child (현재 B의 child = C)
  satbQueue.add(C)     ← C도 기록!

실제 변경:
  B.child = null

현재 상태:
  ⬛A → ⬜D
  🔘B           (자식 없음)
  ⬜C           (아무도 참조 안 함, 실제론 죽음)
  satbQueue: [B, C]

━━━━ Remark STW ━━━━

큐 처리:
  B 재마킹:
    B의 현재 자식 없음 → B → ⬛ (검정)
  C 재마킹:
    C의 현재 자식 없음 → C → ⬛ (검정)
    (C는 실제로 죽었지만 이번엔 살아있다고 처리)
  D 처리:
    A(검정)에서 D(흰색) 참조 → D → ⬛

최종 결과:
  ⬛A ⬛B ⬛C ⬛D 모두 살아있음으로 처리

C는 Floating Garbage:
  실제로 죽었지만 이번 사이클에서 못 치움
  → 다음 GC 사이클에서 수거
```

### TAMS와 SATB의 관계
> TAMS (Top At Mark Start) = Councurrent Mark 중에 새로 생성된 객체를 자동으로 처리하는 경계선

#### SATB만으로는 부족한 이유
```
문제 :
  Concurrent Mark 진행 중에 새로 생성된 객체는?

  GC : 마킹 시작 시점 기준으로 판단
  앱 : 마킹 도중에도 new Object() 계속 호출

  새로 생긴 객체는 스냅샷에 없음
  -> 흰색으로 시작
  -> GC가 쓰레기 라고 판단할 수 있음

TAMS 해결책 :
  Concurrent Mark 시작 시점에
  각 Region의 현재 할당 위치(Top)를 TAMS로 기록

  TAMS 위쪽에 새로 할당되는 객체
  -> 자동으로 검정 처리
  -> 마킹 대상에서 제외

Region 메모리 레이아웃:
    [Bottom ── 기존 객체들 ── TAMS ── 새 객체들 ── Top]
                            ↑           ↑
                     Mark 시작       Mark 진행 중
                     경계선          새로 생긴 것들
                                    (자동으로 살아있음)
```

```
SATB와 TAMS의 역할 분담 :
SATB : 마킹 시작 전부터 있던 객체 중 참조가 끊어지는 것을 보호
TAMS : 마킹 시작 후 새로 생기는 객체를 자동으로 살아있음 처리

둘이 합쳐져야 Concurrent Mark가 완전히 안전해짐
```

### Flating Garbage 허용 범위
```
Floating Garbage가 너무 많아지면?

  이번 사이클에서 못 치운 쓰레기
  → Old Gen에 쌓임
  → IHOP(45%) 빨리 도달
  → Concurrent Mark 더 자주 시작
  → CPU 오버헤드 증가

실무 판단 기준:
  Mixed GC 후 Old Gen 사용률이
  예상보다 높게 유지되면 Floating Garbage 의심

  -Xlog:gc+heap=debug 로 확인
  Mixed GC 전후 Old Gen 크기 비교
  예상 수거량 vs 실제 수거량 차이 분석
```

### G1GC가 SATB를 선택한 이유?
SATB는 변경 전 값을 보존해서 끊어지는 참조 경로를 추적한다. Remark시 SATB큐에 쌓인 것만 처리하면 되므로 Remark STW가 짧다.
대신 이미 죽은 객체를 살아있다고 볼 수 있는 Flating Garbage가 발새한다. G1GC는 Remark STW를 짧게 유지하는 것이 더 중요했기 때문에 SATB를 선택했다.

### Concurrent Mark 중에 새로 생성된 객체는 어떻게 처리 되는가
TAMS로 처리한다. Concurrent Mark 시작 지점에 각 Region의 현재 할당 위치를 TAMS로 기록해두고, TAMS이후에 할당되는
객체는 자동으로 살아있음으로 처리한다. SATB는 기존에 있던 객체의 참조가 끊어지는 것을 보호하고, TAMS는 마킹 도중 새로 생기는
객체를 보호한다. 두 메커니즘이 함께 동작해야 Concurrent Mark가 완전히 안전하다.

