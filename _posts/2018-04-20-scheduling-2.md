---
layout: post
section-type: post
title: 스케줄링 - FIFO, SJF 개념
category: os
tags: [ 'os' ]
comments: true
---

> 스케줄링에 대한 전반적인 개론을 보고 싶다면 [이 링크](https://wkdtjsgur100.github.io/scheduling-1/)를 먼저 보고 오는 것을 추천합니다.

# FIFO(First In First Out)

FCFS(First Come First Serve)라고도 불린다. 말 그대로 먼저 들어온 프로세스를 먼저 스케줄 하겠다는 뜻입니다. 비선점형(Non-preemptive) 스케줄링입니다. 예를 들어, 아래 표와 같이 동시에 A, B, C 프로세스가 순서대로 도착했다고 가정해봅시다.(즉, Ready Queue에 A, B, C 순서대로 들어왔다고 가정합니다.)

|     | Arrival Time | Service Time |
| :-- | :----------: | :----------: |
| A   |            0 |            6 |
| B   |            0 |            2 |
| C   |            0 |            3 |

위는 아래 표와 같이 스케줄링 됩니다.  

| Time Elapsed |1|2|3|4|5|6|7|8|9|10|11|
| ------------ |-|-|-|-|-|-|-|-|-|-|-|
| Scheduled Process |A|A|A| A| A| A| B| B| C|C|C|

## ATT(Average Turnaround Time), ART(Average Response Time)

- ATT: A는 0에 도착했지만 6에 끝났고, B는 0에 도착했지만 8에 끝났으며, C는 0에 도착했지만 11에 끝났으므로 **( (6-0) + (8-0) + (11-0) ) / 3 = 25/3**
- ART: A는 0에 도착했고 0에 바로 스케줄 되었으며, B는 6에 스케줄 되기 시작했고, C는 8에 스케줄 되기 시작 했으므로 **( (0-0) + (6-0) + (8-0) ) / 3 = 14/3**

# SJF(Shortest Job First)

SPN(Shortest Process Next)라고도 불립니다. 말 그대로 ready queue에 있는 프로세스 중 가장 Service Time이 짧은 프로세스를 스케줄링 합니다. 비선점형(Non-preemptive) 스케줄링 입니다.  
FIFO로 스케줄링 했을 때 보다 SJF로 스케줄링을 했을 때 ATT와 ART 둘 다 감소 시킬 수 있다는 것을 알 수 있습니다.  
FIFO와 같은 상황에서 SJF에서는 다음과 같이 스케줄링 됩니다.  

| Time Elapsed |1|2|3|4|5|6|7|8|9|10|11|
| ------------ |-|-|-|-|-|-|-|-|-|-|-|
| Scheduled Process |B|B|C| C| C| A| A| A| A|A|A|

수학적으로 최적화 되었다고 증명되어 있지만, B 프로세스가 약간 늦게 도착했을 경우 여전히 A 프로세스를 먼저 스케줄 한다는 단점이 있습니다.

## ATT(Average Turnaround Time), ART(Average Response Time)

- ATT: ( 2 + 5 + 11 ) / 3 = 6
- ART: ( 0 + 2 + 5 ) / 3 = 7/3
