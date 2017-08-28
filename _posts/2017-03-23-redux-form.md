---
layout: post
section-type: post
title: Redux-form 시작하기
category: react
tags: [ 'react', 'redux', 'redux-form' ]
---

> Redux store를 통해 React Component 내 form 데이터를 관리해주는 라이브러리인 [redux-form 공식 문서](http://redux-form.com/6.5.0/docs/GettingStarted.md/)를 보고 번역하였음.

## redux-form 시작하기

redux-form은 주로 다음의 네 가지로 구성됩니다.

1. redux에서 form state를 유지하기 위한 'redux-form 액션'들을 전송하길 기다리는 redux reducer
2. 높은 우선순위의 컴포넌트(Higher Order Component,HOC)에서 전체 form을 감싸고, props를 통해서 기능들을 제공하는 리액트 컴포넌트 데코레이터
3. 개개의 필드가 redux store에 입력하는 것을 연결하는 Field component
4. 애플리케이션을 통해 form과 상호작용하기 위한 다양한 Action Creator들

## 구현 가이드

### Step 1

가장 먼저 해야할 일은 redux에 redux-form reducer를 전달하는 것입니다.  
얼마나 많은 form 컴포넌트가 앱에 사용되는가에 관계없이 이 작업은 한번만 하면 됩니다.

``` js
import { createStore, combineReducers } from 'redux'
import { reducer as formReducer } from 'redux-form'

const reducers = {
  // 여기에 다른 reducer들이 들어갑니다..
  form: formReducer     // <---- 'form'에서 마운트 되었습니다.
}
const reducer = combineReducers(reducers)
const store = createStore(reducer)
```

### Step 2

reduxForm()으로 form component를 decorate합니다.  
이것은 form 상태에 대한 정보와 form을 전송하는 기능들을 제공하는 props와 함께 컴포넌트를 전달합니다.  
각각의 입력 컴포넌트는 Field 컴포넌트의 component prop 내에 위치되어야 합니다.  
Field 컴포넌트는 value, onChange, onBlur 같은 props를 React.DOM.input component에 전달해서    
이 값들을 채우고 변경사항들을 수신합니다.  

``` js
import React, { Component } from 'react';
import { Field, reduxForm } from 'redux-form';

class ContactForm extends Component {
  render() {
    const { handleSubmit } = this.props;
    return (
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="firstName">First Name</label>
          <Field name="firstName" component="input" type="text"/>
        </div>
        <div>
          <label htmlFor="lastName">Last Name</label>
          <Field name="lastName" component="input" type="text"/>
        </div>
        <div>
          <label htmlFor="email">Email</label>
          <Field name="email" component="input" type="email"/>
        </div>
        <button type="submit">Submit</button>
      </form>
    );
  }
}

// Decorate the form component
ContactForm = reduxForm({
  form: 'contact' // a unique name for this form
})(ContactForm);

export default ContactForm;
```

### 끝!

여전히 해야 할 것이 있다:

- 전송된 데이터롤 무언가 해보자. onSubmit function에 JSON 형태로 데이터가 전달될 것이다.

``` js
import ContactForm from './ContactForm';

class ContactPage extends React.Component {
  handleSubmit = (values) => {
    // Do something with the form values
    console.log(values);
  }
  render() {
    return (
      <ContactForm onSubmit={this.handleSubmit} />
    );
  }
}
```

- 어떤 경우에는 초기에 initialValues prop으로 form 값들을 초기화 시켜야 할 수도 있다.

redux-form으로 시작하기 외에 다른 걸 하고 싶다면, redux-form에 입력을 연결하는 방법을 배우는 좋은 방법은 [Simple Form 예제](https://redux-form.com/6.5.0/examples/simple)를 통해 배울 수 있다.
