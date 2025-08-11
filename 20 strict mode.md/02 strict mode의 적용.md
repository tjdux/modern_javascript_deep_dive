## strict mode의 적용

- 전역의 선두 또는 함수 몸체의 선두에 `use strict` 추가

```js
"use strict";

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

```js
function foo() {
  "use strict";
  x = 10; // ReferenceError: x is not defined
}
foo();
```
