## strict mode 적용에 의한 변화

### 일반 함수의 `this`

- strict mode에서 함수를 일반 함수로서 호출하면 `this`에 `undefined`이 바인딩됨

```js
(function () {
  "use strict";

  function foo() {
    console.log(this);
  }
  foo(); //undefined

  function Foo() {
    console.log(this);
  }
  new Foo(); // Foo
})();
```

### `arguments` 객체

- strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 `arguments` 객체에 반영되지 않음

```js
(function (a) {
  "use strict";

  a = 2;

  console.log(arguments); // {0: 1, length: 1}
})(1);
```
