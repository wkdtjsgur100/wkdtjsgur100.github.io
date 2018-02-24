---
layout: post
section-type: post
title: 전위순회(preorder), 중위순회(inorder), 후위순회(postorder) 요약
category: algorithm
tags: [ 'algorithm' ]
---

코드로 간단히 설명하면

``` cpp
func(Node* root)
{
	print(root->value); //preorder

    func(root->left);

    print(root->value); //inorder

    func(root->right);

    print(root->value); //postorder
}
```

말로 풀어서 설명하면,  
`root 노드부터, 왼쪽 노드, 오른쪽 노드`는 **preorder**,   
`왼쪽 노드부터, root 노드, 오른쪽 노드`는 **inorder**,   
`왼쪽 노드부터, 오른쪽 노드, root 노드`는 **postorder**.  

그림으로 설명하면  

![small-tree](/images/posts/small-tree.png)  

위와 같은 그림이 있을 때,
- preorder: `0 1 2`
- inorder: `1 0 2`
- postorder: `1 2 0`
