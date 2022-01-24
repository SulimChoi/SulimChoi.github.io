---
layout: post
title: [Chapter 5] CPU Scheduling 
category: 운영체제
permalink: /til/:year/:month/:day/:title/
tags: [운영체제, CS]
comments: true
---

> KOCW에 공개된 [이화여대 반효경 교수님 운영체제](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09) 강의를 수강 후 정리한 내용입니다.

# 1. CPU and IO Bursts in Program Execution
---
![](/assets/post-img/os/programexecution.png)
- CPU burst: CPU만 연속적으로 사용하면서 명령어를 실행하는 단계
- IO burst: IO를 실행하는 단계
- 프로그램이 실행되면 CPU burst와 IO burst를 번갈아가며 실행
  - 프로그램의 종류에 따라 CPU burst와 IO burst의 빈도와 길이가 달라짐

### CPU burst Time의 분포
![](/assets/post-img/os/cpubursttime.png)
- IO bound job process: 중간에 IO 작업이 많아 CPU를 짧게 쓰기 때문에 CPU burst의 빈도가 높음
  - CPU를 잡고 계산하는 시간보다 IO에 많은 시간이 필요한 job
  - many short CPU bursts
- CPU bound job process: CPU를 오래 사용하기 때문에 CPU burst의 빈도가 낮음
  - 계산 위주의 job
  - few very long CPU bursts
- **여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요**
  - Interactive job에게 적절한 response 제공 요망
  - CPU와 IO 장치 등 시스템 자원을 효율적으로 사용
  
# 2. CPU Schedular & Dispatcher
---
**CPU Scheduler**
- Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름
- 운영체제 안에 CPU 스케줄링을 하는 코드가 있음

**Dispatcher**
- CPU의 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘기는 운영체제 안의 특정 기능
- 이 과정을 context switch라고 함

> **CPU 스케줄링이 필요한 경우**
>
> 1. Running -> Blocked (ex. IO 요청하는 시스템 콜): 강제로 빼앗지 않고 자진 반납(**nonpreemptive**, 비선점형)
> 2. Running -> Ready (ex. 할당시간만료로 timer interrupt): 강제로 빼앗음(**preemptive**, 선점형)
> 3. Blocked -> Ready (ex. IO 완료 후 인터럽트): 강제로 빼앗음(**preemptive**, 선점형)
> 4. Terminate: 강제로 빼앗지 않고 자진 반납(**nonpreemptive**, 비선점형)

# 3. Scheduling Algorithms
---
### Scheduling Criteria (성능 척도)
**CPU utilization (이용률)**
- 시스템 입장에서의 성능 척도
- 전체 시간 중에서 CPU가 일한 시간의 비율

**Throughput (처리량)**
- 시스템 입장에서의 성능 척도
- 주어진 시간 동안 몇 개의 작업을 완료했는지를 나타냄

**Turnaround time(소요시간, 반환시간)**
- 프로세스 입장에서의 성능 척도
- CPU를 사용하러 들어와서 다 쓰고 나갈 때까지 걸리는 시간 (대기 시간도 포함)

**Waiting time (대기 시간)**
- 프로세스 입장에서의 성능 척도
- CPU를 기다리는 시간

**Response time (응답 시간)**
- 프로세스 입장에서의 성능 척도
- ready queue에 들어와서 처음으로 CPU를 얻기까지 걸리는 시간

### 1. FCFS(First-Come First-Served)
![](/assets/post-img/os/fcfs.png)
- 먼저 온 순서대로 처리. 효율적이지는 않음
- 비선점형 스케줄링
- 프로세스 도착 순서를 바꿔보면
  ![](/home/ubuntu/SulimChoi.github.io/assets/post-img/os/fcfs_ex.png)
  - wating time이 많이 줄어드는 것을 확인할 수 있음
- **convoy effect**: burst time이 긴 프로세스가 먼저 도착해서 짧은 프로세스가 오래 기다리게 되는 현상

### 2. SJF(Shortest-Job-First)
- 각 프로세스와 다음번 CPU burst time을 가지고 스케줄링에 활용
- CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄
- SRTF 알고리즘은 average waiting time을 최소화한 알고리즘 (optimal)

**nonpreemptive**
- 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점당하지 않음
- 하나의 프로세스가 CPU를 다 쓰고 나가는 시점에 스케줄링을 할지 안할지 결정

**preemptive**
- SRTF(Shortest-Remaining-Time-First): 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
- 새로운 프로세스가 도착하는 시점에 언제든지 스케줄링이 이루어짐

**예시**

![](/assets/post-img/os/sjf1.png)
![](/assets/post-img/os/sjf2.png)

**문제점**
- **starvation**(기아 현상): CPU 사용 시간이 긴 프로세스는 CPU를 할당받는데 너무 오래 걸리거나 아예 실행되지 않을 수 있는 현상
- CPU 사용 시간을 미리 알 수 없다는 문제점이 있음
  - 단, 과거 사용 시간을 통해 추정이 가능하므로 SJF 알고리즘을 사용할 수 있는 것

### 3. Priority Scheduling
- 우선순위 스케줄링
- 우선순위가 가장 높은 프로세스에게 CPU를 할당
  - 각각의 프로세스에 우선순위를 나타내는 숫작가 할당됨
  - 가장 높은 우선순위 프로세스는 가장 작은 수가 할당된 프로세스
- nonpreemptive 방식과 preemptive 방식이 있음
- SJF는 우선순위 스케줄링의 일종이라고 볼 수 있음
  - SJF는 우선순위를 CPU의 burst time으로 정의한 것
- 문제점
  - **Starvation**: 우선순위가 낮은 프로세스가 CPU 할당받는데 너무 오래 걸리거나 아예 실행되지 않을 수 있는 현상
- 해결방법
  - **Aging**: 아무리 우선순위가 낮은 프로세스라도 오래 기다리는 경우 우선순위를 조금씩 높여주는 방법


### 4. Round Robin (RR)
- 현대에서 사용하는 스케줄링 방식은 RR에 기반됨
- **preemptive** 방식
- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐. 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 제일 뒤에 가서 다시 줄을 섬
- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻음
  - 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않음
  - q를 너무 크게 잡으면 FCFS와 비슷해지고, 너무 작게 잡으면 context switch 오버헤드가 커짐
- CPU를 길게 쓰는 프로세스는 기다리는 시간도 길어지고, 짧게 쓰는 프로세스는 기다리는 시간도 짧음
  - **wating time이 CPU burst time과 비례**
- 장점: **응답 시간(response time)이 빠름**

**예시**

![](/assets/post-img/os/rr.png)
- SJF보다 average turnaround time이 길지만 response time은 더 짧음


# 4. Multilevel Queue
---
![](/assets/post-img/os/multilevelqueue.png)
- 줄마다 우선순위가 존재
- 위에있는 줄일수록 높은 우선순위를 가짐
- 출신에 따라 우선순위가 달라짐
- 줄이 여러 개인데 CPU는 하나이므로 고려해야할 사항이 생김
  - Process를 어느 줄에 넣을 것인가
  - 우선순위가 높은 줄에만 CPU를 줄 경우 우선순위가 낮은 줄에서는 starvation이 발생할 수 있음

### 1. Multilevel Queue
- 본인의 출생을 극복하지 못함 (**프로세스가 다른 큐로 이동 불가**)
- Ready queue를 여러 개로 분할
  - foreground (interactive)
  - background (batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - foreground - RR (사용자와 interaction을 하므로 응답시간이 빠른 RR를 사용)
  - background - FCFS (사용자와 interaction을 하지 않으므로 context switch 오버헤드를 줄이기 위해 FCFS를 사용)
- 큐에 대한 스케줄링이 필요
  - Fixed priority scheduling: 우선순위가 높은 큐에게 먼저 CPU를 할당
  - Time slice: 각 큐에 CPU time을 적절한 비율로 할당

### 2. Multilevel Feedback Queue
![](/assets/post-img/os/multilevelfeedbackqueue.png)
- 본인 출생 극복 가능 (**프로세스가 다른 큐로 이동 가능**)
- aging을 이와 같은 방식으로 구현 가능
- CPU burst time이 짧은 프로세스에게 우선순위를 많이 주는 방식
- Multilevel-feedback-queue scheduler를 정의하는 파라미터
  - Queue의 수
  - 각 큐의 scheduling algorithm
  - Process를 상위 큐로 보내는 기준
  - Process를 하위 큐로 보내는 기준
  - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
- 일반적으로 
  1. 처음으로 들어오는 프로세스를 우선순위가 높은 큐에 주고 RR 알고리즘의 quantum를 짧게 줌
  2. 아래 큐로 갈수록 quantum를 길게 줌
  3. 제일 아래 큐는 FCFS 알고리즘 사용

# 5. 여러 Scheduling 방법
---
### 1. Multiple-Processor Scheduling
- CPU가 여러 개인 경우의 스케줄링

**Homogeneous processor인 경우**
- Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있음
- 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 더 복잡해짐

**Load sharing**
- 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 매커니즘이 필요
- 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법

**Symmetric Multiprocessing(SMP)**
- 모든 CPU가 대등한 관계
- 각 프로세서가 각자 알아서 스케줄링 결정

**Asymmetric multiprocessing**
- 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

### 2. Real-Time Scheduling
- deadline은 보장해줘야 함

**Hard real-time systems**
- 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함

**Soft real-time computing**
- 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

### 3. Thread Scheduling
**Local Scheduling**
- User level thread(운영체제가 모르는 thread)의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
- 운영체제가 아닌 사용자 프로세스가 직접 어느 thread에게 cpu를 줄지 경정

**Global Scheduling**
- Kernel level thread의 경우 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정

# 6. Algorithm Evaluation
---
![](/assets/post-img/os/algorithmevaluation.png)
- server: CPU
- arrival rate: 프로세스 도착률
- service rate: 프로세스 처리률 (CPU가 단위시간당 처리하는 프로세스의 비율)

**Queueing models**
- 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각 종 performance index 값을 계산

**Implementation(구현) & Measurement(성능 측정)**
- 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능을 측정 비교

**Simulation (모의 실험)**
- 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
  - trace: simulation 프로그램에 input으로 들어갈 데이터
  - 만들 수도 있고 실제 프로그램을 돌리면서 뽑아낼 수도 있음

> **Chapter 5 끝!!!**
>
>게시물에 사용한 사진은 강의 내용을 캡쳐한 것입니다.
