---
layout: post
title: Chapter 9. Memory Management_1 
category: 운영체제
permalink: /til/:year/:month/:day/:title/
tags: [운영체제, CS]
comments: true
---



> KOCW에 공개된 [이화여대 반효경 교수님 운영체제](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09) 강의 수강 후 정리한 내용입니다. 



**2가지 주소**

- Logical address (=virtual address)
  - 프로세스마다 독립적으로 가지는 주소 공간
  - 각 프로세스마다 0번지부터 시작
  - **CPU가 보는 주소는 logical address**

- Physical address
  - 메모리에 실제 올라가는 위치

- 주소 바인딩: 어떤 프로그램이 어디 메모리 주소로 올라갈지를 결정하는 것
  - 주소 변환 과정: Symbolic address -> Logical address -> Physical address
  - Symbolic address: 프로그래머가 사용하는 주소



# 1. 주소 바인딩 (Address Binding)

> **Compile time binding**
>
> - 물리적 메모리 주소가 컴파일 시 알려짐
> - 시작 위치 변경시 컴파일을 다시 해야 함
> - 컴파일러는 절대 코드(absolute code) 생성
>
> **Load time binding**
>
> - 실행이 시작될 때 물리적 메모리 주소가 알려짐
> - 컴파일러가 재배치가능코드(relocatable code)를 생성한 경우 가능
>
> **Execution time binding (= Run time binding)**
>
> - 실행 도중 물리적 메모리 주소가 변경될 수 있는 binding
> - CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)
> - **하드웨어적인 지원**이 필요 (ex. base and limit registers, MMU)



![addressbindng](/assets/post-img/os/addressbindng.png)

- 소스코드: A+B를 해서 A에 저장한 후 C 위치로 jump
- Compile time binding
  - 컴파일 시점에 이미 물리적 메모리 주소가 결정됨
  - 실행파일에 있는 주소의 위치와 같은 주소에 위치해야 함
- Load time binding
  - 프로그램이 실행되서 메모리에 올라갈 때 물리적 주소가 결정됨
- Run time binding
  - Load time binding처럼 프로그램이 실행되서 메모리에 올라갈 때 물리적 주소가 결정되지만, 프로그램 실행 도중 물리적 주소가 변경될 수 있음
  - 메모리에서 쫓겨났다가 다시 올릴 때, 300번지에 다른 내용이 들어와있다면 비어있는 700번지에 내용을 저장할 수 있음





### MMU (Memory-Management Unit)

- logical address를 physical address로 매핑해주는 하드웨어 장치

![mmu](/assets/post-img/os/mmu.png)

- MMU scheme

  - base register와 limit register를 사용해 주소 변환을 수행 
  - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(= relocation register)을 더함
  - base register는 접근할 수 있는 물리적 메모리 주소의 최솟값을 가짐

  ![mmu_2](/assets/post-img/os/mmu_2.png)

  - limit register는 프로그램의 최대 크기를 담고 있음. 프로그램의 논리 주소보다 더 큰 주소의 값을 요청하면 trap이 발생

- user program

  - logical address만을 다루며, 실제 physical address를 볼 수 없고 알 필요가 없음





# 2. Some Terminologies

### Dynamic Loading

- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는 것
- memory utilization 향상
- 가끔식 사용되는 만은 양의 코드의 경우 유용 (ex. 오류 처리 루틴)
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)
- 운영체제가 직접 관리하는 페이징 기법과는 다름. 하지만 현재는 dynamic loading과 섞어 사용하기도 함
  - 프로그래머가 명시적으로 dynamic loading을 구현한 것이 본래의 dynamic loading이지만, 프로그래머가 명시적으로 구현하지 않고 운영체제가 관리해주는 것도 dynamic loading이라고 섞어 사용하기도 함



### Overlays

- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 **초창기 시스템에서 수작업으로 프로그래머가 구현**
  - Manual Overlay라고도 함
  - 프로그래밍이 매우 복잡
- **운영체제의 지원이 없다**는 것에서 Dynamic Loading과 차이점이 있음



### Swappping

- 프로세스를 일시적으로 메모리에서 backing store로 쫓아내는 것
  - backing store (= swap area): 디스크. 많은 사용자의 프로세스 이미지를 담을만큼 충분히 빠르고 큰 저장 공간

![swapping](/assets/post-img/os/swapping.png)

- Swap In / Swap out
  - 일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스 선정
  - priority-based CPU scheduling algorithm
    - priority가 낮은 프로세스를 swapped out 시킴
    - priority가 높은 프로세스를 메모리에 올려 놓음
  - Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야 함
  - Execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
  - swap time은 대부분 transfer time (swap되는 양에 비례하는 시간)
- 원칙적으로 swap in / swap out의 의미는 프로그램를 구성하는 주소 공간 전체가 쫓겨나는 것을 의미하지만 페이징에서 일부 페이지만 쫓겨나는 것을 의미하는 용어로도 사용하기도 함



### Dynamic Linking

- Linking을 실행 시간(execution time)까지 미루는 기법
- Linking: 여러 곳에 존재하던 컴파일된 파일들을 하나의 실행 파일로 만드는 과정



**Static linking**

- 라이브러리가 프로그램의 실행  파일 코드에 포함됨
- 실행 파일의 크기가 커짐
- 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비



**Dynamic linking**

- 라이브러리가 프로그램의 실행 파일 코드에 포함되지 않고 실행시 연결(link) 됨
- 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둠
- 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
- dynamic linking을 해주는 라이브러리를 shared library라고 함
- 운영체제의 도움이 필요





# 3. Allocation of Physical Memory

- 메모리는 일반적으로 두 영역으로 나누어 사용
  - OS 상수 영역: interrupt vector와 함께 낮은 주소 영역 사용
  - 사용자 프로세스 영역: 높은 주소 영역 사용



**사용자 프로세스 영역의 할당 방법**

- **Contiguous allocation (연속 할당)**
  - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
  - 프로그램이 메모리에 올라갈 때 한 군데에 통째로 올라가는 방식
  - Fixed partition allocation
  - Variable partition allocation

- **Noncontiguous allocation (불연속 할당)**
  - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
  - Paging
  - Segmentation
  - Paged Segmentation



### Contiguous allocation

![contiguous allocation](/assets/post-img/os/contiguous allocation.png)

**고정 분할 방식 (Fixed partition allocation)** 

- 물리적 메모리를 몇 개의 영구적 분할(partition)으로 나눔
- 분할의 크기가 동일할 수도 다를 수도 있음
- 분할당 하나의 프로그램을 적재
- External fragmentation(외부 조각): 프로그램 크기보다 분할의 크기가 작은 경우
  - 아무 프로그램에도 배정되지 않은 빈 곳인데도 프로그램이 올라갈 수 없는 작은 분할
- Internal fragmentation(내부 조각): 프로그램 크기보다 분할의 크기가 큰 경우
  - 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
  - 특정 프로그램에 배정되었지만 사용되지 않는 공간



**가변 분할 방식 (Variable partition allocation)**

- 프로그램의 크기를 고려해 할당

- 분할의 크기, 개수가 동적으로 변함

- External fragmentation 발생

- **Hole (가용 메모리 공간)**

  ![hole](/assets/post-img/os/hole.png)

  - 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음
  - 프로세스가 도착하면 수용 가능한 hole을 할당
  - 운영체제는 할당 곤간과 가용 공간(hole)에 대한 정보를 유지해야 함

- **Dynamic Storage-Allocation Problem**

  - 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
  - First-fit: Size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
  - Best-fit: Size가 n 이상인 가장 작은 hole을 찾아 할당
    - Hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야 함
    - 많은 수의 아주 작은 hole들이 생성됨
  - Worst-fit: 가장 큰 hole에 할당
    - 모든 리스트를 탐색해야 함
    - 상대적으로 아주 큰 hole들이 생성됨
  - first-fit과 best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐

- **Compaction**

  - external fragmentation 문제를 해결하는 한 가지 방법
  - 사용 중인 메모리 영역을 한 군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 block을 만드는 것
  - 비용이 매우 많이 듦
  - 최소한의 메모리 이동으로 compaction하는 방법이 효율적일 수 있지만 매우 복잡한 문제임
  - Compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있음 (Run time binding이 지원되야 함)
