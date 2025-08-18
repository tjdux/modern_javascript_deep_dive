## `this`

- 객체 (object)
  - 상태를 나타내는 프로퍼티, 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조
  - 동작을 나타내는 메서드는 자신이 속한 객체의 프로퍼티를 참조하고 변경할 수 있어야 함 ➡️ **자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 함**
- 객체 리터럴의 방식의 경우: 메서드 내부에서 자신이 속한 객체를 가리키는 식별자를 재귀적으로 참조 가능

```js
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * circle.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

- 🙅‍♀️ 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지 않으며 바람직하지 않음

```js
function Circle(radius){
  ???.radius = radius;
}

Circle.prototype.getDiameter = function(){
  return 2 * ???.radius;
}

const circle = new Circle(5);
```

- `this`
  - **자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수**
  - **`this`를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드 참조 가능**
  - **`this` 바인딩은 함수 호출 방식에 의해 동적으로 결정**

```js
// 1️⃣ 객체 리터럴
const circle = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10

// 2️⃣ 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius;
};

const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

- **JS의 `this`는 함수가 호출되는 방식에 따라 `this` 바인딩이 동적으로 결정됨**

```js
// 1️⃣ 전역
console.log(this); // window

// 2️⃣ 일반 함수
function square(number) {
  console.log(this); // window
  return number * number;
}

// 3️⃣ 객체 리터럴
const person = {
  name: "Park",
  getName() {
    console.log(this); // {name: 'Park', getName: ƒ}
    return this.name;
  },
};
console.log(person.getName());

// 4️⃣ 생성자 함수
function Person(name) {
  this.name = name;
  console.log(this); // Person {name: 'Park'}
}

const me = new Person("Park");
```

- 일반적으로 `this`는 메서드 내부 또는 생성자 함수 내부에서만 의미가 있음 ➡️ `strict mode`에서는 `undefined`에 바인딩
