## 함수 호출 방식과 `this` 바인딩

- **`this` 바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정**

### 일반 함수 호출

- **기본적으로 `this`에는 전역 객체가 바인딩됨**

```js
function foo() {
  console.log(`foo's this: ${this}`); //foo's this: [object Window]
  function bar() {
    console.log(`bar's this: ${this}`); //bar's this: [object Window]
  }
  bar();
}

foo();
```

- `strict mode`가 적용된 일반 함수의 내부 `this`에는 `undefined`

```js
function foo() {
  "use strict";

  console.log(`foo's this: ${this}`); //foo's this: undefined
  function bar() {
    console.log(`bar's this: ${this}`); //bar's this: undefined
  }
  bar();
}

foo();
```

- 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 `this`에는 전역 객체가 바인딩

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(`foo's this: ${this}`); // {value: 100, foo: ƒ}
    console.log(`foo's this.value: ${this.value}`); // 100

    function bar() {
      console.log(`bar's this: ${this}`); // window
      console.log(`bar's this.value: ${this.value}`); // 1
    }

    bar();
  },
};

obj.foo();
```

- 콜백 함수가 일반 함수로 호출된다면 콜백 함수 내부의 `this`에도 전역 객체가 바인딩

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(`foo's this: ${this}`); // {value: 100, foo: ƒ}

    setTimeout(function () {
      console.log(`callback's this: ${this}`); // window
      console.log(`callback's this.value: ${this.value}`); // 1
    }, 100);
  },
};

obj.foo();
```

- **일반 함수로 호출된 모든 함수 (중첩 함수, 콜백 함수 포함) 내부의 `this`에는 전역 객체가 바인딩**
- 🤷‍♀️ 하지만 매서드 내의 중첩 함수 또는 메서드에게 전달한 콜백 함수의 `this`가 전역 객체에 바인딩되는 것은 문제가 있음 ⬅️ 중첩 함수 또는 콜백 함수는 헬퍼 함수로 동작하는 경우가 대부분이기 때문

#### 중첩 함수나 콜백 함수의 `this` 바인딩 일치시키기 위한 방법

```js
// 1️⃣ this 할당
var value = 1;

const obj = {
  value: 100,
  foo() {
    const that = this;

    setTimeout(function () {
      console.log(that.value);
    }, 100);
  },
};

obj.foo(); // 100
```

```js
// 2️⃣ this 명시적 바인딩
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(
      function () {
        console.log(this.value);
      }.bind(this),
      100
    );
  },
};

obj.foo(); // 100
```

```js
// 3️⃣ 화살표 함수
var value = 1;

const obj = {
  value: 100,
  foo() {
    setTimeout(() => console.log(this.value), 100);
  },
};

obj.foo(); // 100
```

<br/>

### 메서드 호출

- **메서드 내부의 `this`는 메서드를 소유한 객체가 아닌 메서드를 호출하는 객체에 바인딩됨**

```js
const person = {
  name: "Park",
  getName() {
    return this.name;
  },
};

console.log(person.getName()); // Park

const anotherPerson = {
  name: "Kim",
};

anotherPerson.getName = person.getName;

console.log(anotherPerson.getName()); // Kim

const getName = person.getName;

console.log(getName()); // ''
// this === globalThis
```

- 💡 메서드는 프로퍼티에 바인딩된 함수 ➡️ person 객체의 getName 프로퍼티가 가리키는 함수 객체는 person 객체에 포함된 것이 아니라 독립적으로 존재하는 별도의 객체, getName 프로퍼티가 함수 객체를 가리키고 있는 것
- 메서드 내부의 `this`는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고 메서드를 호출한 객체에 바인딩됨
- 프로토타입 메서드 내부에서 사용된 `this`도 일반 메서드와 마찬가지로 해당 메서드를 호출한 객체에 바인딩

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Park");

console.log(me.getName()); // Park;

Person.prototype.name = "Kim";

console.log(Person.prototype.getName()); // Kim
```

<br/>

### 생성자 함수 호출

- 생성자 함수 내부의 `this`에는 생성자 함수가 생성할 인스턴스가 바인딩됨

```js
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20

// new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수가 아니라 일반 함수로 동작
const circle3 = Circle(15);
console.log(circle3); // undefined
console.log(radius); // 15
```

<br/>

### `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출

- `Function.prototype`의 메서드로 모든 함수가 상속받아 사용 가능

#### `Function.prototype.apply`, `Function.prototype.call`

```js
/**
 * 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출
 * @param thisArg - this로 사용할 객체
 * @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
 * @returns 호출된 함수의 반환값
 */
Function.prototype.apply(thisArg[, argsArray])

/**
 * 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출
 * @param thisArg - this로 사용할 객체
 * @param arg1, arg2, ... - 함수에게 전달할 인수 리스트
 * @returns 호출된 함수의 반환값
 */
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

```js
function getThisBinding() {
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding()); // window

console.log(getThisBinding.apply(thisArg)); // { a: 1 }
console.log(getThisBinding.call(thisArg)); // { a: 1}
```

- **`apply`와 `call` 메서드의 본질적인 기능은 함수를 호출하는 것**
- 함수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 `this`에 바인딩
- 호출할 함수에 인수를 전달하는 방식만 다를 뿐 동일하게 동작
  - `apply`: 호출할 함수의 인수를 배열로 묶어 전달
  - `call`: 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달

```js
function getThisBinding() {
  console.log(arguments);
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// { a: 1 }

console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// { a: 1 }
```

- 대표적인 용도: `arguments` 객체와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우 (`arguments`) 객체는 배열이 아니기 때문에 `Array.prototype.slice`와 같은 메서드를 사용할 수 없으나 `apply`, `call` 메서드를 이용하면 가능

```js
function convertArgsToArray() {
  console.log(arguments);

  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

#### `Function.prototype.bind`

- `apply`, `call`과 달리 함수를 호출하지 않음
- 첫 번째 인수로 전달한 값으로 `this` 바인딩이 교체된 함수를 새롭게 생성해 반환

```js
function getThisBinding() {
  return this;
}

const thisArg = { a: 1 };

console.log(getThisBinding.bind(thisArg));
// ƒ getThisBinding() {
//   return this;
// }
console.log(getThisBinding.bind(thisArg)());
// { a: 1 }
```

- 메서드의 `this`와 메서드 내부의 중첩 함수 또는 콜백 함수의 `this`가 불일치하는 문제를 해결하기 위해 유용하게 사용됨

```js
const person = {
  name: "Park",
  foo(callback) {
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  console.log(`Hi! My name is ${this.name}.`); // Hi! My name is Park.
});
```
