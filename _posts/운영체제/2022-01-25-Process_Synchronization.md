---
layout: post
title: Chapter 6. Process Synchronization 
category: 운영체제
permalink: /til/:year/:month/:day/:title/
tags: [운영체제, CS]
comments: true
---



> KOCW에 공개된 [이화여대 반효경 교수님 운영체제](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09) 강의를 수강 후 정리한 내용입니다.



> - Process Synchronization (프로세스 동기화)를 Concurrency Control (병행 제어)라고 부르기도 함
>   - 프로세스가 동시에 무언가를 실행하면서 발생하는 문제를 해결하는 것을 의미



### Race Condition

![racecondition](/assets/post-img/os/racecondition.png)

- Execution-Box: 연산이 수행되는 곳
- Storage-Box(S-box): 데이터가 저장되어 있는 곳
- **Race Condition**: S-Box(Memory Address Space)를 공유하는 E-box(CPU Process)가 여러 개 있는 경우
  
  - 발생 가능한 경우
    1. Multiprocessor system에서 메모리를 공유하고 있는 경우
    2. 공유메모리를 사용하는 프로세스들 사이에서 발생 가능
    3. 커널 내부 데이터를 접근하는 루틴들(ex. 커널 모드 수행 중 인터럽트로 커널모드 다른 루틴 수행시) 사이에서 발생 가능
  
- OS에서 race condition이 발생하는 경우

  1.  kernel 수행 중 인터럽트 발생 시

     ![racecondition_1](/assets/post-img/os/racecondition_1.png)

     - 커널모드 running 중 interrupt가 발생하여 인터럽트 처리루틴이 수행 -> 양쪽 다 커널 모드이므로 kernel address space 공유
     - 해결방법: interrupt가 들어와도 instruction이 끝날 때까지 disable시켰다가 instruction 작업이 끝난 후 인터럽트 처리루틴을 수행

  2.  Process가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우

     ![racecondition_2](/assets/post-img/os/racecondition_2.png)

     - system call을 하는 동안에는 kernel address space의 data를 access하게 되는데 이 작업 중간에 CPU를 preempt해가면 race condition 발생
     - 해결방법: 커널 모드에서 수행 중일 때는 CPU를 preempt하지 않음. 커널 모드에서 사용자 모드로 돌아갈 때 preempt

  3.  Multiprocessor에서 shared memory 내의 kernel data

     - interrupt enable/disable로 해결되지 않음

     - 해결방법: 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법 (비효율적) / 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock 혹은 unlock을 하는 방법

       

# 1. Process Synchronization 문제

- 공유 데이터의 동시 접근(concurrent access)은 데이터의 불일치 문제를 발생시킬 수 있음
- 일관성(consistency) 유지를 위해 협력 프로세스(cooperation process) 간의 실행 순서를 정해주는 메커니즘 필요

**Race condition**

- 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐

- **race condition을 막기 위해 concurrent process는 동기화(synchronize)되어야 함**



**The Critical-Section Proble**

- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우

- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section(임계 구역)이 존재

- 문제

  - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 함

    ![criticalsection](/assets/post-img/os/criticalsection.png)



**Initial Attempts to Solve Problem**

![criticalsection](/assets/post-img/os/criticalsection.png)

- 두 개의 프로세스가 있다고 가정하면 프로세스들의 일반적인 구조는 그림처럼 구성됨

		- 공유 데이터를 접근하거나 접근하지 않거나 => 두 구역으로 구성됨
		- 프로세스들은 수행의 동기화(Synchronize)을 위해 몇몇 변수를 공유할 수 있음 (Synchronization variable)



**프로그램적 해결법의 충족 조건**

1. **Mutual Exclusion**(상호 배제): 하나의 프로세스가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안됨
2. **Progress**: 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 함
3. **Bounded Waiting**(유한 대기): 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 함



# 2. Process Synchronization 문제 해결 알고리즘

### Algorithm 1

![algorithm1](/assets/post-img/os/algorithm1.png)

- `turn`: critical section에 들어갈 차례를 나타내주는 변수 (0인 경우 0번 프로세스, 1일 경우 1번 프로세스)
- while문을 통해 내 차례가 아닌 경우에는 대기하고 turn이 0으로 변경되면 critical secetion에 들어가고, 나오면 상대방의 차례로 변경해줌
- Mutual Exclusion은 만족하지만 Progress는 만족하지 못함
  - critical section은 반드시 교대로 돌아가도록 구성되어 있음. 따라서 두 프로세스가 critical section에 들어가는 횟수가 비슷하지 않으면 어느 프로세스는 critical section에 아예 들어가지 못할 수 있음



### Algorithm 2

![algorithm2](/assets/post-img/os/algorithm2.png)

- `flag`: 프로세스마다 각각의 flag를 가짐. 본인이 critical section에 들어가고자 한다는 의중을 표현하는 변수
- critical section에 들어가고자 한다면 본인의 flag를 true로 변경해준 뒤 상대방의 flag를 체크. 상대방의 flag가 true일 경우 while문을 돌면서 기다리고 false일 경우 critical section에 들어가고, 나올 때 본인의 flag를 false로 변경해줌
- mutual exclusion은 만족하지만 progress는 만족하지 못함
  - critical section에 들어갔다 나와야 flag의 값이 false로 변경되는데 critical section에 들어가기 전 두 프로세스의 flag의 값이 모두 true이면 아무로 critical section에 들어가지 못함
  - 두 프로세스 모두 2행까지 수행 후 끊임없이 양보하는 상황 발생 가능



### Algorithm 3 (Peterson's Algorithm)

![algorithm3](/assets/post-img/os/algorithm3.png)

- `turn`과 `flag` 모두 사용
- critical section에 들어가고자 할 때, 가장 먼저 `flag`를 true로 변경하고 `turn`을 상대방 turn으로 변경. 상대방의 `flag`가 true이고 상대방의 turn이면 while문을 돌며 대기. 상대방의 `flag`가 false이거나 turn이 내 turn이라면 critical section에 들어가고 나올 때 `flag` 값을 false로 변경
- 세 가지 조건 모두 만족
  - 두 프로세스 모두 critical section에 들어가고자 할 경우 `turn` 값을 보고 critical section에 먼저 들어갈 프로세스를 정함
- **Busy Waiting** (= spin lock): 계속 CPU와 memory를 쓰면서 wait
  - 내가 CPU를 잡아서 and 조건을 만족해 while문을 돌고 있는 경우 while문을 빠져나갈 수 없으므로 CPU 할당시간이 끝날 때까지 while문만 돌다가 CPU를 반납 => 비효율적



### Synchronization Hardware

- Synchronization 문제가 발생했던 이유: 데이터를 읽는 과정과 쓰는 과정을 하나의 instruction으로 처리할 수 없었기 때문

- 하드웨어적으로 **Test & modify를 atomic하게 수행**할 수 있도록 지원하는 경우 앞의 문제를 간단히 해결할 수 있음

  ![testandset](/assets/post-img/os/testandset.png)

  - `Test_and_set(a)`: a의 원래의 값을 읽어내고 원하는 값으로 a의 값을 다시 세팅
    - ex) a의 값이 0인 경우, `Test_and_set(1)`을 수행하면 원래 a의 값인 0을 읽어오고 a의 값을 1로 세팅해줌
    
    

# 3. Semaphores

- 일종의 추상 자료형

- 공유 자원을 획득하고 반납하는 것을 semaphore가 관리

- Semaphore S가 있다고 하면 이 변수는 정수 값(자원의 개수를 의미)을 가질 수 있고 연산은 두 가지로 정의됨

  ```tex
  P(S):	while (S <= 0) do no-op;	// wait
  		S--;
  
  V(S):	S++;
  ```

  - P(S): 자원을 획득하는 연산	/	V(S): 자원을 반납하는 연산
  - automic 연산에 의해서만 접근 가능
  - busy-wait 발생: 자원이 없을 때, P 연산을 하게되면 자원이 없기 때문에 while문을 계속 돌다가 CPU 시간을 모두 쓰고 반납하게 됨



### Critical Section of n Processes

![semaphore](/assets/post-img/os/semaphore.png)

- Semaphore 추상 자료형이 지원된다면, 프로그래머는 앞에서처럼 일일이 코딩해줄 필요없이 P와 V 연산만 수행해주면 됨
- busy-wait(= spin lock)은 효율적이지 못함
- **Block & Wakeup** 방식(= sleep lock)으로도 구현 가능



### Block & Wakeup Implementation

![blockwakeup](/assets/post-img/os/blockwakeup.png)

- Semaphore를 획득할 수 없는 경우 그 프로세스를 block시키고, 다른 프로세스가 shmaphore를 반납하면 block된 프로세스 중 하나를 깨움

- block: 커널은 block을 호출한 프로세스를 suspend 시키고, 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
- wakeup(P): block된 프로세스 P를 wakeup 시키고, 이 프로세스의 PCB를 ready queue로 옮김



![blockwakeup](/assets/post-img/os/blockwakeup.png)

- 여기에서의 S는 자원의 개수를 의미하지 않음. S의 값이 0보다 작다는 것은 누군가가 자원을 기다리고 있다는 의미이고, 양수인 경우 자원을 기다리고 있는 프로세스가 없다는 의미

- V 연산에는 자원을 기다리면서 잠들어 있는 프로세스가 있다면 그 프로세스를 깨워주는 작업이 포함되어 있어야 함



**Block/wakeup overhead vs critical section 길이**

- critical section의 길이가 긴 경우 block/wakeup이 적당
- critical section의 길이가 매우 짧은 경우 block/wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
- 일반적으로 block/wakeup 방식이 더 좋음



### Two Types of Semaphores

**Counting semaphore**

- 도메인이 0 이상인 임의의 정수값
- 주로 resource couting에 사용



**Binary semaphore (= mutex)**

- 자원의 개수가 하나인 경우
- 0 또는 1 값만 가질 수 있는 semaphore
- 주로 mutual exclusion (lock/unlock)에 사용



### Semaphore 사용 시 발생할 수 있는 문제점

- 어떤 일을 하기위해 semaphore 두 자원(S, Q)을 모두 획득해야 한다고 가정
- **Deadlock**: 본인이 가진 자원은 내놓지 않고 상대방이 가진 자원을 얻기 위해 영원히 기다려야 하는 상황
  - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
  - 해결 방법: 자원을 획득하는 순서를 똑같게 맞춰주면 해결 가능
- **Starvation**: 특정 프로세스가 영원히 자원을 얻지 못하고 무한히 기다려야 하는 현상
  - indefinite blocking. 프로세스가 suspend된 이유에 해당하는 semaphore 큐에서 빠져나갈 수 없는 현상



![deadlockstarvation](/assets/post-img/os/deadlockstarvation.png)

- 모든 사람은 본인의 왼쪽, 오른쪽 사람과 젓가락을 공유
- 아래쪽 세 자리를 왼쪽부터 1, 2, 3번이라 가정
- Deadlock 발생
  - 모든 사람이 동시에 본인의 왼쪽 젓가락을 든 경우
- Starvation 발생
  - 2번에 앉은 사람이 젓가락을 들기 전 1번에 앉은 사람이 본인의 오른쪽 젓가락을 들고 식사를 시작하고, 1번에 앉은 사람이 젓가락을 내려놓기 바로 직적엔 3번에 앉은 사람이 본인의 왼쪽 젓가락을 들고 식사를 시작하고, 3번에 앉은 사람이 젓가락을 내려놓기 직전에 1번에 앉은 사람이 다시 젓가락을 드는 상황이 반복되면 2번의 경우 starvation이 발생



# 4. Classical Problems of Synchronization

### Bounded-Buffer Problem (Producer-Consumer Problem)

![boundedbuffer](/assets/post-img/os/boundedbuffer.png)

- buffer의 크기가 유한함
  - circle 형태로 buffer가 구성됨
  - 주황색은 내용이 들어있는 버퍼, 회색은 내용이 비어있는 버퍼를 의미

- 생산자-소비자 문제: 프로세스가 두 가지 종류가 있음

  - Producer, Consumer가 각각 여러 개 존재
  - Producer는 공유 버퍼에 데이터를 하나 만들어서 집어넣는 역할을 하고, Consumer는 데이터를 꺼내는 역할을 함

- **문제점 1) 공유 버퍼에 lock을 걸어줘야하는 경우**

  - 경우 1) 공유 버퍼이기 때문에 여러 생산자가 동시에 같은 빈 버퍼를 채우고자 하면 문제가 발생

  - 경우 2) 여러 소비자가 동시에 같은 버퍼의 내용을 꺼내가고자 할 때 문제 발생

  - 공유 버퍼에 데이터를 쓰거나 꺼내갈 때는 producer가 lock을 걸어 다른 producer가 접근하지 못하도록 막은 뒤에 데이터를 쓰거나 꺼내간 뒤 lock를 풀어줌

- **문제점 2) 공유 버퍼가 가득 차거나 빈 경우**

  - 여러 생산자가 동시에 도착해 버퍼를 가득 채웠는데, 소비자는 오지 않고 생산자만 계속 와서 버퍼를 채우려고 하는 경우
  - 생산자 입장에서 버퍼가 가득 찬 경우, 소비자가 와서 버퍼의 내용을 꺼내갈 때까지 기다려야 함 (생성자 입장에서의 자원은 비어있는 버퍼)
  - 소비자 입장에서 다른 소비자들이 먼저 버퍼의 내용을 모두 꺼내가고 버퍼가 비었을 경우, 생산자가 와서 버퍼를 채워줄 때까지 기다려야 함 (소비자 입장에서의 자원은 채워져있는 버퍼)

- Shared data: buffer 자체 및 buffer 조작 변수(empty/full buffer의 시작 위치)

- Synchronization variables

  - mutual exclusion: lock을 해주기 위해 **binary semaphore** 변수가 필요
  - resource count: 가용 자원의 개수를 세기 위한 **integer semaphore** 변수가 필요

- Bounded-Buffer 의사코드

​	![boundedbuffer2](/assets/post-img/os/boundedbuffer2.png)	



### Readers-Writers Problem

- 문제점 1) 한 프로세스가 DB(공유 데이터)에 write 중일 때 다른 process가 접근하면 안됨. 단, read는 여럿이 동시에 가능
  - 누군가 쓰는 경우에는 lock를 걸어 아무도 접근하지 못하게 막지만, 누군가 읽고 읽는 도중에 다른 reader가 도착하면 동시에 읽을 수 있게 해줘야 함
  - 누군가 읽고 있는 도중 writer가 도착하면 writer는 기다려야함. writer는 다른 작업이 아무 것도 없는 동안 배타적(독립적)으로 작업을 수행
- Shared data: DB 자체, readcount(현재 DB에 접근 중인 Reader의 수)
- Synchronizaion variables
  - mutex: 공유 변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용
    - readcount에 접근할 때 lock를 걸어주는 변수
  - db: Reader와 writer가 공유 DB 자체를 올바르게 접근하게 하는 역할

-  Solution

  ![readerwriter](/assets/post-img/os/readerwirter.png)

  - reader의 경우에도 lock은 걸어줘야 함. 읽고 있는 도중에 writer가 들어와서 lock을 걸 수 있기 때문
    - reader가 들어왔을 때 다른 reader가 읽고 있지 않다면 lock을 걸고 읽기 수행
    - reader가 들어왔을 때 다른 reader가 읽고 있다면 lock을 걸지 않고 읽기 수행
    - 마지막 reader가 읽고 나갈 때는 처음 걸어줬던 lock을 풀어줘야 함
  - reader가 읽고 있는 도중에 writer가 들어오면 writer는 reader가 읽기를 수행한 뒤 lock을 풀어줄 때까지 기다려야 함
    - 만약, readcount가 1이었을 때 writer가 들어와서 대기하고 있는 도중, 1000개의 reader가 들어오면 writer는 1000개의 reader가 읽기를 수행하고 마지막 reader가 lock을 풀어줄 때까지 대기 해야 함
    - 즉, writer가 너무 오래 기다리기 때문에 starvation이 발생 가능
    - 큐에 우선순위를 줘서 writer가 너무 오래 기다리지 않도록 해줄 수 있게 구현할 수 있음 (일상생활에서의 ex. 신호등)



### Dining-Philosophers Problem

![deadlockstarvation](/assets/post-img/os/deadlockstarvation.png)

- 철학자들은 생각하는 일과 밥 먹는 일을 수행

![diningphilosopher](/assets/post-img/os/diningphilosopher.png)

- 밥을 먹기 위해서는 왼쪽 젓가락과 오른쪽 젓가락을 모두 잡아야 함
- 문제점) Deadlock 가능성이 있음. 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어버린 경우
- 해결 방법 1) 4명의 철학자만이 테이블에 동시에 앉을 수 있게 함
- 해결 방법 2) 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 함
- 해결 방법 3) 비대칭 (짝수 철학자는 왼쪽 젓가락부터 잡도록, 홀수 철학자는 오른쪽 젓가락부터 잡도록 함)



# 5. Monitor

>  **Semaphore의 문제점**
>
> - 코딩하기 힘듦
> - 정확성(correctness) 입증이 어려움
> - 자발적 협력(voluntary cooperation)이 필요
> - 한번의 실수가 모든 시스템에 치명적 영향을 미침



- **high-level synchronization construct**
  - 동시 수행 중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 construct
- **공유 데이터에 대한 동시접근을 모니터가 막아줌**

![monitor](/assets/post-img/os/monitor.png)

- 공유 데이터에 접근하기 위해서는 monitor 내부에 정의된 procedure를 통해야만 접근 가능
- 모니터 안에 공유 데이터와 공유 데이터 접근에 필요한 procedures를 정의
- Semaphore와의 차이점: 모니터는 기본적으로 모니터에 동시 접근이 불가능하도록 만들어져 있기 때문에 lock를 걸어줄 필요가 없음 

- Semaphore와의 공통점: 자원의 개수를 세주는 변수가 필요

  - semaphore 변수와 비슷한 역할을 하는 condition variable이 있음

- **condition variable** (`condition x, y;`)

  - x라는 자원이 여분이 있으면 바로 접근하게 해주고, 여분이 없어 기다려야 하는 경우 **wait**(`x.wait();`) 연산 수행
  - 접근을 마치고 빠져나갈 때에는 **signal**(`x.signal();`) 연산을 통해 잠들어 있는 프로세스가 있으면 깨워줌
  - signal 연산은 깨워주는 연산이므로 깨워줄 프로세스가 없는 경우 아무 일도 일어나지 않음

  

### Bounded-Buffer Problem

![monitor2](/assets/post-img/os/monitor2.png)

- 공유 버퍼가 모니터 내부에 정의되어 있기 때문에 생산하거나 소비하는 작업을 수행하기 위해서는 모니터 내부 코드를 실행해야 함
  - 생성자 혹은 소비자 모두 하나의 프로세스만 모니터 안에서 활성화되기 때문에 굳이 lock를 걸어줄 필요가 없음
  - 즉, 공유 데이터를 읽거나 쓰는 작업 앞뒤에 lock을 걸거나 풀어주는 코드가 없음
- 생산자 입장에서는 비어있는 버퍼가 필요
  - 비어있는 버퍼가 없다면 비어있는 버퍼를 기다리는 줄(`empty`)에 줄서서 기다리도록 함
  - 비어있는 버퍼가 있다면 비어있는 버퍼를 채워준 후, 버퍼가 비어있었기 때문에 잠들어있던 소비자가 있다면 깨워줌
- 소비자 입장에서는 내용이 들어있는 버퍼가 필요
  - 내용이 들어있는 버퍼가 없다면 내용이 들어있는 버퍼를 기다리는 줄(`full`)에 줄서서 기다리도록 함
  - 내용이 들어있는 버퍼가 있다면 버퍼의 내용을 꺼내간 후, 빈 버퍼를 기다리는 생산자가 있다면 깨워줌



### Dining Philosophers Problem

![monitor3](/assets/post-img/os/monitor3.png)

- 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 했을 때의 코드



> **Chapter 6 끝 !!!**
>
> 게시물에 사용한 사진은 강의 내용을 캡쳐한 것입니다.
