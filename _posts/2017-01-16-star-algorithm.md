---
layout: post
section-type: post
title: (ALGO) 백준 2447번 문제
category: algorithm
tags: [ 'algorithm', 'baekjoon', 'C++']
---

문제 링크 : <a>https://www.acmicpc.net/problem/2447</a>

<img alt="star" src = "/images/posts/star.png"/>

규칙을 잘 살펴보면, 3^N*3^N의 * 그림에서 가운데에 구멍을 뚫는(공백을 넣는) 작업을 하고, 가운데 구멍을 제외한 8개의 구간에서 똑같은 작업을 하는 것을 알 수 있다.

똑같은 그림을 그리는 작업을 반복하기 때문에, 배열을 사용했다면 시간을 더 줄일 수 있지 않았을까 하는 아쉬움이 남는다.

아래는 전체 소스이다.

```cpp
#include <iostream>

using namespace std;

void star(int modN, int i,int j)
{
  if( (i / modN) % 3 == 1 && (j / modN) % 3 == 1)
      printf(" ");
  else
  {
      if(modN/3 == 0)
          printf("*");
      else
          star(modN/3,i,j);
  }
}
int main() {
  int N;
  cin>>N;

  for(int i=0;i<N;i++)
  {
      for(int j=0;j<N;j++)
          star(N,i,j);
      printf("\n");
  }
  return 0;
}
```
