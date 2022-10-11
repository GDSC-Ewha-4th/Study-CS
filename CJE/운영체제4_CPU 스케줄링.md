## __🦈 프로세스의 특성 분류 (CPU burst-time)__
CPU bound job (few very long CPU bursts) : CPU를 오래 필요로 하는 프로세스

I/O bound job (many short CPU bursts) : CPU를 짧게 필요 (사람과의 interaction이 많음) <br/>
-> CPU bound job 보다는 I/O bound job에게 먼저 CPU를 주기 <br/>
(CPU 조금 쓰고 IO 하러 가버리니깐, CPU bound job 먼저 주면 그동안 아무것도 못하고 기다림...)

## __🦈 CPU scheduler & dispatcher__
CPU Scheduler
* Ready인 프로세스 중 이번에 CPU를 줄 프로세스를 고른다 (결정)

Dispatcher
* CPU 제어권을 스케줄러가 고른 프로세스에게 넘겨줌 (실행) <br/>
-> 이 과정을 Context Switch(문맥 교환)이라고 함

CPU 스케줄링이 필요한 경우
1. Running -> Blocked (ex - IO를 요청하는 시스템 콜) 
* nonpreemptive (강제 X, 자진 반납)
2. Running -> Ready (ex - timer interrupt)
* preemptive (강제로 빼앗음)
3. Blocked -> Ready (ex - IO 완료 후 인터럽트)
* preemptive (강제로 빼앗음)
4. Terminated
* nonpreemptive (강제 X, 자진 반납)

## __🦈 CPU 스케줄링 성능 척도__
CPU utilization (이용률): CPU가 최대한 바쁘게 사용됨

Throughput (처리량): 시간당 완료된 프로세스의 개수

Turnaround time (소요시간, 반환시간): CPU를 기다린 시간 + 사용한 시간

Waiting time (대기시간): CPU Ready Queue에서 대기한 시간의 합

Response time (응답시간): 프로세스가 대기 후 **최초로** CPU를 얻기까지 걸리는 시간

## __🦈 CPU 스케줄링 알고리즘__
1. **FCFS (First Come First Served)**

먼저 온 순서대로 CPU 사용
* nonpreemptive (강제로 빼앗지 X)
* Convoy effect: 짧게 걸리는 프로세스가 길게 걸리는 프로세스보다 늦게 도착해서 오래 기다림 . . .

---
2. **SJF (Shortest Job First)**

CPU를 제일 짧게 쓰는 프로세스에게 CPU를 먼저 줌
* 가장 Optimal한 방법 (가장 효율적)

단점 1: starvation (기아 문제) -> 형평성 X<br/>
단점 2: 대기중인 프로세스들의 CPU burst time을 어떻게 알 수 있는가?<br/>
-> 과거의 CPU burst time을 통해 예측 (exponential averaging)

Nonpreemptive 버전의 SJF
* 일단 CPU를 잡으면 CPU burst가 완료될 때까지 빼앗지 않음

Preemptive 버전의 SJF = Shortest **Remaining** Time First (SRTF)
* 수행중인 프로세스의 **남은 burst time**보다 더 짧은 CPU burst time을 갖는 프로세스가 도착하면 CPU를 빼앗김
* -> 제일 ~ Optimal

---
3. **Priority Scheduling**

우선순위가 가장 높은 프로세스에게 CPU를 할당

* 수행중인 프로세스보다 높은 우선순위의 프로세스가 왔을 때 <br/>
  Nonpreemptive - 안 뺏음 <br/>
  Preemptive - 뺏음 <br/>

* SJF도 일종의 priority 스케줄링 <br/>
-> Starvation 문제 => Aging으로 해결 (오래 기다릴수록 우선순위를 높여줌)

---
4. **RR (Round Robin)**

timer를 사용해 일정 시간마다 인터럽트를 통해 프로세스를 교체 -> Preemptive
* 공평한 방법

할당시간 q의 적정 크기
* q가 large -> FCFS와 다를 바 없음
* q가 small -> context switch의 오버헤드

Response time (첫 응답시간)이 짧아짐 (어떤 프로세스도 (n-1)q time이상 기다리지 않음) <br/>
Turnaround time (소요시간, 반환시간) 은 길어짐 - 모든 프로세스가 골고루 오래 걸릴 수도..<br/>
-> CPU burst time이 긴 job과 짧은 job이 섞여있을 때 효율적

---
5. Multilevel Queue

CPU는 하나인데 줄은 여러개 -> Ready Queue를 여러개로 분할

Foreground: interactive

Background: batch (no human interaction)

큐에 대한 스케줄링이 필요
* Fixed priority 스케줄링
    * 모든 foreground를 먼저 -> background는 starvation 문제
* Time slice 스케줄링
    * 각 큐에 CPU time을 적절하게 할당
    * ex - foreground에게 80%로 RR, background에게 20%로 RR

한 번 줄서면 다른 큐로 이동 X (신분 변경 불가)

---
6. Multilevel Feedback Queue

처음에는 할당시간 8인 큐 줄 서기, 더 써야 하면 16인 큐로 강등, 더 써야 하면 FCFS(할당시간 X)인 큐로 강등

대신, 우선순위는 할당시간 8인 큐 > 16인 큐 > FCFS 큐

하위 큐의 starvation 문제?? -> aging으로 해결 가능

---
7. Multiple Processor Scheduling
    
CPU가 여러개 있는 환경에서의 스케줄링

제약조건 - ex) 프로세스 A,B는 특정 CPU에서 처리되어야 함

Load sharing
* 일부 프로세스에 job(프로세스)이 몰리지 않도록 부하를 적절히 공유
* CPU마다 별개의 큐를 사용 vs 공동 큐를 사용

Symmetric Multiprocessing (SMP)
* 각 프로세스가 각자 알아서 스케줄링 결정

---
8. Real time Scheduling

Hard real-time
* 정해진 시간 안에 반.드.시 끝내도록 스케줄링
* 데드라인을 안 지키면 큰일남

Soft real-time
* 일반 프로세스에 비해 높은 priority
* 안 지켜도 큰 일까진 아님 (ex 동영상 스트리밍)

---
9. Thread Scheduling

Local Scheduling
* User level thread의 경우, 사용자 수준의 쓰레드 라이브러리에 의해 어떤 쓰레드를 스케줄할지 결정
* 운영체제는 쓰레드의 존재를 모름, 프로세스 내에서 결정

Global Scheduling
* 운영체제가 쓰레드의 존재를 알고, 어떤 쓰레드를 스케줄할지 결정

## __🦈 CPU 스케줄링 알고리즘 평가__

1. Queueing models
* 확률 분포로 주어지는 arrival rate(job의 도착율) & service rate(CPU의 처리율)를 통해 계산

2. Implementation(구현) & Measurement(성능 측정)
* 실제 시스템에 스케줄링 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능을 측정 비교
* -> 너무 어렵.. -> 시뮬레이션으로 대체

3. Simulation(모의 실험)
* 알고리즘을 모의 프로그램으로 작성 후 신빙성 있는 trace를 인풋으로 하여 결과 비교
