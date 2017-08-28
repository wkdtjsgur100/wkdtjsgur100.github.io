---
layout: post
section-type: post
title: TensorFlow를 다뤄보자(inflearn)
category: machine-learning
tags: [ 'tensorflow', 'machine-learning', 'inflearn' ]
---

# Tensorflow

## 설치

일단 [virtual-env 및 autoenv](https://cjh5414.github.io/python-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD%EA%B5%AC%EC%B6%95/) 를 설정해주자.

```
pip install tensorflow
pip install tensorflow-gpu # GPU 사용가능 시 속도를 더 빠르게 해줌.
```

## 첫 번째 프로그램

tensorflow를 설치 하고, 아래의 프로그램을 실행해보자.(HelloWorld 출력)

``` python
import tensorflow as tf

hello = tf.constant('Hello World!')

sess = tf.Session()

print (sess.run(hello))
```
나같은 경우엔 아래와 같은 에러가 떴는데,

```
...
Error importing tensorflow.  Unless you are using bazel,
you should not try to import tensorflow from its source directory;
please exit the tensorflow source tree, and relaunch your python interpreter
from there.
```

구글링을 통해 아래와 같은 방법으로 해결하였다.  
![StackOverflow](/images/posts/stackoverflow.png)

출처:[StackOverflow](https://github.com/tensorflow/tensorflow/issues/1244)  

!! note
 TensorFlow는 변수에 값을 저장하는 것이 아니라 operation 정보를 저장하고, 실행 중에 그 값이 결정되도록 한다.

 예를 들어, ``` c = a + b ```라고 하면 c에 a와 b를 더한 값이 들어가는 것이 아니라, 더하는 연산에 대한 정보가 들어가게 된다.

## TensorFlow를 사용한 Linear Regression

Linear Regression의 cost function:
![cost](/images/posts/cost.png)  
이 cost function을 최소화 하는 것이 목표이다.
