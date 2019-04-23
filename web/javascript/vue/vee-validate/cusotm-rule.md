[](# 커스톰 룰 만들기

이것은 기본 validator form이다. 이것은 기본적으로 boolean 또는 promise를 반환한다.
```
const validator = (value, args) => {
  // Return a Boolean or a Promise that resolves to a boolean.
};
```

Object form
```
const validator = {
  getMessage(field, args) {
    // will be added to default locale messages.
    // Returns a message.
  },
  validate(value, args) {
    // Returns a Boolean or a Promise that resolves to a boolean.
  }
};
```



# 커스톰 룰 사용하기

```javascript
import { Validator } from 'vee-validate';

Validator.extend('truthy', {
  getMessage: field => 'The ' + field + ' value is not truthy.',
  validate: value => !! value
});

let instance = new Validator({ trueField: 'truthy' });

// Also there is an instance 'extend' method for convenience.
instance.extend('falsy', (value) => ! value);

instance.attach({
  name: 'falseField',
  rules: 'falsy'
});
```


# 참고
https://baianat.github.io/vee-validate/guide/custom-rules.html#creating-a-custom-rule)