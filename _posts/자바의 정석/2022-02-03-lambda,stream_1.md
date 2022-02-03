---
layout: post
title: Chapter 14. 람다와 스트림_1(람다)
category: 자바의 정석
permalink: /til/:year/:month/:day/:title/
tags: [자바의 정석, JAVA]
comments: true
---



# 1. 람다식(Lambda expression)

### 1.1 람다식이란?

- 람다식(Lambda expression): **메서드를 하나의 식(expression)으로 표현한 것**
  - 함수를 간략하면서도 명확한 식으로 표현할 수 있게 해줌
- 메서드를 람다식으로 표현하면 메서드의 이름과 반환값이 없어지므로, 람다식을 '**익명 함수(anonymous function)**'이라고도 함

```java
int[] arr = new int[5];
Arrays.setAll(arr, (i) -> (int)(Math.random()*5)+1);

// 람다식이 하는 일을 메서드로 표현
int method() {
    return (int)(Math.random()*5) + 1;
}
```

- 모든 메서드는 클래스에 포함되어야 하므로 클래스도 새로 만들어야 하고, 객체도 생성해야만 메서드를 호출할 수 있지만, 람다식은 람다식 자체만으로도 메서드의 역할을 대신할 수 있음
- 람다식은 메서드의 매개변수로 전달되어지는 것이 가능하고, 메서드의 결과로 반환될 수도 있음





### 1.2 람다식 작성하기

- 메서드에서 이름과 반환타입을 젲거하고 매개변수 선언부와 몸통 {} 사이에 '->'를 추가

  ```java
  (매개변수 선언) -> {
      문장들
  }
  ```

- 반환값이 있는 메서드의 경우, return문 대신 '식(expression)'으로 대신할 수 있음

  ```java
  (int a, int b) -> a > b ? a : b
  ```

  - 식의 연산결과가 자동적으로 반환값이 됨
  - 문장(statement)이 아닌 식이므로 끝에 ';'을 붙이지 않음

- 람다식에 선언된 매개변수의 타입은 추론이 가능한 경우 생략이 가능한데, 대부분의 경우에 생략이 가능함

  ```java
  (a, b) -> a > b ? a : b
  ```

  - 두 매개변수 중 어느 하나의 타입만 생략하는 것은 허용되지 않음

- 선언된 매개변수가 하나뿐인 경우 괄호 ()를 생략할 수 있음. 단, 매개변수의 타입이 있으면 괄호 () 생략 불가

  ```java
  (a) -> a* a
  a -> a*a	// 위의 식과 같은 의미
  ```

- 괄호 {} 안의 문장이 하나일 때는 괄호 {} 생략 가능

  ```java
  (String name, int i) -> {
      System.out.println(name+"="+i);
  }
  
  // 괄호 생략
  (String name, int i) -> 
      System.out.println(name+"="+i)
  ```

  - 문장의 끝에 ';'를 붙이지 않아야 한다는 것에 주의





### 1.3 함수형 인터페이스(Functional Interface)

- 람다식은 익명 클래스의 객체와 동등

- 람다식으로 정의된 익명 객체의 메서드를 호출하기 위해서는 참조변수가 필요

  ```java
  타입 f = (int a, int b) -> a > b ? a: b;
  ```

  - 익명 객체의 주소를 f라는 참조변수에 저장
  - 이 때, 참조변수 f의 타입은 클래스 또는 인터페이스가 가능
  - 람다식과 동등한 메서드가 정의되어 있어야 함. 그래야만 참조변수로 익명 객체(람다식)의 메서드를 호출할 수 있기 때문





```java
interface Myfunction {
    public abstract int max(int a, int b);
}

MyFunction f = new MyFunction() {
    					public int max(int a, int b) {
                            return a > b ? a : b;
                        }
				};
int big = f.max(5, 3);

// 람다식 사용
MyFunction f = (int a, int b) -> a> b ? a: b;	// 익명 객체를 람다식으로 대체
int big = f.max(5, 3)	// 익명 객체의 메서드 호출
```

- MyFunction 인터페이스를 구현한 익명 객체를 람다식으로 대체 가능한 이유
  - 람다식도 실제로는 익명 객체
  - MyFunction 인터페이스를 구현한 익명 객체의 메서드 max()와 람다식의 매개변수의 타입과 개수 그리고 반환값이 일치하기 때문





* **함수형 인터페이스(functional interface)**: 람다식을 다루기 위한 인터페이스

  ```java
  @FunctionalInterface
  interface MyFunction {	// 함수형 인터페이스 MyFunction을 정의
      public abstract int max(int a, int b)
  }
  ```

  - 람다식과 인터페이스의 메서드가 1:1로 연결되기 위해, 함수형 인터페이스에는 **오직 하나의 추상 메서드**만 정의되어 있어야 한다는 제약이 있음
  - 반면에 static 메서드와 default 메서드의 개수에는 제약이 없음





**함수형 인터페이스 타입의 매개변수와 반환타입**

- 매서드의 매개변수가 MyFunction타입이면, 이 메서드를 호출할 때 람다식을 참조하는 참조변수를 매개변수로 지정해야한다는 의미

- 참조변수 없이 람다식을 직접 매개변수로 지정하는 것도 가능

  ```java
  aMethod(() -> System.out.println("myMethod()"));
  ```

- 메서드의 반환타입이 함수형 인터페이스타입이라면, 이 함수형 인터페이스의 추상메서드와 동등한 람다식을 가리키는 참조변수를 반환하거나 람다식을 직접 반환할 수 있음

- 람다식을 참조변수로 다룰 수 있다는 것은 메서드를 통해 람다식을 주고받을 수 있다는 것을 의미

  - 변수처럼 메서드를 주고받는 것이 가능해짐





**람다식의 타입과 형변환**

- 함수형 인터페이스로 람다식을 참조할 수 있는 것일 뿐, 람다식의 타입이 함수형 인터페이스 타입과 일치하는 것은 아님
- 람다식은 익명 객체이고 익명 객체는 타입이 없음. 따라서 대입 연산자의 양변의 타입을 일치시키기 위해서는 형변환이 필요
  - 정확히는 타입이 있지만, 컴파일러가 임의로 이름을 정하기 때문에 알 수 없는 것

- 람다식은 함수형 인터페이스로만 형변환이 가능
  - Object 타입으로도 형변환 불가





### 1.4 java.util.function 패키지

- 일반적으로 자주 쓰이는 형식의 메서드를 함수형 인터페이스로 미리 정의해 놓음

  ![functionalinterface](/assets/post-img/java/functionalinterface.png)

  - 매개변수와 반환값의 유무에 따라 4개의 함수형 인터페이스가 정의되어 있음
  - Function의 변형으로 Predicate가 있음. 반환값이 boolean이라는 것만 제외하면 Function과 동일





**조건식의 표현에 사용되는 Predicate**

- 조건식을 람다식으로 표현하는데 사용됨

  ```java
  Predicate<String> isEmptyStr = s -> s.length() == 0;
  String s = "";
  
  if (isEmptyStr.test(s))
      System.out.println("This is an empty String.");
  ```





**매개변수가 두 개인 함수형 인터페이스**

- 매개변수 개수가 2개인 함수형 인터페이스는 이름 앞에 접두사 'Bi'가 붙음

  ![functionalinterface2](/assets/post-img/java/functionalinterface2.png)





**UnaryOperator와 BinaryOperator**

- 매개변수의 타입과 반환타입의 타입이 모두 일치한다는 점만 제외하면 Function과 같음

  ![functionalinterface3](/assets/post-img/java/functionalinterface3.png)





**컬렉션 프레임웍과 함수형 인터페이스**

- 컬렉션 프레임웍의 인터페이스에 다수의 디폴트 메서드가 추가되었는데, 그 중 일부는 함수형 인터페이스를 사용

  ![functionalinterface4](/assets/post-img/java/functionalinterface4.png)

  - Map 인터페이스에 있는 'comput'로 시작하는 메서드들은 맵의 value를 변환하는 일을 하고 merge()는 Map을 병합하는 일을 함





**기본형을 사용하는 함수형 인터페이스**

- 지금까지 살펴본 함수형 인터페이스는 매개변수와 반환값의 타입이 모두 지네릭 타입
- 기본형 타입의 값을 처리할 때는 래퍼 클래스를 사용해왔는데, 기본형 대신 래퍼클래스를 사용하는 것은 비효율적. 따라서 효율적으로 처리하기 위해 기본형을 사용하는 함수형 인터페이스들이 제공됨



> 사진 출처: https://velog.io/@oyeon/14-78-java.util.function-%ED%8C%A8%ED%82%A4%EC%A7%80





### 1.5 Function의 합성과 Predicate의 결합

```java
// Function
default <V> Function<T,V> andThen(Function<? super R,? extends V> after)
default <V> Function<V,R> compose(Function<? super V,? extends T> before)
static <T> Function<T, T> identity()
    
// Predicate
default Predicate<T> and(Predicate<? super T> other)
default Predicate<T> or(Predicate<? super T> other)
default Predicate<T> negate()
static <T> Predicate<T> isEqual(Object targetRef)
```





**Function의 합성**

- 두 함수의 합성은 어느 함수를 먼저 적용하느냐에 따라 달라짐

  - 함수 f, g가 있을 때, `f.andThen(g)`는 함수 f를 먼저 적용하고, 그 다음에 함수 g를 적용
  - `f.compose(g)`는 반대로 g를 먼저 적용하고 f를 적용

- 문자열을 숫자로 변환하는 함수 f와 숫자를 2진 문자열로 변환하는 함수 g를 `andThen()`으로 합성해 새로운 함수 h 만들기

  ```java
  Function<String, Integer> f = (s) -> Integer.parseInt(s, 16);
  Function<Integer, String> g = (i) -> Integer.toBinaryString(i);
  Function<String, String> h = f.andThen(g);
  ```

- `identity()`는 함수를 적용하기 이전과 이후가 동일한 항등 함수가 필요할 때 사용





**Predicate의 결합**

- 여러 Predicate를 and(), or(), negate()로 연결해서 하나의 새로운 Predicate로 결합 가능

- static 메서드인 `isEqual()`은 두 대상을 비교하는 Predicate를 만들 때 사용

  ```java
  boolean result = Predicate.isEqual(str1).test(str2);
  ```

  - `isEqual()`의 매개변수로 비교대상을 하나 지정하고, 또 다른 비교대상은 test()의 매개변수로 지정





### 1.6 메서드 참조

- 람다식이 하나의 메서드만 호출하는 경우 '메서드 참조(method reference)'라는 방법으로 람다식을 간략히 할 수 있음

  ```java
  Function<String, Integer> f = (String s) -> Integer.parseInt(s);
  
  // 메서드 참조
  Function<String, Integer> f = Integer::parseInt;
  ```

  - 컴파일러는 생략된 부분을 우변의 parseInt 메서드의 선언부로부터, 혹은 좌변의 Function 인터페이스에 지정된 지네릭 타입으로부터 알아낼 수 있음

- 3가지 경우의 메서드 참조

  | 종류                          | 람다                         | 메서드 참조         |
  | ----------------------------- | ---------------------------- | ------------------- |
  | static 메서드 참조            | `(x) -> ClassName.method(x)` | `ClassName::method` |
  | 인스턴스메서드 참조           | `(obj, x) -> obj.method(x)`  | `ClassName::method` |
  | 특정 객체 인스턴스메서드 참조 | `(x) -> obj.method(x)`       | `obj::method`       |



> **하나의 메서드만 호출하는 람다식은 '클래스이름::메서드이름' 또는 '참조변수::메서드이름'으로 바꿀 수 있음**





**생성자의 메서드 참조**

- 생성자를 호출하는 람다식도 메서드 참조로 변환 가능

  ```java
  Supplier<MyClass> s = () -> new MyClass(); 	// 람다식
  Supplier<MyClass> s = MyClass::new;			// 메서드 참조
  ```

- 매개변수가 있는 생성자라면, 매개변수의 개수에 따라 알맞은 함수형 인터페이스를 사용

  ```java
  Function<Integer, MyClass> f = (i) -> new MyClass(i);	// 람다식
  Function<Integer, MyClass> f2 = MyClass::new;			// 메서드 참조
  
  BiFunction<Integer, String, MyClass> bf = (i, s) -> new MyClass(i, s);
  BiFunction<Integer, String, MyClass> bf = MyClass::new;	// 메서드 참조
  ```

- 메서드 참조는 람다식을 마치 static 변수처럼 다룰 수 있게 해줌

- 메서드 참조는 코드를 간략히 하는데 유용해서 많이 사용됨