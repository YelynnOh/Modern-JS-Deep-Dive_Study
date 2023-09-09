# 19. 프로토타입

JS는 클래스 기반 객체 지향 프로그래밍 언어보다 효율적이며 더 강력한 객체지향 프로그래밍 능력을 지니고 있는 프로토타입 기반의 객체 지향 프로그래밍 언어이다.
JS를 이루고 있는 거의 '모든 것'이 객체다!

## 1. 객체 지향 프로그래밍

객체 지향 프로그래밍이란 __여러 개의 독립적 단위, 즉 객체(object)의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 말한다.__

객체 지향 프로그래밍은 객체의 _상태_ 를 나타내는 데이터(__프로퍼티__) 와 상태 데이터를 조작할 수 있는 _동작(behavior)_ (__메서드__)을 하나의 논리적인 단위로 묶어 생각한다.
즉, 객체는 __상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조라고 할 수 있다.__

```js
const circle = {
  // property
  radius: 5, // 반지름

  // method
  // 원의 지름: 2r
  getDiameter() {
    return 2 * this.radius;
  },

  // method
  // 원의 둘레: 2πr
  getPerimeter() {
    return 2 * Math.PI * this.radius;
  },

  // method
  // 원의 넓이: πrr
  getArea() {
    return Math.PI * this.radius ** 2;
  }
};

console.log(circle);
// {radius: 5, getDiameter: ƒ, getPerimeter: ƒ, getArea: ƒ}

console.log(circle.getDiameter());  // 10
console.log(circle.getPerimeter()); // 31.41592653589793
console.log(circle.getArea());      // 78.53981633974483
```

## 2. 상속과 프로토타입

__상속은 객체지향 프로그래밍의 핵심 개념으로, 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.__

__JS는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다.__

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    // Math.PI는 원주율을 나타내는 상수다.
    return Math.PI * this.radius ** 2;
  };
}

// 반지름이 1인 인스턴스 생성
const circle1 = new Circle(1);
// 반지름이 2인 인스턴스 생성
const circle2 = new Circle(2);

// Circle 생성자 함수는 인스턴스를 생성할 때마다 동일한 동작을 하는
// getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.
// getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); // false

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```
여기서 `Circle` 생성자 함수가 생성하는 모든 객체(인스턴스)는 `radius` 프로퍼티와 `getArea` 메서드를 갖는다. `getArea` 메서드의 경우 모든 인스턴스가 동일한 내용의 메서드를 사용하므로 단 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
하지만 `Circle` 생성자 함수는 인스턴스를 생성할 때마다 `getArea` 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다. 

__상속을 통해 이런 불필요한 중복을 제거할 수 있다. JS는 프로토타입(prototype)을 기반으로 상속을 구현한다.__
```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를
// 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/be6d6743-4516-4bce-bd88-2cc4322e9d2f)

## 3. 프로토타입 객체

__프로토타입 객체(aka 프로토타입)은 객체지향 프로그래밍의 근간을 이루는 객체 간 상속(inheritance)을 구현하기 위해 사용된다.__

프로토타입은 어떤 객체의 상위(부모) 객체의 역할을 하는 객체로서 다른 객체에 공유 프로퍼티(메서드 포함)을 제공한다. 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용할 수 있다.

__모든 객체가 생성될 때는 객체 생성 방식에 따라 프로토타입이 결정되고 [[Prototype]]에 저장된다.__

__모든 객체는 하나의 프로토타입을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 연결되어 있다.__

### __proto__ 접근자 프로퍼티

모든 객체는 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 [[Prototype]] 내부 슬롯에 간접적으로 접근할 수 있다.
아래와 같이 확인할 수 있다.

```js
const person = {name : 'Lee'};
```

❓ 교재 사진에서는 `__proto__`로 나오는데, 나는 [[Prototype]]으로 나왔다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/90b02526-30ac-41ad-8f74-c3b5c40db0da)

- 여기서 name: 'Lee' 가 person 객체의 프로퍼티이고, 이 밑에 내용이 person 객체의 프로토타입이다.

__`__proto__`는 접근자 프로퍼티다.__

- JS는 원칙적으로 내부 슬롯과 내부 메서드에 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 X
- 단, 일부 내부 슬롯과 내부 메서드에 한해 간접적으로 접근할 수 있는 수단을 제공한다.
- [[Prototype]] 내부 슬롯에도 직접 접근이 불가하고 `__proto__` 접근자 프로퍼티를 통해 간접적으로 가능하다.

  ```js
  const obj = {};
  const parent = { x: 1 };
  
  // getter 함수인 get __proto__가 호출되어 obj 객체의 프로토타입을 취득
  obj.__proto__;
  // setter함수인 set __proto__가 호출되어 obj 객체의 프로토타입을 교체
  obj.__proto__ = parent;
  
  console.log(obj.x); // 1
  ```

__`__proto__ 접근자 프로퍼티는 상속을 통해 사용된다.`__

이는 객체가 직접 소유하는 프로퍼티가 아니라 `Object.prototype`의 프로퍼티이다. 모든 객체는 상속을 통해 `Object.prototype.__proto__`를 사용할 수 있다.

```js
const person = { name: 'Lee' };

// person 객체는 __proto__ 프로퍼티를 소유하지 않는다.
console.log(person.hasOwnProperty('__proto__')); // false

// __proto__ 프로퍼티는 모든 객체의 프로토타입 객체인 Object.prototype의 접근자 프로퍼티다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}

// 모든 객체는 Object.prototype의 접근자 프로퍼티 __proto__를 상속받아 사용할 수 있다.
console.log({}.__proto__ === Object.prototype); // true
```
📌 `Object.prototype`

- 모든 객체는 프로토타입의 계층 구조인 프로토타입 체인에 묶여 있다.
- 프로토타입 체인의 최상위 객체가 `Object.prototype` 이며, 이 객체으 프로퍼티와 메서드는 모든 객체에 상속된다.

__`__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유__

[[Prototype]] 내부 슬롯의 값, 즉 프로토타입에 접근하기 위해 접근자 프로퍼티를 사용하는 이유는 _상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서다._

프로토타입 체인은 __단방향 링크드 리스트__ 로 구현이 되어야 한다. 즉, 프로퍼티 검색 방향이 한쪽 방향으로만 흘러가야 하고, 만약에 순환 참조하는 프로토타입 체인이 만들어진다면 프로토타입 체인 종점이 존재하지 않기 때문에 프로퍼티 검색 시 무한 루프에 빠진다.

때문에 아무런 체크 없이 무조건적으로 프로토타입을 교체할 수 없도록 `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하고 교체하도록 구현되어 있다. 

__`__proto__` 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.__

모든 객체가 `__proto__` 접근자 프로퍼티를 사용할 수 있는 것은 아니기 때문에 권장이 되지 않는다. 
예를 들어, 직접 상속을 통해 `Object.prototype`을 상속받지 않는 객체를 생성할 수 있기 때문에 사용하자 못할 수도 있다. 

따라서 __`__proto__` 접근자 프로퍼티 대신 프로토타입의 차모를 취득하고 싶은 경우에는 `Object.getPrototypeOf` 메서드를 사용하고, 프로토타입을 교체하고 싶은 경우에는 `Object.setPrototypeOf` 메서드를 사용하는 것을 권장한다.__

```js
const obj = {};
const parent = { x: 1 };

// obj 객체의 프로토타입을 취득
Object.getPrototypeOf(obj); // obj.__proto__;
// obj 객체의 프로토타입을 교체
Object.setPrototypeOf(obj, parent); // obj.__proto__ = parent;

console.log(obj.x); // 1
```

### 함수 객체의 prototype 프로퍼티

_함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다._

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty('prototype'); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty('prototype'); // -> false
```
prototype 프로퍼티는 생성자 함수가 생성할 객체(인스턴스)의 프로토타입을 가리킨다. 따라서 생성자 함수로서 호출할 수 없는 함수, 즉 non-constructor 인 _화살표 함수_ 와 _ES6 메서드 축약 표현으로 정의한 메서드_ 는 prototype 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.

```js
// 화살표 함수는 non-constructor다.
const Person = name => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor다.
const obj = {
  foo() {}
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```

__모든 객체가 가지고 있는(엄밀히 말하면 `Object.prototype`으로부터 상속받은) `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 결국 동일한 프로퍼티는 결국 동일한 프로토타입을 가리킨다.__
하지만!! 이들 프로퍼티를 사용하는 주체가 다르다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/1b38f8bb-5755-47c6-a1b8-3cb58d0921e6)

생성자 함수로 객체를 생성할 후 `__proto__` 접근자 프로퍼티와 prototype 프로퍼티로 프로토타입 객체에 접근해보자

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 결국 Person.prototype과 me.__proto__는 결국 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__);  // true
```

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/796f3706-7e91-44d2-92b5-6f47e90e6cca)

### 프로토타입의 constructor 프로퍼티와 생성자 함수

_모든 프로토타입은 constructor 프로퍼티를 갖는다. 이 constructor 프로퍼티는 prototype 프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킨다._
이 연결은 생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 이뤄진다. 
```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// me 객체의 생성자 함수는 Person이다.
console.log(me.constructor === Person);  // true
```
me객체에는 constructor프로퍼티가 없다.
me객체의 프로토타입인 `me.__proto__` 즉, Person.prototype에는 constructor프로퍼티가 있어 이를 상속받아 사용할 수 있다.

## 4. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

리터럴 표기법에 의한 객체 생성 방식과 같이 명시적으로 `new` 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하지 않는 객체 생성 방식도 있다.

```js
// 객체 리터럴
const obj = {};

// 함수 리터럴
const add = function (a, b) { return a + b; };

// 배열 리터럴
const arr = [1, 2, 3];

// 정규표현식 리터럴
const regexp = /is/ig;
```

리터럴 표기법에 의한 객체도 물론 프로토타입이 존재한다. 하지만 _리터럴 표기법에 의해 생성된 객체의 경우 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 생성자 함수라고 단정할 수는 없다._
```js
// obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴로 생성했다.
const obj = {};

// 하지만 obj 객체의 생성자 함수는 Object 생성자 함수다.
console.log(obj.constructor === Object); // true
```
위 `obj` 객체는 Object 생성자 함수로 생성한 객체가 아니고 객체 리터럴에 의해 생성이 되었다. 하지만 `obj` 객체는 Object 생성자 함수와 constructor 프로퍼티로 연결되어 있다.
그럼 객체 리터럴에 의해 생성된 객체는 사실 Object 생성자 함수로 생성되는 것은 아닐까?

Object 생성자 함수의 원리에 따르면, 해당 함수에 인수를 전달하지 않거나 undefined 또는 null을 인수로 전달하면서 호출할 경우 _내부적으로는 추상 연산 OrdinaryObjectCreate를 호출해 Object.prototype을 프로토타입으로 갖는 빈 객체를 생성한다._
객체 리터럴이 평가될 때는 다음과 같이 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 새엇ㅇ하고 프로퍼티를 추가하도록 정의되어 씨다.

```js
// 2. Object 생성자 함수에 의한 객체 생성
// 인수가 전달되지 않았을 때 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성한다.
let obj = new Object();
console.log(obj); // {}

// 1. new.target이 undefined나 Object가 아닌 경우
// 인스턴스 -> Foo.prototype -> Object.prototype 순으로 체인 생성
class Foo extends Object {}
new Foo(); // Foo {}

// 3. 인수가 전달된 경우에는 인수를 객체로 변환한다.
obj = new Object(123);
console.log(obj); // Number(123)

// String 객체 생성
obj = new Object('123');
console.log(obj); // String {'123'}
```

Object 생성자 함수 호출과 객체 리터럴의 평가는 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성하는 점에서는 동일하나, new.target의 확인이나 프로퍼티를 추가하는 처리 등 세부 내용은 다르다.
__따라서 객체 리터럴에 의해 생성된 객체는 Object 생성자 함수가 생성한 객체가 아니다.__

프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다고 한다.아래는 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입이다.
<br>
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/5e45d846-a7c1-4eae-9b29-11002dda2977)

## 5. 프로토타입의 생성 시점

객체는 __1) 리터럴 표기법__ 또는 __2) 생성자 함수__ 에 의해 생성이 되므로 결국 모든 객체는 __생성자 함수와 연결되어 있다.__
__프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.__

생성자 함수는 _1) 사용자가 직접 정의한 사용자 정의 생성자 함수_ 와 _2) JS가 기본 제공하는 빌트인 생성자 함수_ 로 구분할 수 있다. 

### 사용자 정의 생성자 함수와 프로토타입 생성 시점

__생성자 함수로서 호출할 수 있는 함수, 즉 constructor는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.__

```js
// 함수 정의가 평가되어 함수 객체를 생성하는 시점에서 프로토타입도 같이 생성
console.log(Person.prototype); // {constructor: f}

// 생성자 함수
function Person(name) {
  this.name = name;
}
```

생성자 함수로서 호출 불가 시, 즉, non-constructor일 때는 프로토타입이 생성되지 않는다.

```js
//화살표 함수는 non-constructor이다.
const Person = name => {
  this.name = name;
};

//non-constructor는 프로토타입이 생성되지 안흔ㄴ다.
console.log(Person.prototype); //undefined
```

함수 선언문으로 정의된 person 생성자 함수를 살펴보면
  1. 함수 호이스팅으로 인해 함수 선언문으로 정의된 Person 생성자 함수는 어떤 코드보다 먼저 평가되어 함수 객체가 됨
  2. 이때, 프로토타입도 더불어 생성
  3. 생성된 프로토타입은 Person 생성자 함수의 prototype 프로퍼티에 바인딩 됨
이 과정에서 생성된 프로토타입의 프로토타입은 Object.prototype이다.
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/2ce251a3-958c-4c3e-a684-f01d4651b210)

> 이처럼 __사용자 정의 생성자 함수는 자신이 평가되어 함수 객체로 생성되는 시점에 프로토타입도 더불어 생성되며, 생성된 프로토타입의 프로토타입은 언제나 Object.prototype이다.__

### 빌트인 생성자 함수와 프로토타입 생성 시점

모든 빌트인 생성자 함수(`Object, String, Number, Function, Array, RefExp, Date, Promise`)는 __전역 객체가 생성되는 시점에 생성된다.__
생성된 프로토타입은 빌트인 생성자 함수의 prototype 프로퍼티에 바인딩된다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/a119e5ee-7ddd-4249-be0b-a753f209e497)

이처럼 객체가 생성되기 이전에 생성자 함수와 프로토타입은 이미 객체화되어 존재한다. __이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 [[Prototype]] 내부 슬롯에 할당된다.__
이로써 생섣된 ㄱ개체는 프로토타입을 상속받는다.

## 6. 객체 생성 방식과 프로토타입의 결정

객체는 다양한 생성 방식이 있고 각 방식마다 세부적인 객체 생성 방식에 차이는 있으나 _추상 연산 OrdinaryObjectCreate_ 에 의해 생성된다는 공통점이 있다.

추상 연산 OrdinaryObjectCreate는 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달받는다. 그리고 자신이 생성할 객체에 추가할 프로퍼티 목록을 옵션으로 전달한다. 

이는 빈 객체를 생성한 후, 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 프로퍼티를 객체에 추가한다. 그리고 인수로 전달받은 프로토타입을 자신이 생성한 객체의 [[Prototype]] 내부 슬롯에 할당한 다음, 생성한 객체를 반환한다.

즉, __프로토타입은 추상 연산 OrdinaryObjectCreate에 전달되는 인수에 의해 결정된다.__ 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

👀 객체 생성 방법 정리
  - 객체 리터럴
  - Object 생성 함수
  - 생성자 함수
  - Object.create 메서드
  - 클래스(ES6)

### 객체 리터럴에 의해 생성된 객체의 프로토타입

```js
const obj = { x: 1 };
```

위 예제의 경우  
  1. 객체 리터럴을 평가한다.
  2. OrdinaryObjectCreate를 호출한다. 이때 전달되는 프로토타입은 Object.prototype이다
  3. 결국 Object.prototyped을 프로토타입으로 갖기 때문에 obj객체는 constructor, hasOwnProperty메서드 등을 소유하지 않지만 언제든지 쓸 수 있다.

```js
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x'));    // true
```

### Object 생성자 함수에 의해 생성된 객체의 프로토타입

1. Object 생성자 함수를 인수 없이 호출 시 빈 객체가 생성된다.
2. Object 생성자 함수를 호출 시 객체 리터럴과 마찬가지로 추상 연산 ordinaryObjectCreate가 호출된다. 이때 전달되는 프로토타입은 Object.prototype이다.
3. 즉, Object 생성자 함수에 의해 생성되는 객체의 프로토타입은 Object.prototype이다.

```js
const obj = new Object();
obj.x = 1;

//해당 코드 실행 시 추상 연산 OrdinaryObjectCreate에 의해 Object 생성자 함수와 Object.prototype과 생성된 객체 사이에 연결 생성
// 객체 리터럴에 의해 생성된 객체와 동일한 구조를 갖는다.
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/34c2f16f-aea2-41b0-bd78-d82327b50214)

📌 객체 리터럴과 Object 생성자 함수에 의한 객체 생성 방식의 차이는 __프로퍼티를 추가하는 방식에 있다.__
- 객체 리터럴 : 객체 리터럴 내부에 프로퍼티 추가
- Object 생성자 함수 방식 : 일단 빈 객체 생성 후 프로퍼티 추가

### 생성자 함수에 의해 생성된 객체의 프로토타입

1. 추상 연산 OrdinaryObjectCreate 호출한다.
2. 이때, OrdinaryObjectCreate에 전달되는 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 개체다.
3. 즉, 생성자 함수에 의해 생성되는 객체의 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되엉 ㅣㅆ는 객체다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');
//위 코드 실행 시 추상 연산 OrdinaryObjectCreate에 의해 생성자 함수와 생성자 함수의 prototype 프로퍼티에 바인딩되어있는 객체와 생성된 객체 사이에 연결 생성
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/2271615e-b3c5-43cb-a7c4-ace80f9b3bd5)

그림을 보면 생성자 함수와 더불어 생성된 프로토타입 Object.prototype은 다양한 빌트인 메서드(`hasOwnProperty`, `propertyIsEnumerable` 등)을 가지고 있다. 하지만 사용자 정의 생성자 함수 Person과 더불어 생성된 프로토타입 Person.prototype의 프로퍼티는 constructor 뿐이다.

프로토타입 Person.prototype에 프로퍼티를 추가해 하위(자식) 객체가 상속받을 수 있도록 구현해본다면 아래와 같다.
```js
function Person(name) {
  this.name = name;
}

//프로토타입 메서드
Person.prototype.sayHello = function() {
  console.log('Hi! My name is ${this.name}`);
};

const me = new Person('Lee');
const you = new Person('Kim');

me.sayHello(); //Hi! My name is Lee
you.sayHello(); //Hi! My name is Kim
```

Person 생성자 함수를 통해 생성된 모든 객체는 프로토타입에 추가된 sayHello 메서드를 상속받아 자신의 메서드처럼 사용할 수 있다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/3c8cc4f0-bbc2-486a-9c7d-89025d4b9a2f)

## 7. 프로토타입 체인

JS는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 [[Prototype]]의 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다. 이를 _프로토타입 체인_ 이라고 한다.
프로토타입 체인은 JS가 객체지향 프로그래밍의 상속을 구현하는 메커니즘이다. 

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty('name')); // true
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/860ad565-ac32-4ee9-83dd-67cb0a08465c)


마지막 줄 실행 시 JS 엔진은 다음과 같은 과정을 거친다.
  1. 먼저 hasOwnProperty 메서드를 호출한 me 객체에 해당 메서드 검색. 이때, me 객체는 이를 가지고 있지 않으므로 프로토타입 체인을 따라 검색을 한다. 즉, [[Prototype]] 내부 슬롯에 바인딩되어 있는 프로토타입(Person.prototype)으로 이동하여 메서드를 검색한다.
  2. Person.prototype에도 hasOwnProperty 메서드가 없기 때문에 동일한 방식으로 Object.prototype으로 이동하여 메서드를 검색한다
  3. Object.prototype에는 hasOwnProperty 메서드가 존재한다. JS 엔진은 Object.prototype.hasOwnProperty 메서드를 호출한다. 이때, Object.prototype.hasOwnProperty 메서드의 this에는 me 객체가 바인딩된다.


프로토타입의 최상위에 위치하는 객체는 언제나 Object.prototype이다. 때문에 Object.prototype을 __프로토타입 체인의 종점(end of prototype chain)이라고 한다.__

📌 프로토타입 체인 / 스코프 체인

- 프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘
- 스코프 체인은 식별자 검색을 위한 메커니즘
    - 위 코드 예제의 마지막 줄을 예로 든다면, 스코프 체인에서 me 식별자를 검색한다. me 식별자는 전역에서 선언되었으므로 전역 스코프에서 검색된다.
    - me 식별자를 검색한 다음, me 객체의 프로토타입 체인에서 hasOwnProperty 메서드를 검색한다.
 
_이처럼 스코프 체인과 프로토타입 체인은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는 데 사용된다._

## 8. 오버라이딩과 프로퍼티 섀도잉

프로토타입이 소유한 프로퍼티를 _프로토타입 프로퍼티_, 인스턴스가 소유한 프로퍼티를 _인스턴스 프로퍼티_ 라고 부른다.

```js
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };

  // 생성자 함수를 반환
  return Person;
}());

const me = new Person('Lee');

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hey! My name is Lee
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/0f6d7d65-7018-49a0-8d2a-d2faacf6a629)

인스턴스 메서드 sayHello는 프로토타입 메서드 sayHello를 __오버라이딩__ 했고, 프로토타입 메서드 sayHello 는 가려진다. 이처러 ㅁ상속 관계에 의해 프로퍼티가 가려지는 현상은 __프로퍼티 섀도잉(property shadowing)__ 이라한다. 

📌 _오버라이딩 vs 오버로딩_
> 오버라이딩
> 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

> 오버로딩
> 함수의 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현하고 매개변수에 의해 메서드를 구별하여 호출하는 방식
> JS는 오버로딩을 지원하지 않지만 arguments 객체를 사용해 구현할 수 있다.

프로퍼티를 삭제할 때도 당연히 sayHello가 삭제가 된다. 
하위 객체를 통해 프로토타입에 get 엑세스는 허용이 되나 set 엑세스는 허용이 되지 않는다. 만약 프로토타입 프로퍼티를 변경/삭제하려면 하위 객체를 통해 프로토타입 체인으로 접근하는 것이 아니라 프로토타입에 직접접근해야 한다.

```js
//인스턴스 메서드 삭제 > 프로토타입 메서드가 호출됨.
delete me.sayHello();

//프로토타입 체인을 통해 프로토타입 메서드 삭제 불가능
delete me.sayHello();

//프로토타입 메서드 직접 변경
Person.prototype.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};
me.sayHello(); //Hey! My name is Lee

//프로토타입 메서드 삭제
delete Person.prototype.sayHello;
me.sayHello(); //TypeError: me.sayHello is not a function. 즉, 삭제됨!
```

## 9. 프로토타입의 교체

프로토타입은 1) 생성자 함수 또는 2) 인스턴스에 의해 교체할 수 있다. 

### 생성자 함수에 의한 프로토타입의 교체

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // ① 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');
```
1에서 Person.prototype에 객체 리터럴을 할당했다. 이는 Person 생성자 함수가 생성할 객체의 프로토타입을 객체 리터럴로 교체한 것이다!

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/c76374dd-e2c4-46e0-b9cb-296a03330d18)

프로토타입으로 교체한 객체 리터럴에는 constructor 프로퍼티가 없다. constructor 프로퍼티는 JS 엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티다. 때문에 me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다.
프로토타입을 유지하려면 프로토타입으로 교체한 객체 리터럴에 constructor 프로퍼티를 추가하면 된다.

### 인스턴스에 의한 프로토타입의 교체

프로토 타입은 생성자 함수의 prototype 프로퍼티 뿐만 아니라 인스턴스의 `__proto__` 접근자 프로퍼티를 통해서도 접근 할 수 있다.

즉, 생성을 할 때 변경하는 것이 아니라 이미 생성된 객체의 프로토타입을 교체한다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};

// ① me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/0134f60b-7a0f-418b-860d-d5e6fdfa048e)

프로토타입으로 교체한 객체에는 constructor 프로퍼티가 없으므로 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다. 따라서 프로토타입의 constructor 프로퍼티로 me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다. 

📌 생성자 함수로 프로토타입 교체 vs 인스턴스에 의한 프로토타입 교체

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/554fd26f-75b9-41b7-bb7f-3f9f384bb7f0)
```js
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {
  // constructor 프로퍼티와 생성자 함수간의 연결을 설정
  constructor: Person,
  sayHello(){
    console.log(`Hi! My name is ${this.name}`);
   }
};

// 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결을 설정
Person.prototype = parent;

// me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true;
console.log(me.constructor === Object); // false;

// 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리킨다.
console.log(Person.prototype === Object.getPrototypeOf(me)) // true
```

프로토타입 교체를 통해 객체 간 상속 관계를 동적으로 변경하는 것은 꽤 번거롭다. 따라서 프로토타입은 직접 교체하지 않는 것이 좋다. 

## 10. instanceof 연산자

`객체 instanceof 생성자 함수` 이런 식으로 작성한다.

__우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true로 평가되고, 그렇지 않은 경우에는 false로 평가된다.__

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

```js
//프로토타입 교체

function Person(name) {
   this.name = name;
} 

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입으로 교체
Object.setPrototypeOf(me, parent);

// Person 생성자함수와 parent 객체는 연결되어있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 상에 존재하지 않기 때문에 false
console.log(me instanceof Person); // false
// Person.prototype이 me 객체의 프로토타입 상에 존재하기 때문에 true
console.log(me instanceof Object); // true;
```
me 객체는 비록 프로토타입이 교체되어 프로토타입과 생성자 함수 간 연결이 파괴되었지만 Person 생성자 함수에 의해 생성된 인스턴스임에는 틀림없다. 하지만 `me instanceof Person` 은 false로 평가된다.
이는 Person.prototype이 me 객체의 프로토타입 체인 상에 존재하지 않기 때문이다. 때문에 프로토타입으로 교체한 parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩하면 `me instanceof Person`은 true로 평가된다.

이처럼 __`instanceof` 연산자는 생성자 함수의 prototype에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인한다.__
때문에 constructor 프로퍼티와 생성자 함수간의 연결이 파괴되어도 instanceof 연산에는 아무런 영향을 받지 않는다. 

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');

// constructor 프로퍼티와 생성자 함수 간의 연결은 파괴되어도 instanceof는 아무런 영향을 받지 않는다.
console.log(me.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

## 11. 직접 상속

### Object.create에 의한 직접 상속

Object.create 메서드는 명시적으로 프로토타입을 지정해 새로운 객체를 생성한다. Object.create 메서드도 다른 객체 생성 방식과 마찬가지로 추상 연산 OrdinaryObjectCreate를 호출한다.

Object.create메서드로 객체를 생성할 때 장점
- new 연산자 없이도 객체 생성이 가능하다.
- 프로토타입을 지정하면서 객체를 생성한다.
- 객체 리터럴에 의해 생성된 객체도 상속 가능하다.

```js
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj → null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
// Object.prototype을 상속받지 못한다.
console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj → Object.prototype → null
// obj = {};와 동일하다.
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj → Object.prototype → null
// obj = { x: 1 };와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true }
});
// 위 코드는 다음과 동일하다.
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// 임의의 객체를 직접 상속받는다.
// obj → myProto → Object.prototype → null
obj = Object.create(myProto);
console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj → Person.prototype → Object.prototype → null
// obj = new Person('Lee')와 동일하다.
obj = Object.create(Person.prototype);
obj.name = 'Lee';
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

### 객체 리터럴 내부에서 `__proto__`에 의한 직접 상속

Object.create 메서드에서는 두 번째 인자로 프로퍼티를 정의하는 게 꽤 번거롭다.
ES6에서는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.

```js
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체를 직접 상속받는다.
  // obj → myProto → Object.prototype → null
  __proto__: myProto
};
/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 12. 정적 프로퍼티/메서드

__정적 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드를 말한다.__

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티
Person.staticProp = 'static prop';

// 정적 메서드
Person.staticMethod = function () {
  console.log('staticMethod');
};

const me = new Person('Lee');

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // staticMethod

// 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/53b0bdc4-33cd-4c2d-a52f-383da89a5866)

정적 프로퍼티/메서드는 인스턴스의 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아니므로 인스턴스로 접근할 수 없다. 

만약 인스턴스/프로토타입 메서드 내에서 this를 사용하지 않는다면 그 메서드는 정적 메서드로 변경할 수 있다.
```js
function Foo() {}

// 프로토타입 메서드
// this를 참조하지 않는 프로토타입 메소드는 정적 메서드로 변경해도 동일한 효과를 얻을 수 있다.
Foo.prototype.x = function () {
  console.log('x');
};

const foo = new Foo();
// 프로토타입 메서드를 호출하려면 인스턴스를 생성해야 한다.
foo.x(); // x

// 정적 메서드
Foo.x = function () {
  console.log('x');
};

// 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.
Foo.x(); // x
```


## 13. 프로퍼티 존재 확인

### in 연산자

in 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.

```js
/**
 * key: 프로퍼티 키를 나타내는 문자열
 * object: 객체로 평가되는 표현식
 */
// key in object 이런 식으로 쓰인다.

const person = {
  name: 'Lee',
  address: 'Seoul'
};

// person 객체에 name 프로퍼티가 존재한다.
console.log('name' in person);    // true
// person 객체에 address 프로퍼티가 존재한다.
console.log('address' in person); // true
// person 객체에 age 프로퍼티가 존재하지 않는다.
console.log('age' in person);     // false
```

다만 이는 확인 대상 객체가 상속받은 모든 프로토타입의 프로퍼티를 확인하므로 주의가 필요하다. 

in 연산자 대신 `Reflect.has` 메서드를 사용할 수도 있다.

### Object.prototype.hasOwnProperty 메서드

이 메서드 역시 마찬가지로 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다.

```js
console.log(person.hasOwnProperty('name')); // true
console.log(person.hasOwnProperty('age'));  // false
```

이 메서드는 인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 true를 반환하고, 상속받은 프로토타입의 프로퍼티 키인 경우 false를 반환한다.

## 14. 프로퍼티 열겨

### for ... in 문

객체의 모든 프로퍼티를 순회하면 열거(enumeration)하려면 for ... in문을 사용한다.

`for (변수선언문 in 객체) {...}` 식으로 선언한다.

```js
const person = {
  name: 'Lee',
  address: 'Seoul'
};

// for...in 문의 변수 prop에 person 객체의 프로퍼티 키가 할당된다.
for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
```

for ... in 문은 객체의 프로퍼티 개수만큼 순회해 변수 선언문에서 선언한 변수에 프로퍼티 키를 할당한다.
  - 위 예제의 경우 person 객체에 2개의 프로퍼티가 있기 때문에 객체를 2번 순회, 프로퍼티 키를 key 변수에 할당 후 코드 블록을 실행한다.

해당 메서드는 순회 대상 객체의 프로퍼티 뿐 만 아니라 상속받은 프로토타입의 프로퍼티까지 열겨한다.

하지만! in 연산자와 다르게 toString과 같은 Object.prototype의 프로퍼티는 열겨되지 않는데, 이는 Object.prototype.toString 프로퍼티의 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false이기 때문이다.

따라서 __for ... in 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 true인 프로퍼티를 순회하며 열거한다. 또한, 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않는다.__

### Object.keys/values/entries 메서드

```js
//Object.keys : 해당 메서드는 객체 자신의 고유 프로퍼티만 열겨해주는 메서드이다.
const person = {
  name: 'Lee',
  address: 'Seoul',
  __proto__: { age: 20 }
};

console.log(Object.keys(person)); // ["name", "address"]

//Object.values : 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환.
console.log(Object.values(person)); // ["Lee", "Seoul"]

//Object.entries : 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환
console.log(Object.entries(person)); // [["name", "Lee"], ["address", "Seoul"]]

Object.entries(person).forEach(([key, value]) => console.log(key, value));
/*
name Lee
address Seoul
*/

```
