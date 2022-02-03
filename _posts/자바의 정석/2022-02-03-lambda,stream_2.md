---
layout: post
title: Chapter 14. 람다와 스트림_2(스트림)
category: 자바의 정석
permalink: /til/:year/:month/:day/:title/
tags: [자바의 정석, JAVA]
comments: true
---



# 2. 스트림(stream)

### 2.1 스트림이란?

- **스트림(Stream)**: 데이터 소스를 추상하하고, 데이터를 다루는데 자주 사용되는 메서드들을 정의
  - 데이터 소스를 추상화하였다는 것은 데이터 소스가 무엇이던 간에 같은 방식으로 다룰 수 있게 되었다는 것과 코드의 재사용성이 높아진다는 것을 의미
- 스트림을 이용하면 배열이나 컬렉션뿐만 아니라 파일에 저장된 데이터도 모두 같은 방식으로 다룰 수 있음

- 예시

  - 문자열 배열과 같은 내용의 문자열을 저장하는 List가 있을 때

    ```java
    String[] strArr = {"aaa", "ddd", "ccc"};
    List<String> strLisst = Arrays.asList(strArr);
    ```

  - 두 데이터 소스를 기반으로 하는 스트림은

    ```java
    Stream<String> strStream1 = strList.stream();
    Stream<String> strStream2 = Arrays.stream(strArr);
    ```

  - 두 스트림으로 데이터소스의 데이터를 읽어 정렬하고 화면에 출력하는 방법

    ```java
    // 기존 방법
    Arrays.sort(strArr);
    Collections.sort(strList);
    
    for (String str : strArr)
        System.out.println(str);
    for (String str : strList)
        System.out.println(str);
    
    // 스트림 사용
    strStream1.sorted().forEach(Ststem.out::println);
    strStream2.sorted().forEach(Ststem.out::println);
    ```





**스트림은 데이터 소스를 변경하지 않음**

- 스트림은 데이터 소스로부터 데이터를 읽기만할 뿐, 데이터 소스를 변경하지 않음

- 정렬된 결과를 컬렉션이나 배열에 담아 반환할 수도 있음

  ```java
  // 정렬된 결과를 샐로운 List에 담아서 반환
  List<String> sortedList = strStream2.sorted().collect(Collectors.toLost());
  ```





**스트림은 일회용**

- Iterator처럼 일회용. Iterator로 컬렉션의 요소를 모두 읽고 나면 다시 사용할 수 없는 것처럼, 스트림도 한번 사용하면 닫혀서 다시 사용할 수 없음
- 필요하다면 스트림을 다시 생성해야 함





**스트림은 작업을 내부 반복으로 처리**

- 내부 반복은 반복문을 메서드의 내부에 숨길 수 있다는 것을 의미
- `forEach()`는 스트림에 정의된 메서드 중의 하나로 매개변수에 대입된 람다식을 데이터 소스의 모든 요소에 적용





**스트림의 연산**

- 스트림이 제공하는 다양한 연산을 이용해 복잡한 작업들을 간단히 처리할 수 있음

  - 연산(operation): 스트림에 정의된 메서드 중에서 데이터 소스를 다루는 작업을 수행하는 것

- 스트림이 제공하는 연산은 중간 연산과 최종 연산으로 분류

  > **중간 연산**	연산 결과가 스트림인 연산. 스트림에 연속해서 중간 연산할 수 있음
  >
  > **최종 연산**	연산 결과가 스트림이 아닌 연산. 스트림의 요소를 소모하므로 단 한번만 가능





**지연된 연산**

- 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않는다는 것
- 스트림에 대해 `distinct()` 나 `sort()`같은 중간 연산을 호출해도 즉각적인 연산이 수행되는 것은 아님
  - 중간 연산을 호출하는 것은 어떤 작업이 수행되어야하는지를 지정해주는 것일 뿐
  - 최종 연산이 수행되어야 스트림의 요소들이 중간 연산을 거쳐 최종 연산에서 소모됨





**Stream<Integer>와 IntStream**

- 요소의 타입이 T인 스트림은 기본적으로 Steam<T>이지만, 오토박싱&언박싱으로 인한 비효율을 줄이기 위해 데이터 소스의 요소를 기본형으로 다루는 스트림을 제공
  - IntStream, LongStream, DoubleStream이 제공됨
- Stream<Integer> 대신  IntStream을 사용하는 것이 더 효율적. IntStream은 int 타입의 값으로 작업하는데 유용한 메서드들이 포함되어 있음





**병렬 스트림**

- 스트림으로 데이터를 다룰 경우 병렬 처리가 쉬움

- 병렬 스트림은 내부적으로 fork&join 프레임웍을 이용해 자동적으로 연산을 병렬로 수행

- 스트림에 **`parallel()`**이라는 메서드를 호출해 병렬로 연산을 수행하도록 지시

  - 병렬로 처리되지 않게 하려면 `sequential()`을 호출하면 됨
  - 모든 스트림은 기본적으로 병렬 스트림이 아니므로 `sequential()` 을 호출할 필요가 없음

  - `sequential()`은 `parallel()`을 호출한 것을 취소할 때만 사용





### 2.2 스트림 만들기

