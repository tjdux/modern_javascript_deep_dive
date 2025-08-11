## instanceof 연산자

```js
객체 instanceof 생성자 함수
```

- **우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true, 그렇지 않은 경우에는 false로 평가**

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Park");

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재
console.log(me instanceof Person); //true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재
console.log(me instanceof Object); //true

// 프로토타입을 교체할 객체
const parent = {};

// 프로토타입 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않음
console.log(Person.prototype === parent); //false
console.log(parent.constructor === Person); //false

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하지 않음
console.log(me instanceof Person); //false
console.log(me instanceof Object); //true

Person.prototype = parent;

console.log(me instanceof Person); //true
console.log(me instanceof Object); //true
```

- `instanceof` 연산자는 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니라 **생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인**

```js
function isInstanceof(instance, constructor) {
  // 프로토타입 취득
  const prototype = Object.getPrototypeOf(instance);

  // 재귀 탈출 조건: prototype이 null이면 프로토타입 체인의 종점에 다다른 것
  if (prototype === null) return false;

  // 프로토타입이 생성자 함수의 prototype 프로퍼티에 바인딩된 객체라면 true 반환
  // 그렇지 않다면 재귀 호출로 프로토타입 체인 상의 프로토타입으로 이동하여 확인
  return (
    prototype === constructor.prototype || isInstanceOf(prototype, constructor)
  );
}
```

- 생성자 함수에 의해 프로토타입이 교체되어 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결은 파괴되지 않으므로 `instanceof`는 영향을 받지 않음

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Park");

console.log(me.constructor === Person); //false
console.log(me instanceof Person); //true
console.log(me instanceof Object);
```
