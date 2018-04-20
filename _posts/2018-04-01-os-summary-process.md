---
layout: post
section-type: post
title: 운영체제(Operating System) 핵심 개념 정리 (3) - Process
category: os
tags: [ 'os' ]
comments: true
---

# Process의 정의

실행 중인 프로그램.

# Process의 구조

다음과 같이 실행을 위한 자원들이 필요하다.
- CPU : PC, SP 같은 레지스터들
- Memory(주소 공간)의 영역들, 낮은 주소 -> 높은 주소 순으로 작성
	- Text 영역: 프로그램 코드들
	- Data 영역: 전역 변수들
	- Heap 영역: 동적할당 된 것들.
	- Stack 영역: 지역 변수, 매개변수 등..
- I/O 정보: 장치를 포함한 open된 파일들

# Process API

프로그램은 어떻게 실행되는가

1. Load 합니다: 주소공간에 코드와 static data를 넣는다.   
2. 동적 할당 합니다: Stack, 파라미터 초기화, 필요하면 힙을 사용합니다.
3. 초기화
4. entry point로 점프

# 프로세스의 상태와 정의

![process-status](/images/posts/process-status.png)

- new 상태: 프로세스가 이제 막 만들어진 상태, 메모리에 올라가기 전의 상태이다.
- ready 상태: 프로세스가 메모리로 올라간 상태. CPU에 올라가기 전 상태이다.
- running 상태: `ready` 상태에서 dispatch(스케줄 이라고도 한다)가 되어 CPU를 할당받아 실제 수행되고 있는 상태를 말한다. 그런데 프로세스 하나가 CPU를 독점하는 것을 방지하기 위해, timeout을 시켜 강제로 다시 `ready` 상태로 돌아가게 할 수도 있다. 이를 선점(preemptive) 한다라고도 한다.
- waiting 상태: I/O 또는 이벤트로 인해 잠시 대기상태로 전환된 상태이다. 이벤트가 종료되면, 프로세스는 다시 `ready`상태로 돌아간다.
- terminated 상태: `running` 상태인 프로그램이 종료되면 `terminated` 상태가 된다.
- suspend 상태: `ready` 상태나 `waiting` 상태인 프로세스를 메모리에서 내리는 것이다. `running` 상태에선 suspend 시킬 수 없고, `ready`가 suspend되면 suspend-ready라 하고, `waiting` 상태가 suspend 되면 suspend-wait 상태라 한다.
- resume: `suspend` 상태에서 다시 메모리로 올라가는 것을 resume이라 한다.

# PCB(Process Control Block)

- 프로세스를 관리하기 위해서 하나의 자료구조가 만들어지고 거기에 **프로세스의 정보가 담겨있는 것** 이 생기는데 이를 PCB라 함.
- 프로세스의 상태, pid, PC(Program Counter), CPU 스케줄링 정보등을 가지고 있음.

# Process 관련 system call

- fork(): 새로운 프로세스를 생성. 생성된 프로세스의 id(pid)를 반환한다. 부모 프로세스가 먼저 호출 될 지, 자식 프로세스가 먼저 실행될 지 알 수 없다. 즉, Non-deterministic하다.
- wait(): 자식 프로세스가 끝날 때 까지 wait를 호출한 이후의 작업을 실행시키지 않는 system call. 즉, fork함수를 deterministic하게 바꿔준다. 죽은 자식 프로세스의 pid를 반환한다.  

예를 들어 보자. 아래와 같은 코드가 있다.

``` cpp
// C program to demonstrate working of wait()
#include<stdio.h>
#include<stdlib.h>
#include<sys/wait.h>
#include<unistd.h>

int main()
{
	pid_t cpid;
	if (fork()== 0)
		exit(0);		 
	else
		cpid = wait(NULL);
	printf("Parent pid = %d\n", getpid());
	printf("Child pid = %d\n", cpid);

	return 0;
}
```

이 코드는 무조건 아래와 같은 순서로 결과가 나오게 된다.(pid 값은 변경될 수 있음.)

```
Parent pid = 12345678
Child pid = 89546848
```
- exec(): 코드와 static data를 Load 및 덮어쓰기를 하고, stack과 heap을 초기화 하고 실행합니다. 사용자가 원하는 프로그램을 실행시킬 수 있습니다.
- exec과 fork를 분리한 이유: 자식 프로세스가 끝나기 전 무언가를 하고 싶을 수 있기 때문에
- getpid(): pid를 획득

# Time sharing

프로세서들이 자원을 공유할 수 있도록 하는 시스템. CPU를 독점하는 것 같이 보여줘야 한다.  
CPU의 시간을 작게 분할해 여러 작업을 돌아가면서 수행하는 것. CPU를 가상화하는 핵심 테크닉

## Issues

- 성능: 가상화 오버헤드를 어떻게 최소화 시킬것인가?
- 제어: 중간에 인터럽트가 발생할 때마다 OS는 어떻게 제어할 것인가?

## Solution

### Direct Execution

운영체제를 거치지 않고 직접 수행, 효율적이지만 **컨트롤 할 수 없다. 즉, 프로세스는 무한히 돌 것이고, 자원을 독점해 버릴 것입니다.** 

> 이와 반대되는 개념으로, indirect execution이 있습니다. 효율은 떨어지지만 모든 작업을 할때 운영체제를 거쳐서 실행하는 것입니다.

`Direct Execution`의 절차

1. OS에서 프로세스 리스트에 엔트리를 생성
2. OS에서 프로그램 메모리 할당
3. OS에서 메모리로 로드
4. OS에서 argc/argv를 사용해서 스택 설정
5. OS에서 레지스터 클리어 후 main문 실행
6. 프로그램에서 main문 실행
7. OS에서 프로세스 메모리 해제 및 프로세스 리스트에서 제거

### Direct Execution Solution

Direct Execution의 단점을 해결할 두 가지 해결방안이 있다. 
1. 자원과 관련된 요청이 오면 OS가 개입한다.(system call을 통해)
2. 유저모드와 커널모드를 구분한다. 모드를 변경할 때 `trap` 명령을 사용한다.

#### trap

trap은 `trap table(a.k.a. IDT, Interrupt Descriptor table)`을 사용해서 발생시킵니다.  
`trap table`은 `trap handler`들로 구성이 됩니다. `trap handler`엔 system call handler, div_dy_zero handler, segment fault handler 등이 있습니다. 부팅 시간에 초기화 됩니다.  
  
예를 들어, system call의 처리 절차는 다음과 같습니다.
1. 프로그램(유저 모드)에서 system call trap을 호출합니다.
2. 하드웨어 단에서 kernel stack에 register 값들을 저장하고, 커널 모드로 이동하고, trap handler로 점프합니다.
3. OS 단(커널 모드)에서 trap을 제어하고, system call을 수행한 뒤 다시 되돌아 갑니다.

### 또 다른 OS 개입 방법 - Timer interrupt and Context switch

프로세스가 변경될 때마다 OS가 개입해야하는데, 두가지 방법이 있습니다.  
- 협력적인 방법: system call 을 발생시킵니다.
- 비협력적인 방법
	- timer interrupt 사용. 사람의 심장처럼 주기적으로 timer interrupt를 발생시키고 필요 시 스케줄링을 진행합니다.
	- `Context switch(문맥 교환)`: Context save and restore
	- `Context switch`의 흐름: Process A 실행 -> Process B 도입: A의 context를 저장하고 B의 context를 올림. -> B 종료 -> B의 context를 저장하고 저장했었던 A의 context를 다시 로드.

#### Context switch

context switch(문맥 교환) 은 선점 되었을 때 프로세스의 마지막 상태를 기억합니다.
- Context save: 메모리에 있는 PCB에 CPU register를 담는 것을 말합니다.
- Context restore: CPU register들에 PCB를 로딩하는 것을 말합니다.
- switching하는 동안은 유용한 일을 못하는 overhead가 발생합니다.

# 운영체제 관련 다른 글들

- [운영체제(Operating System) 핵심 개념 정리 (1)](https://wkdtjsgur100.github.io/os-summary)
- [운영체제(Operating System) 핵심 개념 정리 (2)](https://wkdtjsgur100.github.io/os-summary-2)
- [운영체제(Operating System) 핵심 개념 정리 (4) - scheduling(Overview)](https://wkdtjsgur100.github.io/scheduling-1)
	









