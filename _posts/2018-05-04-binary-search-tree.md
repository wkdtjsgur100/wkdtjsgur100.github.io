---
layout: post
section-type: post
title: (C언어) 이진탐색트리(BST, Binary Search Tree) 구현
category: data structure
tags: [ 'data structure' ]
comments: true
---

# 이진 트리

이진 트리에는 완전 이진 트리, 포화 이진 트리 등 다양한 종류의 트리가 있다. 하지만 이진 트리의 기본적인 속성으로는 **왼쪽 자식 노드의 값이 부모 노드보다 더 작은 값을 가지고, 오른쪽 자식 노드의 값이 부모 노드보다 더 큰 값을 가지는 트리** 를 말한다.

## 탐색

트리의 루트 노드 부터 순회하며 탐색 해야 하는 값이 루트 노드보다 클 경우 오른쪽 자식을 방문, 작을 경우 왼쪽 자식을 방문하고, 같을 경우 탐색을 마친다. 방문한 노드가 NULL이 될 때까지 탐색 값을 찾지 못하면, 해당 트리에 탐색 값이 없는 것으로 간주된다.  

## 삽입

마찬가지로 루트 노드에서부터 탐색하며 삽입하는 노드의 값이 현재 노드 보다 더 크면 오른쪽 자식을 방문, 작으면 왼쪽 자식을 방문하는 것을 반복하다가 NULL을 만나면 해당 위치에 노드를 삽입하고 종료한다.

## 삭제

삭제 부분이 좀 복잡한데, 트리를 순회 중 삭제해야 하는 값을 발견하면 해당 노드를 삭제한다. 그러면 그 자리에 트리에 있는 노드들 중 다른 노드를 대체해줘야 한다. 현재 노드를 기준으로 오른쪽 자식트리의 모든 노드는 현재 노드 보다 크고, 왼쪽 자식트리의 모든 노드는 현재 노드 보다 작으므로, **오른쪽 자식 트리 중에서 가장 작은 값을 가지는 노드를 대체해주면** 이진 트리의 속성이 유지되는 것을 알 수 있다.  

특정 트리에서 최소 값은 어떻게 찾을 수 있을까. 바로 가장 왼쪽에 있는 자식이 가장 최소값이다. 따라서 root가 주어졌을 때 최소 값을 찾는 코드는 다음과 같이 작성할 수 있다.

``` cpp
Node* findMinNode(Node* root)
{
    Node* tmp = root;
    while(tmp->leftChild != NULL)
        tmp = tmp->leftChild;
    return tmp;
}
```

# 시간 복잡도

시간 복잡도는 삽입, 삭제, 탐색 모두 최선의 경우(트리의 균형이 잘 잡혀있는 경우, 즉, 왼쪽 오른쪽 자식이 잘 분배되어 있는 경우) `O(logN)`의 시간복잡도를 가지고, 최악의 경우 (자식 노드가 한쪽으로 쏠려 있는 경우) `O(N)`이다.

# 구현

``` c
#include <stdio.h>
#include <stdlib.h>

typedef struct NodeStruct
{
    int value;
    struct NodeStruct* leftChild;
    struct NodeStruct* rightChild;
}Node;

Node* root = NULL;

Node* BST_insert(Node* root, int value)
{
    if(root == NULL)
    {
        root = (Node*)malloc(sizeof(Node));
        root->leftChild = root->rightChild = NULL;
        root->value = value;
        return root;
    }
    else
    {
        if(root->value > value)
            root->leftChild = BST_insert(root->leftChild, value);
        else
            root->rightChild = BST_insert(root->rightChild, value);
    }
    return root;
}
Node* findMinNode(Node* root)
{
    Node* tmp = root;
    while(tmp->leftChild != NULL)
        tmp = tmp->leftChild;
    return tmp;
}
Node* BST_delete(Node* root, int value)
{
    Node* tNode = NULL;
    if(root == NULL)
        return NULL;

    if(root->value > value)
        root->leftChild = BST_delete(root->leftChild, value);
    else if(root->value < value)
        root->rightChild = BST_delete(root->rightChild, value);
    else
    {
        // 자식 노드가 둘 다 있을 경우
        if(root->rightChild != NULL && root->leftChild != NULL)
        {
            tNode = findMinNode(root->rightChild);
            root->value = tNode->value;
            root->rightChild = BST_delete(root->rightChild, tNode->value);
        }
        else
        {
            tNode = (root->leftChild == NULL) ? root->rightChild : root->leftChild;
            free(root);
            return tNode;
        }
    }

    return root;
}
Node* BST_search(Node* root, int value)
{
    if(root == NULL)
        return NULL;

    if(root->value == value)
        return root;
    else if(root->value > value)
        return BST_search(root->leftChild, value);
    else
        return BST_search(root->rightChild, value);
}
void BST_print(Node* root)
{
    if(root == NULL)
        return;

    printf("%d ", root->value);
    BST_print(root->leftChild);
    BST_print(root->rightChild);
}

int main()
{
    root = BST_insert(root, 5);
    root = BST_insert(root, 3);
    root = BST_insert(root, 7);
    root = BST_insert(root, 1);
    root = BST_insert(root, 9);
    root = BST_insert(root, 6);

    root = BST_delete(root, 7);

    BST_print(root);
}
```
