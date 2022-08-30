# LinkedList와 ArrayList
> LinkedList와 ArrayList에 대해 알아보고 각각 장단점과 언제 사용하면 좋을지에 대해서 알아봅시다

<br>

## ArrayList
ArrayList는 연관된 데이터를 **메모리상에 연속적이며 순차적**으로 **미리 할당된 크기**만큼 저장한다

<br>

### ArrayList의 특징
- 고정된 저장 공간
- 순차적인 데이터 저장
<br>

### ArrayList의 시간 복잡도
- access : O(1)
- append : O(1)
- 마지막 원소 delete : O(1)
- insert : O(n)
- delete : O(n)
- search : O(n)

<br>

### ArrayList의 장점
ArrayList의 장점은 조회(lookup)와 추가(append)가 빠르다.
따라서 조회를 자주 해야되는 작업에서 ArrayList를 사용한다

<br>

### ArrayList의 단점
ArrayList의 단점은 고정된 저장 공간 특성상 선언시 크기를 미리 정해야 한다. 이는 메모리 낭비나 추가적인 overhead가 필요할 수 있다

<br>

여기서 말하는 overhead란 데이터를 추가(append O(1) 하다가 메모리를 초과하게 되면 기존 Array의 크기보다
더 큰 ArrayList를 선언하고, 모든 데이터를 옮기고, 기존 Array는 삭제하는 작업이 필요하다

<br>

## Dynamic Array
ArrayList의 경우 크기가 고정되어 있기 때문에 선언시에 설정한 size보다 많은 갯수의 데이터가 추가되면 저장할 수 없다
이러한 한계을 보완하고자 Dynamic Array가 있다
<br>

Dynamic Array는 저장공간이 가득 차게 되면 resize를 하여 유동적으로 크기를 조절하여 데이터를 저장한다

<br>

### Dynamic Array의 장점
- 저장공간이 가득 차게 되면 resize를 하기 때문에 크기를 미리 고민할 필요가 없다

<br>

Dynamic Array의 resizing을 하는 방법은 대표적으로 기존 Array 크기의 2배를 할당하는 doubling이 있다

<br>

### Doubling
데이터를 추가하다가 메모리를 초과하게 되면 기존 배열의 크기보다 두배 큰 배열을 선언하고 데이터를 일일이 옮기는 방법이다

<br>

### 분할상환 시간복잡도 Amortized time complexity
1. Dynamic Array에 데이터를 추가할 때마다 O(1)의 시간이 걸리게 된다
2. 추가를 하다가 미리 선언된 size를 넘어서는 순간에 resize를 한다
3. 이 때 일일이 데이터를 모두 옮겨야 되기 때문에 O(n)의 시간이 걸리게 된다
4. 그렇다면 append의 시간복잡도는 O(1)와 O(n)중에 무엇일까?

<br>

append는 데이터를 마지막 인덱스에 추가하는 O(1)이 대다수 작업이고,
size를 넘어서 resize를 진행할 때만 O(n)의 시간이 걸리게 된다
append의 전체적인 시간복잡도는 O(1)이지만, 
조금 더 정확히 말하면 **amortized O(1)** 이라고 부른다

amortized O(1)을 설명하자면 가끔 발생하는 O(n)의 resize하는 시간을 자주 발생하는 O(1)의 작업들이 
분담해서 나눠 가짐으로 전체적으로 O(1)의 시간이 걸린다고 보면 된다

<br>

## Linked List
1. Linked List는 Node라는 구조체로 이루어져 있다 Node는 데이터 값과 다음 Node의 주소를 저장한다.
2. Linked List는 물리적인 메모리상에서 비 연속적으로 저장이 되지만, 
각각의 Node가 next Node의 주소를 가리킴으로써 논리적인 연속성을 가진다

<br>

### 논리적 연속성
각 노드들은 next address 정보를 가지고 있기 때문에 논리적으로 연속성을 유지하면서 연결되어 있다.
Array의 경우 연속성을 유지하기 위해 물리적 메모리 상에서 순차적으로 저장하는 방법을 사용하였고,
Linked list에는 메모리에서 연속성을 유지하지 않아도 되기 때문에 메모리 사용이 좀 더 자유로운 대신
next address를 추가적으로 저장해야 하기 때문에 데이터 하나당 차지하는 메모리가 더 커지게 된다

<br>

### Linked List의 시간복잡도 
- access : O(n)
- search : O(n)
- 첫 번째 원소에 insertion : O(1)
- 첫 번째 원소에 deletion : O(1)
- 그 외 insertion : O(n)
- 그 외 deletion : O(n)

<br>

## ArrayList VS Linked List
- ArrayList는 메모리 상에서 연속적으로 데이터를 저장한다
- LinkedList는 메모리 상에서는 연속적이지 않지만, 각각의 원소가 다음 원소의 메모리 주소값을 저장함으로써 논리적 연속성을 유지한다
- 이러한 점은 시간복잡도에서 차이가 난다

<br>

### 조회 (lookup)
#### ArrayList
ArrayList는 메모리상에서 연속적으로 데이터를 저장하였기 때문에 저장된 데이터에 즉시 접근할 수 있다 O(1)

<br>

#### LinkedList
LinkedList는 메모리 상에서 불 연속적으로 데이터를 저장하기 때문에 순차 접근만 가능하다 특정 index의 데이터를 조회하기 위해서는 O(n)의 시간이 걸린다

<br>

### 삽입/삭제 (insert/delete)
#### ArrayList
- ArrayList는 마지막 원소를 추가/삭제 하면 시간복잡도가 O(1)이다.
- 하지만 마지막 원소가 아닌 중간에 있는 원소를 삽입/삭제하면 해당 원소보다 큰 인덱스의 원소들을 한 칸씩 shift 해줘야한다 따라서 이 경우에는
시간복잡도가 O(n)이 된다

<br>

#### LinkedList
- LinkedList는 어느 원소를 추가/삭제를 하더라도 노드에서 다음 주소를 가르키는 부분만 다른 주소 값으로 변경하면 되기 때문에 O(1)이다
- 하지만 추가/삭제를 하려는 index까지 도달하는데 O(n)의 시간이 걸리기 때문에, 실질적으로 추가/삭제 시에 O(n)의 시간이 걸린다

<br>

### memory
#### ArrayList
- ArrayList의 장점은 데이터 접근과 append가 빠르다
- 하지만 메모리 낭비라는 단점이 있다 선언시 크기를 지정하여 메모리 할당을 한다. 즉, 데이터가 저장되어 있지 않더라도 메모리를 차지하고 있기 때문에
메모리 낭비가 발생한다

<br>

#### LinkedList
- LinkedList는 런타임 중에서도 크기를 늘리고 줄일 수 있다. 그래서 크기를 지정할 때 고민할 필요가 없다
- LinkedList는 필요한 만큼 메모리를 사용하기 때문에 메모리 낭비가 없다

<br>

## 어느 상황에 무엇을 사용해야 할까?
### LinkedList를 사용해야 할 때
- O(1)으로 삽입/삭제를 자주 해야 될 때
- 데이터가 얼마나 들어올 지 예측할 수 없을 때
- 조회 작업을 별로 하지 않을 때

<br>

### ArrayList를 사용해야 할 때
- 조회 작업을 자주 해야될 때
- ArrayList를 선언할 당시에 데이터의 갯수를 미리 알고 있을 때
- 데이터를 반복문을 통해서 빠르게 순회할 때
- 메모리를 적게 쓰는게 중요할 때(LinkedList보다 ArrayList가 메모리를 적게 차지하기 때문에 미리 들어올 데이터의 양을
알고 있으면 ArrayList가 메모리를 더 효율적으로 사용한다)






