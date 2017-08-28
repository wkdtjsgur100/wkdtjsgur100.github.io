---
layout: post
section-type: post
title: redux-form의 Field에 default 값을 설정하는 방법
category: redux-form
tags: [ 'redux-form', 'redux' ]
---

redux-form을 사용해서 아래와 같은 간단한 form 코드를 작성했다고 했을 때,  

``` js
// Test.js
import TestForm from './TestForm';

class Test extends Component {
    _handleSubmit = (values) => {
        console.log(values.testname);
    }
    render() {
        return(
            <TestForm onSubmit={this._handleSubmit}/>
        );
    }
}

// TestForm.js
const TestForm = ({handleSubmit}) => (
    <form onSubmit={handleSubmit} >
        <Field
            name="testname"
            component="input"
            type="text"
        />
    </form>
)
export default reduxForm({
    form: 'test'
})(TestForm);
```

 여기서 ```testname```이란 Field에 default 값으로 `test default value` 를 넣고 싶다고 하자.  
그렇다면 간단히 ```TestForm``` 컴포넌트에 initialValues를 props로 전달하면 된다. 즉,

``` js
<TestForm onSubmit={this._handleSubmit}/>
```

이 코드를


로 변경하면 된다.
