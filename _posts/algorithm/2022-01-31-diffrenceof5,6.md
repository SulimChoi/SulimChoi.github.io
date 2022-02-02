---
layout: post
title: bronze 2. 5와 6의 차이 (2864)
category: 알고리즘 문제풀이
permalink: /algorithm/:year/:month/:day/:title/
tags: [알고리즘, 프로그래밍, 자바]
comments: true
---


# bronze 2. 5 (2864)

> 출처 : https://www.acmicpc.net/problem/2864



## 문제

숫자 5를 볼 때, 5로 볼 때도 있지만, 6으로 잘못 볼 수도 있고, 6을 볼 때는, 6으로 볼 때도 있지만, 5로 잘못 볼 수도 있다.

두 수 A와 B가 주어졌을 때, 두 수의 가능한 합 중, 최솟값과 최댓값을 구해 출력하는 프로그램을 작성




입력: 두 정수 A와 B가 주어짐 (1 <= A, N <= 1,000,000)

출력: 두 수의 합 중 최솟값과 최댓값을 출력





## 풀이

- 아이디어: 최솟값을 구하는 함수와 최댓값을 구하는 함수 작성
  - 최솟값을 구하는 함수: 주어진 수에 6이 들어있는 경우 6을 5로 바꿔준 후, 두 수의 합을 return
  - 최댓값을 구하는 함수: 주어진 수에 5가 들어있는 경우 5를 6으로 바꿔준 후, 두 수의 합을 return
  


```java
import java.util.*;

public class Main{
  public static void main(String[] args){
    Scanner sc = new Scanner(System.in);
    int min = 0;
    int max = 0;

    StringTokenizer st = new StringTokenizer(sc.nextLine());
    
    // 두 수 입력 받음
    String num1 = new String(st.nextToken());
    String num2 = new String(st.nextToken());

    min = min(num1, num2);	// 최솟값 구하기
    max = max(num1, num2);	// 최댓값 구하기

    System.out.printf("%d %d%n", min, max);

  }

  // 두 수의 합 중에서 최솟값을 구하는 함수
  public static int min(String num1, String num2){
    int result = 0;
	
    // 주어진 수들 중에서 6이 포함되어 있는 경우, 6을 5로 변경
    if(num1.contains("6")){
      num1 = num1.replace("6", "5");
    }

    if(num2.contains("6")){
      num2 = num2.replace("6", "5");
    }
    
	// String형인 두 변수를 Int형으로 변경 후 더해줌
    result = Integer.parseInt(num1) + Integer.parseInt(num2);

    return result;	// 최솟값 return
  }

  // 두 수의 합 중에서 최댓값을 구하는 함수
  public static int max(String num1, String num2){
    int result = 0;

    // 주어진 수들 중에서 5가 포함되어 있는 경우, 5를 6으로 변경
    if(num1.contains("5")){
      num1 = num1.replace("5", "6");
    }

    if(num2.contains("5")){
      num2 = num2.replace("5", "6");
    }

    // String형인 두 변수를 Int형으로 변경 후 더해줌
    result = Integer.parseInt(num1) + Integer.parseInt(num2);

    return result;	// 최댓값 return
  }
}
```
