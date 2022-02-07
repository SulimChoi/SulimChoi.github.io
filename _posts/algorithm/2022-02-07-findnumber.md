---
layout: post
title: silver 4. 수 찾기 (1920)
category: 알고리즘 문제풀이
permalink: /algorithm/:year/:month/:day/:title/
tags: [알고리즘, 프로그래밍, 자바]
comments: true
---



# silver 4. 수 찾기 (1920)

> 출처 : [https://www.acmicpc.net/problem/1920](https://www.acmicpc.net/problem/1920)



## 문제

N개의 정수 A[1], A[2], ..., A[N]이 주어져 있을 때, 이 안에 X라는 정수가 존재하는지 알아내는 프로그램을 작성



**입력**: 첫 줄에는 자연수 N이 주어짐. 다음 줄에는 N개의 정수가 주어짐. 다음 줄에 줄에는 자연수 M이 주어지고, 마지막 줄에는 M개의 정수가 주어짐. 이 수들이 A 안에 존재하는지 알아내면 됨

**출력**: M개의 줄에 답을 출력. 존재하면 1을, 존재하지 않으면 0을 출력





## 풀이

- 아이디어: 아이디어는 간단함. 주어지는 M개의 정수들과 N에 있는 정수를 돌아가면서 하나씩 비교하면 됨

- 구현 1. **for문을 통해 값을 하나씩 비교**

  ```java
  import java.util.*;
  import java.io.*;
  
  public class Main {
    public static void main(String[] args) throws IOException{
      BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
      int lenN = Integer.parseInt(br.readLine());	// N 값을 받아옴
      ArrayList<Integer> N = new ArrayList<Integer>(input(br, lenN));	// N개의 정수를 입력받아 ArrayList에 저장
  
      int lenM = Integer.parseInt(br.readLine());	// M 값을 받아옴
      ArrayList<Integer> M = new ArrayList<Integer>(input(br, lenM));	// M개의 정수를 입력받아 ArrayList에 저장
  
      // 결과를 저장할 ArrayList
      List<Integer> result = new ArrayList<Integer> ();
  		
      // for문을 통해 값 비교
      for (int i=0; i < lenM; i++){
        if(N.contains(M.get(i))){	// 정수가 N에 있으면 1를 추가
          result.add(1);
        }
        else{	// 없으면 0을 추가
          result.add(0);
        }
      }
  
      // 결과값 출력
      for (int i: result){
        System.out.println(i);
      }
    }
  	
    // length개의 정수를 입력받아 ArrayList에 저장한 후 리턴해주는 메서드
    public static ArrayList<Integer> input(BufferedReader br, int length) throws IOException {
      StringTokenizer st = new StringTokenizer(br.readLine());
      ArrayList<Integer> result = new ArrayList<Integer> ();
      for (int i=0; i < length; i++){
        result.add(Integer.parseInt(st.nextToken()));
      }
  
      return result;
    }
  ```
  - 결과: 시간 초과
    - 단순히 for문을 사용해 비교하면 시간 초과 발생
    - 주어진 시간이 짧으므로 탐색 방법 중 이진 탐색(binary search) 방법을 사용하기로 함





- 구현 2. **Binary Search를 통한 탐색**

  ```java
  import java.util.*;
  import java.io.*;
  
  public class Main {
    public static void main(String[] args) throws IOException{
      BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
      int lenN = Integer.parseInt(br.readLine());
      int[] N = new int[lenN];
      StringTokenizer st = new StringTokenizer(br.readLine());
  		
      // N개의 정수 입력받아옴
      for(int i=0; i<lenN; i++){
        N[i] = Integer.parseInt(st.nextToken());
      }
  		
      // 이분 탐색을 위해 N을 정렬
      Arrays.sort(N);
  
      int lenM = Integer.parseInt(br.readLine());
      st = new StringTokenizer(br.readLine());
      
      // 결과를 저장해줄 StringBuilder
      StringBuilder sb = new StringBuilder();
  
      for (int i=0; i<lenM; i++){
        // nextToken()을 통해 정수를 하나씩 받아오면서 
        // binarySearch() 메서드를 통해 받아온 정수가 0 이상일 경우
        // N개의 정수에 포함되어 있는지 탐색
        if(Arrays.binarySearch(N, Integer.parseInt(st.nextToken())) >= 0){	// 포함되어있다면
          sb.append(1).append('\n');	// sb에 1과 enter 값을 추가. enter 값을 추가해주는 이유는 형식에 맞춰서 출력해주기 위함 
        }
        else{	// 포함되어있지 않다면
          sb.append(0).append('\n');	// sb에 0과 enter 값을 추가
        }
      }
      
      // 결과값 출력
      System.out.println(sb);
    }
  }
  ```
  - Binary Search를 사용해 탐색한 결과 테스트 통과함





## 배운점

- Arrays 컬렉션 내 Binary Search를 실행해주는 메서드 존재
  - [관련 메서드](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-java.lang.Object:A-java.lang.Object-)
- 여러 개의 결과값을 리스트에 저장해 for-each문을 활용해 출력하는 방법 외에도 StingBuilder를 활용해 출력할 수 있음
