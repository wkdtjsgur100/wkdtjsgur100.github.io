---
layout: post
section-type: post
title: (C++) 합병 정렬(Merge Sort) 구현
category: algorithm
tags: [ 'algorithm' ]
comments: true
---

``` cpp
#include <iostream>

using namespace std;

int arr[] = {3,1,4,2,5,6,9,7,8};
int len = sizeof(arr)/sizeof(int);
int *b = new int[len]; //합병을 위한 임시 배열

void Merge(int low, int mid, int high)
{
    int h = low, j = mid+1;
    int i = low;

    // 하나씩 비교하며 작은 것 순서대로 b배열에 넣어줍니다.
    while( h <= mid && j <= high)
    {
        if(arr[h] < arr[j])
            b[i] = arr[h++];
        else
            b[i] = arr[j++];
        i++;
    }

    // 한쪽 배열 탐색이 다 끝나면, 나머지 배열을 끝까지 채워 넣어줍니다.
    if( h > mid )
    {
        for(int k=j;k<=high;k++)
            b[i++] = arr[k];
    }
    else
    {
        for(int k=h;k<=mid;k++)
            b[i++] = arr[k];
    }
    // 정렬된 결과를 arr[low:high]까지 넣어줍니다. 키를 포함하는 레코드의 크기가 큰 경우 많은 시간을 소요합니다.
    for(int k=low; k<=high;k++)
        arr[k] = b[k];
}
void MergeSort(int low, int high)
{
    if(low < high)
    {
        int mid = (low + high)/2;
        //divide
        MergeSort(low, mid);
        MergeSort(mid+1, high);

        //conquer
        Merge(low, mid, high);
    }
}
int main()
{
    MergeSort(0, len-1);

    for(int i=0;i<len;i++)
        cout<<arr[i]<<" ";
    cout<<endl;

    return 0;
}
```
