---
layout: post
title: bronze 1. 설탕 배달(2839)
category: 알고리즘 문제풀이
permalink: /algorithm/:year/:month/:day/:title/
tags: [알고리즘, 프로그래밍, 자바]
comments: true
---
# bronze 1. 설탕 배달 (2839)
> 출처 : https://www.acmicpc.net/problem/2839

## 문제
설탕을 정확하게 N 킬로그램을 배달해야 함. 봉지는 3 킬로그램, 5 킬로그램이 있음. 최대한 적은 봉지로 배달하려고 할 때 봉지 몇 개를 가져가면 되는지 그 수를 구하는 프로그램을 작성 

입력: 배달해야할 설탕 무게

출력: 들고가야할 봉지 수

## 풀이

- 아이디어: 5 킬로그램 봉지를 최대한 많이 들고가는 것이 효율적
  - 주어진 무게가 5의 배수일 경우 5로 나눈 몫만큼의 봉지만 들고가면됨
  - 5의 배수가 아닐 경우 5의 배수가 될 때까지 무게에서 3을 빼준 후 봉지 수를 3를 빼준 횟수만큼 증가시킴
  - 3을 뺀 후 남은 무게가 0보다 작을 경우 `-1`를 출력하고 프로그램을 종료 (정확하게 N 킬로그램을 만들 수 없는 경우)


```java
import java.util.*;

public class Main {
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        
        int num = sc.nextInt();
        int count = 0;

        while(true) {
          // 무게가 5의 배수인 경우
          if (num % 5 == 0){
            count += num / 5;
            System.out.println(count);
            break;
          }
          // 무게가 5의 배수가 아닌 경우
          else {
            num -= 3;
            count ++;
          }

          // 정확하게 N 킬로그램을 만들 수 없는 경우
          if (num < 0){
            System.out.println(-1);
            break;
          }
        } 
    }
}
```
