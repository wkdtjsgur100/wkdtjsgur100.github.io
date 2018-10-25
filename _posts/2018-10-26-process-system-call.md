---
layout: post
section-type: post
title: process 관련 system call 개념과 예제 (fork(), wait(), getpid(), getppid())
category: os
tags: [ 'os' ]
comments: true
---

# 각 system call에 대한 간단한 설명

- `fork()` system call은 **새로운 프로세스를 생성** 할 때 사용된다.
- `wait()` system call은 **자식 프로세스가 끝날때 까지 기다릴 때** 사용된다.
- `getpid()` system call은 **현재 프로세스의 id** 를 가져온다.
- `getppid()` system call은 **부모 프로세스의 id(parent pid)** 를 가져온다.

# 예제

``` c
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main()
{
    pid_t returned_pid;
    printf("Hello, my pid is %d\n", getpid());

    if((returned_pid=fork()) < 0){
        perror("fork error"); exit(1);
    }
    else if(returned_pid == 0){
        printf("child: pid = %d, ppid = %d\n", getpid(), getppid());
    }
    else{
        wait(NULL); // 자식 프로세스가 모두 끝날 때까지 기다린다.
        printf("parent: I created child with returned_pid=%d\n", returned_pid);
    }

    printf("Bye, my pid is %d\n", getpid());

    return 0;
}
```

## 위 소스 코드에 대한 설명

먼저, main문이 실행이 되면 Hello, my pid is 부모pid가 출력된다.

- fork() 함수를 호출하는 순간, 자식 프로세스, 부모 프로세스의 분기가 시작되서 하위의 내용을 각각 실행한다.
- fork함수의 반환 값은 자식 프로세스일 경우 0을 반환하고, 부모 프로세스일 경우 0 이상의 값을 반환한다.
- 분기가 시작되었을 경우 자식 프로세스가 먼저 실행될 지, 부모 프로세스가 먼저 실행될 지는 알 수 없다.
- 하지만 wait() system call을 사용했을 경우, 자식 프로세스가 종료될 때까지 부모 프로세스는 기다린다.
- 때문에 child: pid = 자식 프로세스의 pid, ppid = 부모 프로세스의 pid를 출력하고, parent: I created child with pid = 자식 프로세스의 pid이 출력되게 된다.

아래는 이해를 돕기 위한 그림입니다. (부모 프로세스 pid: 135, 자식 프로세스 pid: 136이라 할 때)  

![process](/images/posts/process-split.png)

## 위 소스코드의 수행결과

— 부모 프로세스 pid: 135, 자식 프로세스 pid: 136이라 할 때의 수행 결과

``` cmd
Hello, my pid is 135
child: pid = 136, ppid = 135
Bye, my pid is 136
parent: I created child with pid=136
Bye, my pid is 135
```