> KOCW에 공개된 [이화여대 반효경 교수님 운영체제](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09) 강의를 수강 후 정리한 내용입니다.

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

![algorithm1](/home/ubuntu/SulimChoi.github.io/assets/post-img/os/algorithm1.png)

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