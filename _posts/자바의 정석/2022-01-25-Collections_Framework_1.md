---
layout: post
title: Chapter 11. 컬렉션 프레임웍_1
category: 자바의 정석
permalink: /til/:year/:month/:day/:title/
tags: [자바의 정석, JAVA]
comments: true
---



# 1. 컬렉션 프레임웍의 핵심 인터페이스

- 컬렉션 프레임웍(Collections Framework): 데이터 군을 저장하는 클래스들을 표준화한 설계
  - 컬렉션은 다수의 데이터, 즉 데이터 그룹을, 프레임웍은 표준화된 프로그래밍 방식을 의미
  - 컬렉션 프레임웍은 컬렉션(다수의 데이터)을 다루는데 필요한 다양하고 풍부한 클래스들을 제공
  - 인터페이스와 다형성을 이용한 객체지향적 설계를 통해 표준화되어 있기 때문에 사용법을 익히기에도 편리하고 재사용성이 높은 코드를 작성할 수 있음

- 컬렉션데이터 그룹을 3가지 타입(List, Set, Map)이 존재한다고 인식하고 각 컬렉션을 다루는데 필요한 기능을 가진 3개의 인터페이스를 정의
  - 인터페이스 List와 Set의 공통된 부분을 다시 뽑아 새로운 인터페이스인 Collection을 추가로 정의

| 인터페이스 | 특징                                                         |
| :--------: | ------------------------------------------------------------ |
|    List    | - 순서가 있는 데이터의 집합. 데이터 중복을 허용<br />- 구현 클래스: ArrayList, LinkedList, Stack, Vector 등 |
|    Set     | -  순서를 유지하지 않는 데이터의 집합. 데이터의 중복을 허용하지 않음<br />- 구현 클래스: HashSet, TreeSet 등 |
|    Map     | - 키와 값의 쌍으로 이루어진 데이터의 집합. 순서는 유지되지 않으며, 키는 중복을 허용하지 않고, 값은 중복을 허용<br />- 구현 클래스: HashMap, TreeMap, Hashtable, Properties 등 |

- Vector, Stack, HashTable, Proerties와 같은 클래스들은 컬렉션 프레임웍이 만들어지기 이전부터 존재
  - Vector, Hashtable과 같은 기존 컬렉션의 클래스들은 호환을 위해 설계를 변경해 남겨두었지만 가능하면 사용하지 않는 것이 좋음
  - ArrayList, HashMap을 사용



**Collection 인터페이스**

- [Collection 인터페이스에 정의된 메서드](https://docs.oracle.com/javase/8/docs/api/)
- Collection 인터페이스는 컬렉션 클래스에 저장된 데이터를 읽고, 추가하고 삭제하는 등 컬렉션을 다루는데 가장 기본적인 메서드들을 정의



**List 인터페이스**

- [List 인터페이스에 정의된 메서드](https://docs.oracle.com/javase/8/docs/api/)
- 중복을 허용하면서 저장순서가 유지되는 컬렉션을 구현하는데 사용됨



**Set 인터페이스**

- [Set 인터페이스에 정의된 메서드](https://docs.oracle.com/javase/8/docs/api/)
- 중복을 허용하지 않고 저장순서가 유지되지 않는 컬렉션 클래스를 구현하는데 사용



**Map 인터페이스**

- [Map 인터페이스에 정의된 메서드](https://docs.oracle.com/javase/8/docs/api/)

- 키(key)와 값(value)을 하나의 쌍으로 묶어서 저장하는 컬렉션 클래스를 구현하는 데 사용됨
- 키가 중복될 수 없지만 값은 중복을 허용
  - 기존에 저장된 데이터와 중복된 키와 값을 저장하면 기존의 값은 없어지고 마지막에 저장된 값이 남게 됨
- `values()`에서는 반환타입이 Collection이고, `keySet()`에서는 반환타입이 Set
  - Map 인터페이스에서 값은 중복을 허용하기 때문에 Collection 타입으로 반환하고, 키는 중복을 허용하지 않기 때문에 Set 타입으로 반환



**Map.Entry 인터페이스**

- [Map.Entry 인터페이스의 메서드](https://docs.oracle.com/javase/8/docs/api/)

- Map 인터페이스의 내부 인터페이스
- 내부 클래스와 같이 인터페이스도 인터페이스 안에 인터페이스를 정의하는 내부 인터페이스를 정의하는 것이 가능
- Map에 저장되는 key-value 쌍을 다루기 위해 내부적으로 Entry 인터페이스를 정의해놓음
- Map 인터페이스를 구현하는 클래스에서는 Map.Entry 인터페이스도 함께 구현해야 함



# 2. ArrayList 클래스와 LinkedList 클래스

### ArrayList

- [ArrayList의 생성자와 메서드](https://docs.oracle.com/javase/8/docs/api/)

- 컬렉션 프레임웍에서 가장 많이 사용되는 컬렉션 클래스

- List 인터페이스를 구현하기 때문에 데이터의 저장순서가 유지되고 중복을 허용함

- Object 배열을 이용해 데이터를 순차적으로 저장

  - 더 이상 저장할 공간이 없으면 더 큰 배열을 생성해 기존의 배열에 저장된 내용을 새로운 배열로 복사한 다음 저장하게 됨

- elementData라는 이름의 Object 배열을 멤버변수로 선언

  - 배열의 타입이 Object로 선언되었기 때문에 모든 종류의 객체를 담을 수 있음

- ex) list2에서 list1과 공통되는 요소들을 찾아 삭제하는 코드

  ```java
  for (int i = list2.size()-1; i >= 0; i--){
      if (list1.contains(list2.get(i)))
          list2.remove(i)
  }
  ```

  - for문의 변수 i를 0부터 증가시키지 않고 `list2.size()-1` 부터 감소시킨 이유
    - i를 증가시켜가면서 삭제하면, 한 요소가 삭제될 때마다 빈 공간을 채우기 위해 나머지 요소들이 자리이동을 하기 때문에 올바른 결과를 얻을 수 없기 때문
    - 제어변수를 감소시켜가면서 삭제해야 자리이동이 발생해도 영향을 받지 않고 작업이 가능

- ArrayList나 Vector 같이 배열을 이용한 자료구조는 데이터를 읽어오고 저장하는데 효율이 좋지만, 용량을 변경해야할 때는 새로운 배열을 생성한 후 기존의 배열로부터 새로 생성된 배열로 데이터를 복사해야하기 때문에 효율이 떨어진다는 단점이 있음
  - 처음 인스턴스를 생성할 때, 저장할 데이터의 개수를 고려해 충분한 용량의 인스턴스를 생성하는 것이 좋음



### LinkedList

> **배열의 장점과 단점**
>
> - 장점: 구조가 간단하며 사용하기 쉽고 데이터를 읽어오는데 걸리는 시간이 가장 빠름
> - 단점
>   - **크기 변경이 불가**
>     - 크기를 변경할 수 없으므로 새로운 배열을 생성해 데이터를 복사해야 함
>     - 실행속도를 향상시키기 위해서는 충분히 큰 크기의 배열을 생성해야 하므로 메모리가 낭비됨
>   - **비순차적인 데이터의 추가 또는 삭제에 시간이 오래 걸림**
>     - 차례대로 데이터를 추가하고 마지막에서부터 데이터를 삭제하는 것은 빠름
>     - 배열의 중간에 데이터를 추가하려면, 빈자리를 만들기 위해 다른 데이터들을 복사해서 이동해야 함



- LinkedList는 불연속적으로 존재하는 데이터를 서로 연결한 형태로 구성되어 있음

```java
class Node {
	Node next;	// 다음 요소의 주소를 저장
    Object obj;	// 데이터를 저장
}
```

- 링크드 리스트의 각 요소(node)들은 자신과 연결된 다음 요소에 대한 참조(주소값)와 데이터로 구성되어 있음



>  **링크드 리스트에서의 데이터 삭제**
>
> - 삭제하고자 하는 요소의 이전요소가 삭제하고자 하는 요소의 다음 요소를 참조하도록 변경하면 됨
> - 데이터를 이동하기 위해 복사하는 과정이 없기 때문에 처리 속도가 매우 빠름



> **링크드 리스트에서의 데이터 추가**
>
> - 새로운 요소를 생성한 다음 추가하고자 하는 위치의 이전 요소의 참조를 새로운 요소에 대한 참조로 변경해주고, 새로운 요소가 그 다음 요소를 참조하도록 변경



- **이중 연결리스트(doublely linked list)**: 링크드 리스트는 이동방향이 단방향이기 때문에 다음 요소에 대한 접근은 쉽지만 이전 요소에 대한 접근은 어려움. 이 점을 보완한 것이 이중 연결리스트

  ```java
  class Node {
      Node next;		// 다음 요소의 주소를 저장
      Node previous;	// 이전 요소의 주소를 저장
      Object obj;		// 데이터를 저장
  }
  ```

  - 링크드 리스트에 참조변수를 하나 더 추가해 다음 요소에 대한 참조와 이전 요소에 대한 참조가 가능하도록 함
  - 링크드 리스트보다 각 요소에 대한 접근과 이동이 쉽기 때문에 링크드 리스트보다 더 많이 사용됨



- **이중 원형 연결리스트(doubly circular linked list)**: 이중 연결리스트의 접근성을 향상시킨 연결 리스트
  - 이중 연결리스트의 첫 번째 요소와 마지막 요소를 서로 연결시킨 것
  - 마지막 요소의 다음 요소가 첫 번째 요소가 되고, 첫 번째 요소의 이전 요소가 마지막 요소가 됨



**LinkedList 클래스**

- [LinkedList의 생성자와 메서드](https://docs.oracle.com/javase/8/docs/api/)

- 링크드 리스트의 낮은 접근성을 높이기 위해 이중 연결리스트로 구현



> **ArrayList와 LinkedList 비교**
>
> 1. 순차적으로 추가/삭제하는 경우에는 ArrayList가 LinkedList보다 빠름
>    - 단, ArrayList의 크기가 충분하지 않으면, 새로운 크기의 ArrayList를 생성하고 데이터를 복사하는 일이 발생하게 되므로 순차적으로 데이터를 추가해도 LinkedList가 더 빠를 수 있음
> 2. 중간 데이터를 추가/삭제하는 경우에는 LinkedLiast가 ArrayList보다 빠름
>    - LinkedList는 각 요소간의 연결만 변경해주면 되기 때문에 처리속도가 빠르지만, ArrayList는 각 요소들을 재배치하여 추가할 공간을 확보하거나 빈 공간을 채워야하기 때문에 처리 속도가 느림
> 3. 데이터를 읽어오는데 걸리는 시간(접근 시간)은 ArrayList가 LinkedList보다 빠름



- **다루고자 하는 데이터의 개수가 변하지 않는 경우라면 ArrayList를, 데이터의 변경이 잦다면 LinkedList를 사용하는 것이 좋음**

  - 처음에 작업학기 전 데이터를 저장할 때는 ArrayList를 사용한 다음, 작업할 때는 LinkedList로 데이터를 옮겨 작업하면 좋은 효율을 얻을 수 있을 것

  ```java
  ArrayList al = new ArrayList(1000000);
  for (int i=0; i<100000; i++)	al.add(i+"");
  
  LinkedList ll = new LinkedList(al);
  for (int i=0; i<1000; i++)	ll.add(500, "X");
  ```

  - 컬렉션 프레이웍에 속한 대부분의 컬렉션 클래스들은 서로 변환 가능한 생성자를 제공하므로 간단하게 다른 컬렉션 클래스로 데이터를 옮길 수 있음



# 3. Stack 인터페이스와 Queue 인터페이스

- [Stack의 메서드](https://docs.oracle.com/javase/8/docs/api/)와 [Queue의 메서드](https://docs.oracle.com/javase/8/docs/api/)
- 스택은 마지막에 저장한 데이터를 가장 먼저 꺼내게 되는 LIFO 구조로 되어 있고, 큐는 처음에 저장한 데이터를 가장 먼저 꺼내게 되는 FIFO 구조로 되어 있음
- 순차적으로 데이터를 추가하고 삭제하는 스택에는 ArrayList와 같은 배열 기반의 컬렉션 클래스가 적합
- 큐는 데이터를 꺼낼 때 항상 첫 번째 저장된 데이터를 삭제하므로 LinkedList로 구현하는 것이 더 적합
  - ArrayList와 같은 배열 기반 컬렉션 클래스로 구현할 경우 데이터를 꺼낼 때마다 빈 공간을 채우기 위해 데이터의 복사가 발생하므로 비효율적
  - Queue의 인테페이스는 LinkedList에서 구현



> **스택과 큐의 활용**
>
> - 스택의 활용 예 - 수식계산, 수식괄호검사, 워드프로세서의 undo/redo, 웹브라우저의 뒤로/앞으로
> - 큐의 활용 예 - 최근사용문서, 인쇄작업 대기목록, 버퍼(buffer)



### PriorityQueue

- 저장한 순서에 관계없이 우선순위(priority)가 높은 것부터 꺼내게 됨
  - 숫자의 경우, 숫자가 작을수록 높은 우선순위를 가짐
  - 객체도 저장 가능하지만 각 객체의 크기를 비교할 수 있는 방법을 제공해야 함
- null 값 저장 불가. null를 저장하면 NullPointerException이 발생함
- PriorityQueue는 저장공간으로 배열을 사용하며, 각 요소를 '힙(heap)'이라는 자료구조의 형태로 저장함
  - 힙(heap): 이진 트리의 한 종류로 가장 큰 값이나 가장 작은 값을 빠르게 찾을 수 있음



### Deque(Double-Ended Queue)

- 한 쪽 끝으로만 추가/삭제할 수 있는 Queue와 달리, Deque(덱 또는 디큐)는 양쪽 끝에 추가/삭제가 가능
- Deque의 조상은 Queue이며, 구현체로는 ArrayDeque과 LinkedList등이 있음
- 덱은 스택과 큐를 하나로 합쳐놓은 것과 같으며 스택으로 사용할 수도 있고, 큐로 사용할 수도 있음

- 덱의 메서드에 대응하는 큐와 스택의 메서드

  |    Deque    |  Queue  | Stack  |
  | :---------: | :-----: | :----: |
  | offerLast() | offer() | push() |
  | pollLast()  |    -    | pop()  |
  | pollFirst() | poll()  |   -    |
  | peekFirst() | peek()  |        |
  | peekLast()  |    -    | peek() |



# 4. Iterator, ListIterator, Enumeration

- 컬렉션에 저장된 요소를 접근하는데 사용되는 인터페이스
- Enumeration은 Iterator의 구버전이며, ListIterator는 Iterator의 기능을 향상시킨 것



### Iterator

- [Iterator 인터페이스의 메서드](https://docs.oracle.com/javase/8/docs/api/)

- 컬렉션 프레임웍에서는 컬렉션에 저장된 요소들을 읽어오는 방법을 표준화함
- 컬렉션에 저장된 각 요소에 접근하는 기능을 가진 Iterator 인터페이스를 정의하고, Collection 인터페이스에는 'Iterator(Iterator를 구현한 클래스의 인스턴스)'를 반환하는 `iterator()`를 정의
  - `iterator()`는 Collection 인터페이스에 정의된 메서드이므로 Collection 인터페이스의 자손인 List와 Set도 포함되어 있음
  - List나 Set 인터페이스를 구현하는 컬렉션은 `iterator()`가 각 컬렉션의 특징에 맞게 작성되어 있음
  - 컬렉션 클래스에 대해 `iterator()`를 호출하여 Iterator를 얻은 다음 반복문(주로 while문)을 사용해서 컬렉션 클래스의 요소들을 읽어올 수 있음
- Map 인터페이스를 구현한 컬렉션 클래스는 키와 값을 쌍으로 저장하고 있기 때문에 `iteratior()`를 직접 호출할 수 없고, 그 대신 `keySet()`이나 `entrySet()`과 같은 메서드를 통해 키와 값을 각각 따로 Set의 형태로 얻어 온 후 다시 `iterator()`를 호출해야 Iterator를 얻을 수 있음



### ListIterator와 Enumeration

- Enumeration: 컬렉션 프레임웍이 만들어지기 이전에 사용하던 것으로 Iterator의 구버전
  - [Enumeration 인터페이스의 메서드](https://docs.oracle.com/javase/8/docs/api/)
  - 이전 버전으로 작성된 소스와의 호환을 위해 남겨 두고 있을 뿐이므로 가능하면 Enumeration 대신 Iterator를 사용하는 것이 좋음
- ListIterator: Iterator를 상속받아 기능을 추가한 것
  - [ListIterator의 메서드](https://docs.oracle.com/javase/8/docs/api/)
  - 컬렉션의 요소에 접근할 때 Iterator는 단방향으로만 이동할 수 있는 반면, ListIterator는 양방향으로 이동 가능
  - ArrayList나 LinkedList와 같이 List 인터페이스를 구현한 컬렉션에서만 사용 가능



# 5. Arrays

- [Arrays에 정의된 메서드](https://docs.oracle.com/javase/8/docs/api/)
  - Arrays에 정의된 메서드는 모두 static 메서드
- 배열을 다루는데 유용한 메서드가 정의되어 있음



**배열의 복사 - copyOf(), copyOfRange()**

- `copyOf()`는 배열 전체를, `copyOfRange()`는 배열의 일부를 복사해 새로운 배열을 만들어 반환



**배열 채우기 - fill(), setAll()**

- `fill()`은 배열의 모든 요소를 지정된 값으로 채우고, `setAll()`은 배열을 채우는데 사용할 함수형 인터페이스를 매개변수로 받음
- `setAll()` 를 호출할 때는 함수형 인터페이스를 구현한 객체를 매개변수로 지정하던가 아니면 람다식을 지정해야함



**배열의 정렬과 검색 - sort(), binarySearch()**

- `sort()`는 배열을 정렬할 때, `binarySearch()`는 배열에 저장된 요소를 검색할 때 사용
- `binarySearch()`는 배열에서 지정된 값이 저장된 위치(index)를 찾아서 반환하는데, 반드시 **배열이 정렬된 상태**여야 올바른 결과를 얻음
  - 검색한 값과 일치하는 요소들이 여러 개 있다면, 이 중 어떤 것의 위치가 반환될지는 알 수 없음



**배열의 비교와 출력 - equals(), toString()**

- `toString()`은 배열의 모든 요소를 문자열로 출력
  - 일차원 배열에만 사용할 수 있으므로, 다차원 배열에는 `deepToSting()`를 사용해야 함
- `equals()`는 두 배열에 저장된 모든 요소를 비교해서 같으면 true, 다르면 false를 반환
  - 일차원 배열만 사용 가능하므로, 다차원 배열에서는 `deepEquals()`를 사용해야 함
  - 다차원 배열에서 `equals()`로 값을 비교하게 되면 문자열을 비교하는 것이 아니라 **배열에 저장된 배열의 주소**를 비교하게 되기 때문에 항상 false를 결과로 얻음



**배열을 List로 변환 - asList(Object... a)**

- `asList()`는 배열을 List에 담아서 반환
- 매개변수의 타입이 가변인수라 배열 생성 없이 저장할 요소들만 나열하는 것도 가능
- `asList()`가 반환한 List의 크기는 변경 불가. **추가 또는 삭제가 불가능하지만 저장된 내용은 변경 가능**



**parallelXXX(), spliterator(), stream()**

- 빠른 결과를 얻기 위해 여러 쓰레드가 작업을 나눠어 처리
- `spliterator()`는 여러 쓰레드가 처리할 수 있게 하나의 작업을 여러 작업으로 나누는 Spliterator를 반환하며, `stream()`은 컬렉션을 스트림으로 변환함



# 6. Comparator와 Comparable

- Comparator와 Comparable은 모두 인터페이스로 컬렉션을 정렬하는데 필요한 메서드를 정의
- Comparable을 구현하고 있는 클래스들은 같은 타입의 인스턴스끼리 서로 비교할 수 있는 클래스들이며 기본적으로 오름차순으로 정렬되도록 구현되어 있음
  - [Comparable를 구현한 클래스 목록](https://docs.oracle.com/javase/8/docs/api/)
  - 주로 Integer와 같은 wrapper 클래스와 String, Date, File과 같은 클래스들을 비교
  - **Comparable을 구현한 클래스는 정렬이 가능하다는 것을 의미**
- Comparable을 구현한 클래스들이 기본적으로 오름차순으로 정렬되어 있지만, 내림차순으로 정렬한다던가 아니면 다른 기준에 의해 정렬되도록 하고 싶을 때 Comparator를 구현해서 정렬기준을 제공할 수 있음



> **Comparable**	기본 정렬기준을 구현하는데 사용
>
> **Comparator**	기본 정렬기준 외에 다른 기준으로 정렬하고자할 때 사용