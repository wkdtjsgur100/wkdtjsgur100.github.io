---
layout: post
section-type: post
title: redux-form을 사용할 때 유의해야 할 점.
category: redux-form
tags: [ 'redux', 'redux-form' ]
---

redux-form을 사용해서 다음과 같이 renderField를 사용해서 데이터 값을 받았지만,  
Field에 지정한 testname값이 들어오지 않았다.(undefined 값이 출력 됨)

``` js
// Test.js
import TestForm from './TestForm';

class Test extends Component {
    _handleSubmit = (values) => {
        console.log(values.testname); // undefined!!
    }
    render() {
        return(
            <TestForm onSubmit={this._handleSubmit}/>
        );
    }
}

// TestForm.js
const renderField = ({ type, meta: { touched } }) => (
    <div>
        <input type={type} />
    </div>
);
const TestForm = ({handleSubmit}) => (
    <form onSubmit={handleSubmit} >
        <Field
            name="testname"
            component={renderField}
            type="text"
        />
    </form>
)
export default reduxForm({
    form: 'test'
})(TestForm);
```

하나하나 로그를 찍어보니, 위 코드의 문제점은 renderField에 input을 spread operator를 사용해 속성 값을 전달해야 한다는 것을 알았다.  
그래서 TestForm.js 코드의 renderField 부분을 다음과 같이 수정하면 문제가 해결되었다.

``` js
const renderField = ({ input, type, meta: { touched } }) => (
    <div>
        <input {...input} type={type} /> // 속성 값 전달!
    </div>
);
```
