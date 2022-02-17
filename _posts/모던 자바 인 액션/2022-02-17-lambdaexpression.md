---
layout: post
title: Chapter 3. 람다 표현식
category: 모던 자바 인 액션
permalink: /til/:year/:month/:day/:title/
tags: [모던 자바 인 액션, JAVA]
comments: true
---



# 1. 람다란 무엇인가?

- 람다 표현식: 메서드로 전달할 수 있는 익명 함수를 단순화한 것
  - 이름은 없지만, 파라미터 리스트, 바디, 반환 형식, 발생할 수 있는 예외 리스트는 가질 수 있음



- 람다의 특징
  - **익명**: 이름이 없음
  - **함수**: 특정 클래스에 종속되지 않음
  - **전달**: 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있음
  - **간결성**: 많은 코드를 구현할 필요가 없음



- 람다 표현식 구성

  ```java
  (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
  ```

  - 파라미터 리스트
  - 화살표: 람다의 파라미터 리스트와 바디를 구분
  - 람다 바디: 람다의 반환값에 해당하는 표현식



### 람다 표현식 예제

```java
(String s) -> s.length()
```

- String 형식의 파라미터 하나를 가지며 int를 반환
- 람다 표현식에는 return이 함축되어 있으므로 return 문을 명식적으로 사용하지 않아도 됨



```java
(Apple a) -> a.getWeight() > 150
```

- Apple 형식의 파라미터 하나를 가지며 boolean을 반환



```java
(int x, int y) -> {
    System.out.println("Result:");
    System.out.println(x + y);
}
```

- int 형식의 파라미터 두 개를 가지며 리턴값이 없음 (void 리턴)
- 람다 표현식은 여러 행의 문장을 포함할 수 있음



```java
() -> 42
```

- 파라미터가 없으며 int 42를 반환



```java
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())
```

- Apple 형식의 파라미터 두 개를 가지며 int를 반환





### 람다의 기본 문법

- 표현식 스타일(expression style)

  ```ㅓㅁㅍㅁ
  (parameters) -> expression
  ```

- 블록 스타일(block-style)

  ```java
  (parameters) -> {statements;}
  ```





**람다 예제와 사용 사례**

| 사용 사례          | 람다 예제                                                    |
| ------------------ | ------------------------------------------------------------ |
| 불리언 표현식      | `(List<String> list) -> list.isEmpty()`                      |
| 객체 생성          | `() -> new Apple(10)`                                        |
| 객체에서 소비      | `(Apple a) -> {System.out.println(a.getWeight());}`          |
| 객체에서 선택/추출 | `(String s) -> s.length()`                                   |
| 두 값을 조합       | `(int a, int b) -> a * b`                                    |
| 두 객체 비교       | `(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())` |





# 2. 어디에, 어떻게 람다를 사용할까?

### 함수형 인터페이스

- 함수형 인터페이스는 **정확히 하나의 추상 메서드를 지정하는 인터페이스**를 의미

- 자바 API의 함수형 인터페이스로 Comparator, Runnable 등이 있음

  ```java
  public interface Comparator<T> {
      int compae(T o1, T o2);
  }
  public interface Runnable {
      void run();
  }
  
  public interface ActionListener extends EventListener {
      void actionPerformed(ActionEvnet e);
  }
  
  pblic interface Callable<V> {
      V call() throws Exception;
  }
  
  public interface PrivilegendActio<T> {
      T run();
  }
  ```

  - 인터페이스는 디폴트 메서드를 포함할 수 있음. 많은 디폴트 메서드가 있더라도 추상 메서드가 하나이면 함수형 인터페이스
  - 디폴트 메서드: 인터페이스의 메서드를 구현하지 않은 클래스를 고려해 기본 구현을 제공하는 바디를 포함하는 메서드





- 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있음

  ```java
  Runnable r1 = () -> System.out.println("Hello World 1");	// 람다 사용
  
  Runnable r2 = new Runnable(){	// 익명 클래스 사용
      	public void run(){
              System.out.println("Hello World 2");
          }
  };
  
  public static void process(Runnable r){
      r.run();
  }
  process(r1);
  process(r2);
  process(() -> System.out.println("Hello World 3"));	// 직접 전달된 람다 표현식
  ```





### 함수 디스크립터

- 함수형 인터페이스의 추상 메서드 시그니처(signature)는 람다 표현식의 시그니처를 가리킴

- 람다 표현식의 시그니처를 서술하는 메서드를 **함수 디스크립터(function descriptor)**라고 부름
  - Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로 Runnable 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있음



> 람다와 함수형 인터페이스를 가리키는 표기법
>
> - `() -> void`: 파라미터 리스트가 없으며 void를 반환하는 함수 (ex. Runnable)
> - `(Apple, Apple) -> int`: 두 개의 Apple을 인수로 받아 int를 반환하는 함수



- 람다 표현식은 변수에 할당하거나 함수형 인터페이스를 인수로 받는 메서드로 전달할 수 있으며, 함수형 인터페이스의 추상 메서드와 같은 시그니처를 가짐



> **`@FunctionalInterface`**
>
> - 함수형 인터페이스에 `@FunctionalInterface` 어노테이션이 추가됨
> - 함수형 인터페이스임을 가리키는 어노테이션
> - `@FunctionalInterface`로 인터페이스를 선언했지만 실제로 함수형 인터페이스가 아니면 컴파일러가 에러를 발생시킴





# 3. 람다 활용 : 실행 어라운드 패턴

- 자원 처리에 사용하는 순환 패턴(recurrent pattern)은 자원을 열고, 처리한 다음, 자원을 닫는 순서로 이루어짐
  - 설정(setup)과 정리(cleanup) 과정은 대부분 비슷
  - 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 가짐
- **실행 어라운드 패턴(excute around pattern)**: 중복되는 준비 코드와 정리코드가 작업들을 감싸고 있는 패턴



- 파일에서 한 행을 읽는 코드(try-with-resources 구문 사용)

  ```java
  public String processFile() throws IOException {
      try(BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
          return br.readLine();	// 실제 필요한 작업을 하는 행
      }
  }
  ```





### 1단계 : 동작 파라미터화를 기억하라

- 현재 코드는 파일에서 한 번에 한 줄만 읽을 수 있음. 한 번에 두 줄을 읽거나 가장 자주 사용되는 단어를 반환하려면?

  - 기존의 설정, 정리 과정은 재사용하고 processFile 메서드만 다른 동작을 수행하도록 명령
  - processFile의 동작을 파라미터화하면 됨
  - processFile 메서드가 BufferedReader를 이용해 다른 동작을 수행할 수 있도록 processFile 메서드로 동작을 전달해야 함

- processFile 메서드가 한 번에 두 행을 읽어오도록 코드 수정

  ```java
  String result = processFile((BufferedReader br) -> br.readLibe() + br.readLine());
  ```

  - BufferedReader를 인수로 받아 String을 반환하는 람다를 사용해 수정





### 2단계 : 함수형 인터페이스를 이용해서 동작 전달

- 함수형 인터페이스 자리에 람다를 사용할 수 있음

  - BufferedReader -> String과 IOException을 던질 수 있는 시그니처와 일치하는 함수형 인터페이스를 만들어야 함

  ```java
  @FunctionalInterface
  public interface BufferedReaderProcessor {
      String process(BufferedReader b) throws IOException;
  }
  ```

  

- 정의한 인터페이스를 processFile 메서드의 인수로 전달

  ```java
  public String processFile(BufferedReaderProcessor p) throws IOException {
      ...
  }
  ```

  



### 3단계 : 동작 실행

- BufferedReaderProccessor에 정의된 process 메서드의 시그니처와 일치하는 람다를 전달할 수 있음

- 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있고, 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리

- 따라서 processFile 바디 네에서 BufferedReaderProcessor 객체의 process를 호출할 수 있음

  ```java
  public String processFile(BufferedReaderProcessor p) throws IOException {
      try(BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
          return p.process(br);	// BufferedReader 객체 처리
      }
  }
  ```

  

 

### 4단계 : 람다 전달

- 람다를 이용해 다양한 동작을 processFile 메서드로 전달할 수 있음

- 한 행을 처리하는 코드

  ```java
  String onLine = processFile((BufferedReader br) -> br.readLine());
  ```

- 두 행을 처리하는 코드

  ```java
  String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
  ```





# 4. 함수형 인터페이스 사용

- 함수형 인터페이스는 오직 하나의 추상 메서드를 지정
  - 함수형 인터페이스의 추상 메서드는 람다 표현식의 시그니처를 묘사
  - 함수 디스크립터(function descriptor): 함수형 인터페이스의 추상 메서드 시그니처
- 다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요
- 자바 8에서는 `java.util.function` 패키지로 여러 가지 새로운 함수형 인터페이스를 제공





### Predicate

- `java.util.function.Predicate<T>` 인터페이스는 test라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 불리언을 반환
- T 형식의 객체를 사용하는 불리언 표현식이 필요한 상황에서 Predicate 인터페이스를 사용할 수 있음





### Consumer

- `java.util.function.Consumer<T>` 인터페이스는 제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의
- T 형식의 객체를 인수로 받아 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용할 수 있음





### Function

- `java.util.function.Function<T, R>` 인터페이스는 제네릭 형식 T를 인수로 받아 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의
- 입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있음





### 람다와 함수형 인터페이스 예제

| 사용 사례          | 람다 예제                                                    | 대응하는 함수형 인터페이스                                   |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 불리언 표현        | `(List<String> list) -> list.isEmpty()`                      | `Predicate<List<String>>`                                    |
| 객체 생성          | `() -> new Apple(10)`                                        | `Supplier<Apple>`                                            |
| 객체에서 소비      | `(Apple a) -> System.out.println(a.getWeight())`             | `Consumer<Apple>`                                            |
| 객체에서 선택/추출 | `(String s) -> s.length()`                                   | `Function<String, Integer>`,<br />`ToIntFunction<String>`    |
| 두 값 조합         | `(int a, int b) -> a * b`                                    | `IntBinaryOperator`                                          |
| 두 객체 비교       | `(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())` | `Comparator<Apple>`,<br />`BiFunction<Apple, Apple, Integer>`,<br />`ToIntBiFunction<Apple, Apple>` |



> **예외, 람다, 함수형 인터페이스의 관계**
>
> - 함수형 인터페이스는 확인된 예외를 던지는 동작을 허용하지 않음
>
>   - 예외를 던지는 람다 표현식을 만들려면 확인된 예외를 선언하는 함수형 인터페이스를 직접 정의하거나 람다를 try-catch문으로 감싸야 함
>
> - `Function<T, R>` 형식의 함수형 인터페이스를 사용하고 있으며 직접 함수형 인터페이스를 만들기 어려운 상황이라면 try-catch문을 사용해 명시적으로 확인된 예외를 잡을 수 있음
>
>   ```java
>   Function<BufferedReader, String> f = (BufferedReader b) -> {
>       try{
>           return b.readLine();
>       }
>       catch(IOException){
>           throw new RuntimeException(e);
>       }
>   };
>   ```





# 5. 형식 검사, 형식 추론, 제액

### 형식 검사

- 람다가 사용되는 콘텍스트(context)를 이용해 람다의 형식(type)을 추론할 수 있음

- **대상 형식(target type)**: 어떤 콘텍스트에서 기대되는 람다 표현식의 형식

- 형식 확인 과정

  ```java
  List<Apple> heavierThan150g = filter(inventory, (Apple apple) -> apple.getWeight() > 150);
  ```

  1. filter 메서드의 선언 확인 (`filter(List<Apple> inventory, Predicate<Apple> p)`)
  2. filter 메서드는 두 번째 파라미터로 `Predicate<Apple>` 형식 (대상 형식)을 기대
  3. `Predicate<Apple>`은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스
  4. test 메서드는 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사
  5. filter 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 함





### 같은 람다, 다른 함수형 인터페이스

- 대상 형식이라는 특징 때문에 같은 람다 표현식이더라도 호환되는 추상 메서드를 가진 다른 함수형 인터페이스로 사용될 수 있음
- 즉, 하나의 함다 표현식을 다양한 함수형 인터페이스에 사용할 수 있음





### 형식 추론

- 자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)를 이용해 람다 표현식과 관련된 함수형 인터페이스를 추론. 즉, 대상 형식을 이용해 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있음 

- 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 파라미터 형식 생략 가능

  ```java
  // 파라미터 apple에는 형식을 명시적으로 지정하지 않음
  List<Apple> greenApples = filter(inventory, apple -> GREEN.equals(apple.getColor()));
  ```

- 여러 파라미터를 포함하는 람다 표현식에서는 코드 가독성이 향상될 수 있음

  ```java
  // 형식을 추론하지 않음
  Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
  // 형식을 추론함
  Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
  ```

- 상황에 따라 명시적으로 형식을 포함하는 것이 좋을 때도 있고 형식을 배제하는 것이 가독성을 향상시킬 때도 있음





### 지역 변수 하용

- 지금까지 람다 표현식은 인수를 자신의 바디 안에서만 사용함
- 람다 표현식에서는 익명 함수가 하는 것처럼 자유 변수(free variable, 파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용할 수 있음
- **람다 캡쳐링(capturing lambda)**: 람다 표현식 안에서 자유 변수를 활용하는 것
  - 람다는 인스턴스 변수와 정적 변수를 자유롭게 캡처(자신의 바디에서 참조할 수 있도록)할 수 있음
  - 하지만 캡처하기 위해서는 지역 변수는 명시적으로 final로 선언되어 있어야 하거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 함
  - 즉, **람다 표현식은 한 번만 할당할 수 있는 지역 변수를 캡처할 수 있음**

- 이 예제는 컴파일 할 수 없음

  ```java
  int portNumber = 1337;
  Runnable r = () -> System.out.println(portNumber);
  portNumber = 31337;
  ```

  - portNumber에 값을 두 번 할당하므로 컴파일 할 수 없는 코드





**지역 변수의 제약**

- 인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 저장됨
- 람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 변수를 할당한 스레드가 사라져 변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있음
- 따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공
- 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것



> **클로저(closure)**
>
> - 원칙적으로 클로저란 함수의 비지역 변수를 자유롭게 참조할 수 있는 함수의 인스턴스를 가리킴
>   - 클로저를 다른 함수의 인수로 전다랄 수 있음
>   - 클로저는 클로저 외부에 정의된 변수의 값에 접근하고, 값을 바꿀 수 있음
> - 자바 8의 람다와 익병 클래스는 클로저와 비슷한 동작을 수행
>   - 람다와 익명 클래스 모두 메서드의 인수로 전달될 수 있으며 자신의 외부 영역의 변수에 접근할 수 있음
>   - 하지만 람다와 익명 클래스는 람다가 정의된 메서드의 지역 변수의 값은 바꿀 수 없음
>   - 람다가 정의된 메서드의 지역 변수값은 final 변수여야 하기 때문에 람다는 변수가 아닌 값에 국한되어 동작을 수행
> - 지역 변수값은 스택에 존재하므로 자신을 정의한 스레드와 생존을 같이 해야 하므로 지역 변수는 final이어야 함
>   - 가변 지역 변수를 새로운  스레드에서 캡처할 수 있다면 안전하지 않은 동작을 수행할 가능성이 생김
>   - 인스턴스 변수는 스레드가 공유하는 힙에 존재하므로 특별한 제약이 없음





# 6. 메서드 참조

### 요약

- 메서드 참조: 특정 메서드만을 호출하는 람다의 축약형
  - 명시적 메서드명을 참조함으로써 **가독성을 높일 수 있음**

- 메서드명 앞에 구분자(::)를 붙이는 방식으로 메서드 참조를 활용
  - `Apple::getWeight`: Apple 클래스에 정의된 getWeight의 메서드 참조
  - 실제로 메서드를 호출하는 것은 아니므로 괄호는 필요 없음





**메서드 참조를 만드는 방법**

- 메서드 참조의 세 가지 유형
  1. **정적 메서드 참조**
     - Integer의 parseInt 메서드는 `Integer::parseInt`로 표현할 수 있음
  2. **다양한 형식의 인스턴스 메서드 참조**
     - String의 length 메서드는 `String::length`로 표현할 수 있음
     - 람다 표현식의 파라미터로 전달할 수 있음 (`(String s) -> s.toUpperCase()` -> `String::toUpperCase()`)
  3. **기존 객체의 인스턴스 메서드 참조**
     - Transaction 객체를 할당받은 expensiveTransaction 지역 변수가 있고, Transaction 객체에는 getValue 메서드가 있다면, `expensiveTransaction::getValue`로 표현할 수 있음
     - 람다 표현식에서 현존하는 외부 객체의 메서드를 호출할 때 사용됨 (`() -> expensiveTransaction.getValue()` -> `expensiveTransaction::getValue`)
     - 비공개 헬퍼 메서드를 정의한 상황에서 유용하게 활용



- 컴파일러는 람다 표현식의 형식을 검사하던 방식과 비슷한 과정으로 메서드 참조가 주어진 함수형 인터페이스와 호환하는지 확인
  - 메서드 참조는 콘텍스트의 형식과 일치해야 함





### 생성자 참조

- 클래스명과 new 키워드를 이용해 기존 생성자의 참조를 만들 수 있음

  - 정적 메서드의 참조를 만드는 방법과 비슷

- Supplir의 `() -> Apple`과 같은 시그니처를 갖는 생성자가 있다고 가정

  ```java
  Supplier<Apple> c1 = () -> new Apple();	// 람다 표현식은 디폴트 생성자를 가진 Apple을 만듦
  Apple a1 = c1.get();	// Supplier의 get 메서드를 호출해 새로운 Apple 객체를 만듦
  
  // 위와 같은 코드
  Supplier c1 = Apple::new;
  Apple a1 = c1.get();
  ```

- `Apple(Integer weight)`라는 시그니치러를 갖는 생성자는 Function 인터페이스의 시그니처와 같음

  ```java
  Function<Integer, Apple> c2 = Apple::new;
  Apple a2 = c2.apply(110);	// apply 메서드에 무게를 인수로 호출해 새로운 Apple 객체를 만듦
  ```

- Integer를 포함하는 리스트의 각 요소를 map 같은 메서드를 이용해 Apple 생성자로 전달

  ```java
  List<Integer> weights = Arrays.asList(7, 3, 4, 10);
  List<Apple> apples = map(weights, Apple::new);	// map 메서드로 생성자 참조 전달
  
  public List<Apple> map(List<Integer> list, Function<Integer, Apple> f){
      List<Apple> result = new ArrayList<>();
      for (Integer i : list){
          result.add(f.apply(i));
      }
      return result;
  }
  ```

  - 다양한 무게를 포함하는 사과 리스트가 만들어짐

- `Apple(String color, Integer weight)`처럼 두 인수를 갖는 생성자는 BiFunction 인터페이스와 같은 시그니처를 가짐

  ```java
  BiFunction<Color, Integer, Apple> c3 = Apple::new;
  Apple a3 = c3.apply(GREEN, 110);
  ```





# 7. 람다, 메서드 참조 활용하기

### 1단계 : 코드 전달

```java
public class AppleComparator implements Comparator<Apple> {
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }
}

inventory.sort(new AppleComparator)
```

- 자바 8의 List API에서 sort 메서드를 제공하므로 정렬 메서드를 직접 구현할 필요 없음
  - sort 메서드의 시그니처: `void sort(Comparator<? super E> c)`
  - Comparator 객체를 인수로 받아 두 사과를 비교
- 객체 안에 동작을 포함시키는 방식으로 다양한 전략 전달 가능 => **sort의 동작은 파라미터화 됨**





### 2단계 : 익명 클래스 사용

```java
inventory.sort(new Comparator<Apple>(){
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }
});
```

- 한 번만 사용할 Comparator를 익명 클래스로 작성함





### 3단계 : 람다 표현식 사용

- 함수형 인터페이스를 기대하는 곳 어디든 람다 표현식 사용 가능
  - 함수형 인터페이스: 오직 하나의 추상 메서드를 정의하는 인터페이스
  - 함수 디스크립터(추상 메서드의 시그니처)는 람다 표현식의 시그니처를 정의



```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

- 자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 활용해 람다의 파라미터 형식을 추론함

  ```java
  inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
  ```



- Comparator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인수로 받는 정적 메서드 comparing을 포함

  ```java
  Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
  
  import static java.util.Comparator.comparing;
  inventory.sort(comparing(apple -> apple.getWeight()));
  ```





### 4단계 : 메서드 참조 사용

```java
import static java.util.Comparator.comparing;
inventory.sort(comparing(Apple::getWeight));
```

- '**Apple을 weight별로 비교해 inventory를 sort해라**'는 의미





# 8. 람다 표현식을 조합할 수 있는 유용한 메서드

- 간단한 여러 개의 람다 표현식을 조합해 복잡한 람다 표현식을 만들 수 있음
  - 두 프레디케이트를 조합해 두 프레디케이트의 or 연산을 수행하는 커다란 프레디케이트를 만들 수 있음
  - 한 함수의 결과가 다른 함수의 입력이 되도록 두 함수를 조합할 수 있음





### Comparator 조합

- 정적 메서드 `Comparator.comparing`을 이용해 비교에 사용할 키를 추출하는 Function 기반의 Comparator를 반환할 수 있음

  ```java
  Comparator<Apple> c = Comparator.comparing(Apple::getWeight);
  ```





**역정렬**

- 사과의 무게를 내림차순으로 정렬하고 싶다면?

  - 인터페이스 자체에서 주어진 비교자의 순서를 뒤바꾸는 reverse라는 디폴트 메서드를 제공

- 처음 비교자 구현을 그대로 재사용해 사과의 무게를 기준으로 역정렬할 수 있음

  ```java
  inventory.sort(comparing(Apple::getWeight).reversed());
  ```





**Comperator 연결**

- 무게가 같은 두 사과가 존재한다면 어떤 사과를 먼저 나열해야 할까?

  - 비교 결과를 다듬을 수 있는 두 번째 Comparator를 만들 수 있음

- 무게로 두 사과를 비교한 다음 무게가 같다면 원산지 국가별로 사과를 정렬할 수 있음

  ```java
  inventory.sort(comparing(Apple::getWeight).reversed().thenComparing(Apple::getCountry))
  ```

  - thenComparing 메서드로 두 번째 비교 연산자를 만듦
  - thenComparing은 함수를 인수로 받아 첫 번째 비교자를 이용해서 두 객체가 같다고 판단되면 두 번째 비교자에 객체를 전달





### Predicate 조합

- 복잡한 프레디케이트를 만들 수 있도록 negate, and, or 세 가지 메서드를 제공

- '빨간색이 아닌 사과'처럼 특정 프레디케이트를 반전시킬 때 negate 메서드를 사용

  ```java
  Predicate<Apple> notRedApple = redApple.negate();
  ```

- and 메서드를 이용해 빨간색이면 무거운 사과를 선택하도록 두 람다를 조합

  ```java
  Predicate<Apple> redAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150);
  ```

- or을 이용해 '빨간색이면서 무거운 사과 또는 그냥 녹색 사과' 등 다양한 조건을 만들 수 있음

  ```java
  Predicate<Apple> redAndHeavyAppleOrGreen = redApple.and(apple -> apple.getWeight > 150)
      											   .or(apple -> GREEN.equals(apple.getColor()))
  ```





### Function 조합

- Function 인스턴스를 반환하는 andThen, compose 두 가지 디폴트 메서드를 제공

- andThen 메서드: 주어진 함수를 먼저 적용한 결과를 다른 함수의 입력으로 전달하는 함수를 반환

  - 숫자를 증가시키는 f라는 함수와  숫자에 2를 곱하는 g하는 함수가 있다고 가정

  - f와 g를 조립해 숫자를 증가시킨 뒤 결과에 2를 곱하는 h라는 함수를 만들 수 있음

    ```java
    Function<Integer, Integer> f = x -> x + 1;
    Function<Integer, Integer> g = x -> x * 2;
    Function<Integer, Integer> h = f.andThen(g);
    int result = h.apply(1);	// 4를 반환
    ```

- compose 메서드: 인수로 주어진 함수를 먼저 실행한 다음 그 결과를 외부 함수의 인수로 제공

  ```java
  Function<Integer, Integer> f = x -> x + 1;
  Function<Integer, Integer> g = x -> x * 2;
  Function<Integer, Integer> h = f.compose(g);
  int result = h.apply(1);	// 3를 반환
  ```

  



# 10. 마치며

- **람다 표현식**: 익명 함수의 일종. 이름은 없지만, 파라미터 리스트, 바디, 반환 형식을 가지며 예외를 던질 수 있음
  - 람다 표현식으로 간결한 코드를 구현할 수 있음

- **함수형 인터페이스**: 하나의 추상 메서드만을 정의하는 인터페이스

  - 함수형 인터페이스를 기대하는 곳에서만 람다 표현식을 사용할 수 있음

  - 람다 표현식을 이용해 함수형 인터페이스의 추상 메서드를 즉석으로 제공할 수 있으며 람다 표현식 전체가 함수형 인터페이스의 인스턴스로 취급됨

- **`java.util.function`** 패키지는 `Predicate<T>`, `Function<T, R>`, `Supplier<T>`, `Consumer<T>`, `BinaryOperator<T>` 등을 포함해 자주 사용하는 다양한 함수형 인터페이스를 제공

- 자바 8은 `Predicate<T>`와 `Function<T, R>` 같은 제네릭 함수형 인터페이스와 관련한 박싱 동작을 피할 수 있는 IntPredicate, IntToLongFunction 등과 같은 기본형 특화 인터페이스도 제공
- 실행 어라운드 패턴(코드 중간에 실행해야 하는 메서드에 꼭 필요한 코드)을 람다를 활용하면 유연성과 재사용성을 추가로 얻을 수 있음
- 람다 표현식의 기대 형식(type expected)을 **대상 형식(target type)**이라고 함
- 메서드 참조를 이용하면 기존의 메서드 구현을 재사용하고 직접 전달할 수 있음
- Comparator, Predicate, Function 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있는 다양한 디폴트 메서드를 제공