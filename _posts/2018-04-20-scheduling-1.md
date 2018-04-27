---
layout: post
section-type: post
title: 운영체제(Operating System) 핵심 개념 정리 (4) - Scheduling(Overview)
category: os
tags: [ 'os' ]
comments: true
---

# Scheduling 이란(Process Scheduling)

객체가 여러개가 있고, 이 객체들이 자원을 동시에 요구하는데 자원이 제한되어있다. 그러면 제한된 자원들을 어떻게 나눠줄 것인지에 대한 정책

# Workload(부하)

- 행해져야 할 일의 양
- 프로세스의 세트들이 얼마나 많은 자원들을 요구하느냐.

## workload에서 고려되는 프로세스에 대한 가정

- 모든 프로세스는 같은 시간에 시작되고, 같은 시간 동안 수행된다.
- 한번 시작되면, 각각의 프로세스는 완료까지 실행된다.
- 모든 프로세스는 CPU만 사용한다고 가정.
- 각 프로세스의 수행시간을 미리 알고있다고 가정.

# Metric

workload를 측정하는 기준을 말한다.
- Turnaround Time: 종료시간에서 도착시간을 뺀 값
- Response Time: 처음에 실행된 시간에서 도착 시간을 뺀 값
- 그 밖에도 공평성(Fairness), 처리량(Throughput), 최종 기한(Deadline) 등이 있다.

# Preemptive Scheduling(선점형 스케줄링)

프로세스를 중간에 멈추고 다른 프로세스가 중간에 끼어들 수 있는 스케줄링 방식입니다. `context switch`가 요구됩니다.

# Non-preemptive Scheduling(비선점형 스케줄링)

프로세스가 한번 스케줄 되면 그 프로세스가 끝날때까지 다른 프로세스가 끼어들 수 없는 스케줄링 방식입니다.

# Ready Queue

ready queue에는 프로세스 상태 중에서 ready 상태에 있는 프로세스들, 즉 **메모리에 load 되어있는 프로세스들이 쌓여 있습니다.**

> 프로세스의 상태에 대해서 잘 모른다면 [이 링크](https://wkdtjsgur100.github.io/os-summary-process)를 참고하자.

# Scheduling의 종류

- FIFO(First In, First Out): 먼저 들어온 프로세스를 먼저 스케줄. 비선점형 스케줄링
- SJF(Shortest Job First): 들어온 프로세스 중 가장 짧은 프로세스를 스케줄. 비선점형 스케줄
- STCF(Shortest Time to Completion First): SJF의 단점을 보완한 스케줄. 선점형 스케줄링
- RR(Round Robin): Response Time을 최소화 하기 위해 time quantum 만큼 돌아가며 스케줄.
- MFQ(Multilevel Feedback Queue): 선점형 스케줄링. 여러 개의 ready queue를 사용해서, queue마다 priority(우선순위) 와 time quantum을 할당하고 프로세스가 들어오면 가장 상위 queue에 넣었다가, time quantum이 지나면 하위의 queue로 끌어내리는 스케줄링 방식이다. 가장 하위의 queue에서는 RR방식으로 스케줄링한다. 일정 시간동안 수행되지 못하고 남아있는 프로세스는 다시 상위 큐로 끌어올려준다.
- Lottery: 선점형 스케줄링. 각각의 프로세스에 티켓을 부여하고, 매번 투표를 해 승자를 스케줄링하는 방식이다. MFQ의 단점인 기아상태를 해결할 수 있다.
- Stride: 비결정적인(Non-deterministic) Lottery에 반해서 결정적인(deterministic)스케줄링 방식. 티켓 수에 역수를 취한 값을 stride라 하고, stride 값에 따라 pass 값을 갱신하고 pass 값에 따라 스케줄링 하는 방식.

# Multiprocessor에서의 Scheduling

- Multiprocessor: 말그대로 프로세서가 여러개가 있는 것. physically한 프로세서.
- Multi Core: 프로세서 내부에 코어가 여러개가 있을 수 있음.
- Hyper Thread: 코어 내부에 하드웨어적으로 스레드를 두개 이상 지원할 수 있는데 이를 하이퍼 스레드, CPU가 두개로 뻥튀기 되어 보이게 됨.
- CPU cache(L1,L2,LLC)
    - Temporal locality(시간 지역성): 어떤 데이터에 접근했을 때, 이는 곧 나중에 다시 접근될 것이다.(stack, for 반복문 같이..)
    - Spatial locality(공간 지역성): array나 sequential execution 같이 데이터가 한번 접근되면 줍ㄴ에 있는 데이터도 접근될 것이다.
    - 장점: main memory보다 접근이 빨라서 금방 다시 접근할 수 있는 cache hit의 효과가 있다.
    - Multiprocessor 구조에선 더 복잡한데, cache 사이에서의 일관성을 유지하기가 어렵다. 그래서 Bus snooping을 사용해 주소 버스를 모니터링하고 캐시 상의 메모리 접근을 체크해 일관성을 유지한다.

## Cache affinity Scheduling

프로세스를 실행 할 때, 이전에 실행했던 프로세스를 동일한 CPU에서 실행하는 것이 더 낫다(cache hit때문에). 이를 활용한 스케줄링 방식.

### SQMS(Single Queue Multiprocessor Scheduling)

A~E가 single queue에 있다고 할 때, cache affinity를 고려하지 않으면 아래와 같은 스케줄링이 됩니다.

``` text
A E D C B . . .    
B A E D C . . .   
C B A E D . . .   
D C B A E . . .   
```
cache affinity를 고려해 다음과 같은 스케줄링을 해주면 더 효율적이게 됩니다.

``` text
A E A A A . . .  
B B E B B . . .  
C C C E C . . .  
D D D D E . . .  
```

간단한 스케줄링이지만, 위와 같이 E 프로세스를 affinity에 잘 활용할 복잡한 메커니즘이 필요한 것을 알 수 있습니다.

### MQMS(Multi Queue Multiprocessor Scheduling)

Queue를 여러개 만들어서, 각각의 큐를 각각의 CPU에 사용하는 스케줄링입니다.  
예를 들어, queue 2개가 `Q0 -> A -> C`, `Q1 -> B -> D`처럼 있다고 했을 때, 각각의 CPU는 다음과 같이 스케줄 될 수 있습니다.

``` text
CPU0 | A A C C A A C C A A C C . . .   
CPU1 | B B D D B B D D B B D D . . .  
```

cache affinity에 효율적이고 lock contention은 적지만 위의 예에서 A와 C 프로세스가 먼저 끝이 났을 경우에 CPU0이 놀고 있을 경우가 발생합니다. 이를 방지하기 위해 다시 재 분배해 주는 `load balancing(migration 이라고도 함)`이라는 기법이 사용됩니다.

# 운영체제 관련 다른 글들

- [운영체제(Operating System) 핵심 개념 정리 (1)](https://wkdtjsgur100.github.io/os-summary)
- [운영체제(Operating System) 핵심 개념 정리 (2)](https://wkdtjsgur100.github.io/os-summary-2)
- [운영체제(Operating System) 핵심 개념 정리 (4) - scheduling(Overview)](https://wkdtjsgur100.github.io/os-summary-process)
