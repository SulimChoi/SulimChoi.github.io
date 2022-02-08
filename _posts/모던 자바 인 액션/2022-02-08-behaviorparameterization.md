---
layout: post
title: Chapter 2. 동작 파라미터화 코드 전달하기
category: 모던 자바 인 액션
permalink: /til/:year/:month/:day/:title/
tags: [모던 자바 인 액션, JAVA]
comments: true
---



**동작 파라미터화**

- 동작 파라미터화(behavior parameteriztion)를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응 가능
- 동작 파라미터화: 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미
  - 이 코드 블록은 나중에 프로그램에서 호출. 즉, 코드 블록의 실행은 나중으로 미뤄짐



# 1. 변화하는 요구사항에 대응하기

- 농장 재고목록 애플리케이션에 리스트에서 녹색 사과만 필터맇아는 기능을 추가한다고 가정



### 첫 번째 시도 : 녹색 사과 필터링

- 사과 색을 정의하는 `Color num`이 존재한다고 가정

  ```java
  enum Color { RED, GREEN }
  ```

- 첫 번째 시도 결과 코드

  ```JAVA
  public static List<Apple> filterGreenApples(List<Apple> inventory) {
      List<Apple> result = new ArrayList<>();	// 사과 누적 리스트
      for(Apple apple : inventory){
          if(GREEN.equals(apple.getColor())) {
              result.add(apple);
          }
      }
      return result;
  }
  ```

- 만약 빨간 사과도 필터링하고 싶어진 경우 어떻게 할까?
  - 메서드를 복사해 새로운 메서드를 만든 후 조건만 변경해도 되지만, 거의 비슷한 코드가 반복 존재하면 그 코드를 추상화시키는 것이 좋음





### 두 번째 시도 : 색을 파라미터화

- 색을 파라미터화할 수 있도록 메서드에 파라미터를 추가하면 변화하는 요구사항에 좀 더 유연하게 대응하는 코드를 만들 수 있음

  ```java
  public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
      List<Apple> result = new ArrayList<>();	// 사과 누적 리스트
      for(Apple apple : inventory){
          if(apple.getColor().equals(color)) {
              result.add(apple);
          }
      }
      return result;
  }
  ```

- 아래 코드처럼 구현한 메서드를 호출할 수 있음

  ```java
  List<Apple> greenApples = filterApplesByColor(inventory, GREEN);
  List<Apple> redApples = filterApplesByColor(inventory, RED);
  ```

- 만약 무게를 기준으로 사과를 구분하고 싶다면 위의 메서드에서 color 파라미터를 무게를 의미하는 파라미터로 변경해주는 것도 하나의 방법
  - 각 사과에 필터링 조건을 적용하는 부분의 코드가 색 필터링 코드와 중복 됨. 소프트웨어 공학의 DRY(don't repeat yourself) 원칙을 어기는 것
  - 색과 무게를 filter라는 메서드로 합치는 방법도 있음. 어떤 기준으로 사과를 필터링할지 구분하는 또 다른 방법이 필요. 따라서 색이나 무게 중 어떤 것을 기준으로 필터링할지 가리키는 플래그를 추가할 수 있음





### 세 번째 시도 : 가능한 모든 속성으로 필터링

- 모든 속성을 메서드 파라미터로 추가

  ```java
  public static List<Apple> filterApples(List<Apple> inventory, Color color, int weight, boolean flag) {
      List<Apple> result = new ArrayList<>();	// 사과 누적 리스트
      for(Apple apple : inventory){
          if((flag && apple.getColor().equals(color) || (!flag && apple.getWeight() > weight)) {
              result.add(apple);
          }
      }
      return result;
  }
  ```

- 위 메서드 사용

  ```java
  List<Apple> greenApples = filterApplesByColor(inventory, GREEN, 0, true);
  List<Apple> redApples = filterApplesByColor(inventory, null, 150, false);
  ```

  - true, false는 무엇을 의미? 앞으로 요구사항이 바뀌었을 때도 유연하게 대응 불가

- filterApples에 어떤 기준으로 사과를 필터링할 것인지 효과적으로 전달할 수 있다면 더 좋을 것





# 2. 동작 파라미터화

- 선택 조건을 사과의 어떤 속성에 기초해 불리언 값을 반환하는 방법으로 결정할 수 있음

  - **프레디케이트**: 참 또는 거짓을 반환하는 함수

- 선택 조건을 결정하는 인터페이스를 정의

  ```java
  public interface ApplePredicate {
      boolean test (Apple apple);
  }
  ```

  - ApplePredicate는 사과 선택 전략을 캡슐화함

- 다양한 선택 조건을 대표하는 여러 버전의 ApplePredicate 정의 가능

  ```java
  public class AppleHeavyWeightPredicate implements ApplePredicate {
      public boolean test(Apple apple){
          return apple.getWeight() > 150;
      }
  }
  
  public class AppleGreenColorPredicate implements ApplePredicate {
      public boolean test(Apple apple){
          return GREEN.equals(apple.getColor());
      }
  }
  ```



- 위 조건에 따라 filter 메서드가 다르게 동작할 것
  - **전략 디자인 패턴**(strategy design pattern): 전략이라 불리는 각 알고리즘을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음 런타임에 알고리즘을 선택하는 기법 ([자세한 내용](https://en.wikipedia.org/wiki/Strategy_pattern))
  - ApplePredicate가 알고리즘 패밀리이고 이를 구현한 두 메서드가 전략





- ApplePredicate가 다양한 동작을 수행할 수 있는 방법
  - filterApples에서 ApplePredicate 객체를 받아 애플의 조건을 검사하도록 메서드를 고쳐야 함
  - **동작 파라미터화**, 즉 메서드가 다양한 동작을 받아 내부적으로 다양한 동작을 수행할 수 있음





### 네 번째 시도 : 추상적 조건으로 필터링

- ApplePredicate를 이용한 필터 메서드

  ```java
  public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
      List<Apple> result = new ArrayList<>();	// 사과 누적 리스트
      for(Apple apple : inventory){
          if(p.test(apple)) {		// 프레디케이트 객체로 사과 검사 조건을 캡슐화함
              result.add(apple);
          }
      }
      return result;
  }
  ```





- 위 예제에서 가장 중요한 구현은 test 메서드. filterApples 메서드의 새로운 동작을 정의하는 것이 test 메서드
  - 메서드는 객체만 인수로 받으므로 test 메서드를 ApplePredicate 객체로 감싸서 전달해야 함
  - test 메서드를 구현하는 객체를 이용해 불리언 표현식 등을 전달할 수 있으므로 이는 **코드를 전달**하는 것이나 마찬가지
  - 3번에서 람다를 이용해 여러 개의 ApplePredicate 클래스를 정의하지 않고도 여러 조건의 표현식을 filterApples 메서드로 전달하는 방법을 살펴볼 것





# 3. 복잡한 과정 간소화

- 현재 filterApples 메서드로 새로운 동작을 전달하려면 ApplePredicate 인터페이스를 구현하여 여러 클래스를 정의한 다음에 인스턴스화해야 함. 상당히 번거로운 작업이며 시간 낭비
- 자바는 클래스의 선언과 인스턴스화를 동시에 수행할 수 있도록 익명 클래스(anoymous class)라는 기법을 제공
  - 익명 클래스를 이용하면 코드의 양을 줄일 수 있음





### 익명 클래스

- 익명 클래스는 자바의 지역 클래스(블록 내부에 선언된 클래스)와 비슷한 개념
- 익명 클래스를 이용하면 클래스 선언과 인스턴스화를 동시에 할 수 있어, 즉석에서 필요한 구현을 만들어서 사용할 수 있음





### 다섯 번째 시도: 익명 클래스 사용

-  익명 클래스를 이용해 ApplePredicate를 구현하는 객체를 만드는 방법으로 필터링 코드를 다시 구현한 코드

  ```java
  List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
      public boolean test(Apple apple){
          return RED.equals(apple.getColor());
      }
  });
  ```

  - 익명 클래스는 여전히 많은 공간을 차지하고 많은 프로그래머가 익명 클래스의 사용에 익숙하지 않음



- 익명 클래스로 인터페이스를 구현하는 여러 클래스를 선언하는 과정을 조금 줄일 수 는 있지만 여전히 불만족
  - 코드 조각을 전달하는 과정에서 결국 객체를 만들고 명시적으로 새로운 동작을 정의하는 메서드를 구현해야 한다는 점은 변하지 않음





### 여섯 번째 시도: 람다 표현식 사용

- 자바 8의 람다 표현식을 이용해 위 예제 코드를 간단하게 재구현할 수 있음

  ```java
  List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
  ```

  - 코드가 간결해지면서 문제를 더 잘 설명하는 코드가 됨 (복잡성 문제 해결 가능)





### 일곱 번째 시도 : 리스트 형식으로 추상화

```java
public interface Predicat<T> {
    booelan test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p){	// 형식 파라미터 T 등장
    List<T> result = new ArrayList<>();
    for(T e : list){
        if(p.test(e)){
            result.add(e);
        }
    }
    return result;
}
```

- 다른 과일과 정수, 문자열 등의 리스트에 필터 메세드를 사용할 수 있음





# 5. 마치며

- 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달
- 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있음
- 코드 전달 기법을 이용하면 동작을 메서드 인수로 전달할 수 있음
- 자바 API의 많은 메서드는 정렬, 스레드 ,GUI 처리 등을 포함한 다양한 동작으로 파라미터화할 수 있음





> **Chapter 2 끝!!!**