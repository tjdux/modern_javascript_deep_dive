## strict mode가 발생시키는 에러

### 암묵적 전역

```js
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
```

## 변수, 함수, 매개변수의 삭제

```js
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError: Delete of an unqualified identifier in strict mode

  function foo(a) {
    delete a; // SyntaxError: Delete of an unqualified identifier in strict mode
  }
  delete foo; // SyntaxError: Delete of an unqualified identifier in strict mode
})();
```

### 매개변수 이름의 중복

```js
(function () {
  "use strict";

  // SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

### with 문의 사용

- `with` 문
  - 전달된 객체를 스코프 체인에 추가
  - 낮은 성능과 가독성
  - 사용 지양

```js
(function () {
  "use strict";

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
});
```
