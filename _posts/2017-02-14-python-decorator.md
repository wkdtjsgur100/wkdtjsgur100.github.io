---
layout: post
section-type: post
title: Python Decorator
category: python
tags: [ 'python', 'decorator' ]
---

> 본 문서는 [python wiki](https://wiki.python.org/moin/PythonDecorators#What_is_a_Decorator) 와 [wikipedia](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Decorators)를 참고해 쓴 문서입니다.

# About this page

이 페이지는 python에 데코레이터를 추가하는 과정의 역사가 문서화 되어있습니다.


decorator는 메소드, 클래스, 함수 수정에 사용되는 호출 가능한 python 객체입니다.  
decorator는 정정의된 고유의 객체에 전달되고 정의된 이름에 바인딩 되고 수정된 객체를 반환합니다.  
python docorator들은 Java annotations의 한 부분에서 파생되었고,  
유사한 [syntax](https://en.wikipedia.org/wiki/Syntax_(programming_languages))를 가지고 있습니다;  
decorator syntax는 순수 [syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar)이고, 키워드로 ```@```를 사용합니다.

``` python
@viking_chorus
def menu_item():
    print("spam")
```

는 아래와 같습니다

``` python
def menu_item():
    print("spam")
menu_item = viking_chorus(menu_item)
```

Decorator는 메타 프로그래밍(프로그램을 data처럼 다루는 기술이 포함된 컴퓨터 프로그래밍)의 한 형식입니다;  
이것들이 decorate하는 method나 함수의 수행을 향상시킵니다.  
예를 들어, 아래의 예시에서 ```viking_chorus```는 호출될 때마다 ```menu_item```이 8번 호출됩니다.

``` python
def viking_chorus(myfunc):
    def inner_func(*args, **kwargs):
        for i in range(8):
            myfunc(*args, **kwargs)
    return inner_func
```

함수 데코레이터의 전통적인 사용법들은 클래스 메서드 또는 정적 메서드를 만드는 것, 함수 속성 추가, 추적, precondition 와 postcondition, synchronisation을 설정하는 것 등입니다.  
하지만 이는 훨씬 더 많이 사용될수 있는데, tail recursion elimination, memoization을 포함해서, decorators 작성을 향상 시키는데에도 쓰일 수 있습니다.  
데코레이터는 여러줄을 인접한 줄에 위치하여 연결 시킬 수도 있습니다.

``` python
@invincible
@favorite_color("Blue")
def black_knight():
    pass
```

는 아래와 같습니다.

``` python
def black_knight():
    pass
black_knight = invincible(favorite_color("Blue")(black_knight))
```

또는, 중간에 있는 변수를 사용하는 것과 같습니다.

``` python
def black_knight():
    pass
blue_decorator = favorite_color("Blue")
decorated_by_blue = blue_decorator(black_knight)
black_knight = invincible(decorated_by_blue)
```

위의 예시에 따르면, favorite_color 데코레이터 팩토리(객체지향 프로그래밍에서 다른 객체들을 생성하는 객체)는 매개변수를 가지고 있습니다. 데코레이터 팩토리들은 데코레이터를 반환 해야만 하고, 이는 이 매개변수로서 decorate된 객체로 호출됩니다.

``` python
def favorite_color(color):
    def decorator(func):
        def wrapper():
            print(color)
            func()
        return wrapper
    return decorator
```

그러면 black_knight 함수가 실행되기 전에 "Blue" 같은 black_knight 함수가 출력되는 것을 decorate 합니다. [Closure](https://en.wikipedia.org/wiki/Closure_(computer_programming))는 color 매개변수가 가장 안에있는 wrapper 함수에 접근 가능하게 하는데, 심지어 이 함수가 종료되고 작동하는 데코레이터를 허용하는 스코프를 벗어나도 접근 가능합니다.

파이썬 version 2.6 이전엔, 데코레이터는 함수와 메서드에 요청했지만, 클래스에는 아니었습니다.   
하지만 ```__new__``` 메소드를 데코레이팅 하는 것은 클래스를 수정할 수 있습니다.  
클래스 데코레이터들은 버전 2.6에서부터 지원됩니다.  

이름이 이렇지만, python decorator는 decorator 패턴이 구현(implementation) 된 것은 아닙니다.  
decorator 패턴은 실행 도중에 객체들이 추가 되는 기능을 하게 해주는 객체지향 프로그래밍에서 정형화된 패턴 타입으로 사용되는 디자인 패턴입니다; Python decorator는 함수와 메서드가 definition time에 기능을 더해줍니다.  
그러므로 decorator 패턴 클래스들 보다 더 높은 레벨의 구조라고 볼 수 있습니다.  
데코레이터 패턴은 언어가 duck typed이기 때문에, 파이썬에서 구현 가능한 사소한 것들입니다.  
그래서 데코레이터 패턴은 이와 같이 주로 같은 수준으로 판단되지 않습니다.
