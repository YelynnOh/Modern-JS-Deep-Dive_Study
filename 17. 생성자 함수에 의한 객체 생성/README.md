# 17. 생성자 함수에 의한 객체 생성

## 1. Object 생성자 함수

생성자 함수(constructor)란 `new` 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수를 말한다. 생성자 함수에 의해 생성된 객체를 인스턴스(instance)라고 한다. 
`Object` 생성자 함수를 호출하면 빈 객체를 생성하여 반환하는데, 빈 객체를 생성한 이후 프로퍼티 또는 메서드를 추가해 객체를 완성할 수 있다.
JS는 `Object` 생성자 함수 이외에도 `String, Number, Boolean, Function, Array, Date, RegExp, Promise` 등의 빌트인(built-in) 생성자 함수를 제공한다. 

```js
const person = new Object();

// 프로퍼티 추가
person.name = "Lee";
person.sayHello = function () {
    console.log("Hi! My name is " + this.name);
};

console.log(person); // {name: "Lee", sayHello: ƒ}
person.sayHello(); // Hi! My name is Lee
```

```js
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String("Lee");
console.log(typeof strObj); // object
console.log(strObj); // String {"Lee"}

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123);
console.log(typeof numObj); // object
console.log(numObj); // Number {123}

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj = new Boolean(true);
console.log(typeof boolObj); // object
console.log(boolObj); // Boolean {true}

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function("x", "return x * x");
console.log(typeof func); // function
console.dir(func); // ƒ anonymous(x)

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3);
console.log(typeof arr); // object
console.log(arr); // [1, 2, 3]

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i);
console.log(typeof regExp); // object
console.log(regExp); // /ab+c/i

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();
console.log(typeof date); // object
console.log(date); // Mon May 04 2020 08:36:33 GMT+0900 (대한민국 표준시)
```

다만, 객체를 생성할 때 사용할 수 있는 방법으로 객체 리터럴을 사용하는 것이 더 간편하다!

## 2. 생성자 함수

__객체 리터럴에 의한 객체 생성 방식의 문제점__

장점 > 객체 리터럴에 의한 객체 생성 방식은 직관적이고 간편하다.

단점 > 단 하나의 객체만 생성하기 때문에 동일한 프로퍼티를 갖는 객체를 여러 개 생성해야 하는 경우 매번 같은 프로퍼티를 기술해야 하기 때문에 비효율적이다.

```js
const circle1 = {
    radius: 5,
    getDiameter() {
        return 2 * this.radius;
    }
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
    radius: 10,
    getDiameter() {
        return 2 * this.radius;
    }
};

console.log(circle2.getDiameter()); // 20
```

위 예제에서 보면 `circle 1`과 `circle 2`의 객체는 프로퍼티 구조가 동일하다. 객체 고유 상태(state) 데이터인 `radius` 프로퍼티 값은 객체마다 다를 수 있지만 `getDiameter` 메서드는 완전히 동일하다.
하지만 객체 리터럴에 의해 객체를 생서하는 경우 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메서드를 기술해야 한다. 이는 비효율적이며, 수십 개의 객체를 생성하면 문제가 크다. 

__생성자 함수에 의한 객체 생성 방식의 장점__

생성자 함수에 의한 객체 생성 방식은 마치 객체(인스턴스)를 생성하기 위한 템플릿(클래스)처럼 생성자 함수를 사용하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있다.

```js
//생성자 함수
function Circle(radius) {
    // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

📌 __this__

  `this`는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수(self-referencing variable)다. `this`가 가리키는 값, __즉 `this` 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.__
  
  ![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/ab0a6869-e1c8-443c-ba6c-17123f4baf21)
  
  ```js
  // 함수는 다양한 방식으로 호출될 수 있다.
  function foo() {
      console.log(this);
  }
  
  // 일반적인 함수로서 호출
  // 전역 객체는 브라우저 환경에서는 window, Node.js 환경에서는 global을 가리킨다.
  foo(); // window
  
  // 메서드로서 호출
  const obj = {foo}; // ES6 프로퍼티 축약 표현
  obj.foo(); // obj
  
  // 생성자 함수로서 호출
  const inst = new foo(); // inst
  ```

## 생성자 함수의 인스턴스 생성 과정

생성자 함수의 역할은 프로퍼티 구조가 동일한 인스턴스를 생성하기 위한 템플릿으로 동작하여 인스턴스를 생성하는 것(필수)과 생성된 인스턴스를 초기화(옵션) 하는 것이다.
`new` 연산자와 함께 생성자 함수를 호출할 경우 JS 엔진은 암묵적 처리를 통해 인스턴스를 생성하고 초기화 후 반환한다.

```js
// 생성자 함수
function Circle(radius) {
    // 인스턴스 초기화
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
```

__1) 인스턴스 생성과 this 바인딩__

- 암묵적 빈 객체 생성(== 인스턴스)
- 바로 이 인스턴스가 `this`에 바인딩된다

> 바인딩이란?
> 식별자와 값을 연결하는 과정
> 변수 선언은 변수 이름(식별자)과 확보된 메모리 공간의 주소를 바인딩하는 것이다.
> this 바인딩은 this와 this가 가리킬 객체를 바인딩하는 것이다.

```js
function Circle(radius) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Circle {}

    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}
```

__2) 인스턴스 초기화__

- this에 바인딩되어 있는 인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당한다.
```js
function Circle(radius) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}
```

__3) 인스턴스 반환__

- 생성자 함수 내부에서 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`를 암묵적으로 반환한다.

```js
function Circle(radius) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```

- 만약 this가 아닌 다른 _객체_ 를 명시적으로 반환하면 this가 반환되지 못하고 return 문에 명시한 객체가 반환된다.

```js
function Circle(radius) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };

    // 3. 암묵적으로 this를 반환한다.
    // 명시적으로 객체를 반환하면 암묵적인 this 반환이 무시된다.
    return {};
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // {}
```

하지만 명시적으로 _원시 값_ 을 반환하면 원시 값 반환은 무시되고 암묵적으로 this가 반환된다.

```js
function Circle(radius) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };

    // 3. 암묵적으로 this를 반환한다.
    // 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
    return 100;
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```

__명시적으로 this가 아닌 다른 값을 반환하는 것은 생성자 함수의 기본 동작을 훼손하므로 생성자 함수 내부에서 return 문을 반드시 생략해야 한다.__

__4) 내부 메서드[[Call]]과 [[Construct]]__

함수 선언문 또는 함수 표현식으로 정의한 함수는 일반적인 함수로서 호출할 수 있는 것은 물론, `new` 연산자와 함께 호출하여 객체를 생성할 수 있다. (생성자 함수로서 호출)
함수는 객체이므로 일반 객체(ordinary object)와 동일하게 동작할 수 있다. 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드를 모두 가진다.
```js
// 함수는 객체다.
function foo() {}

// 함수는 객체이므로 프로퍼티를 소유할 수 있다.
foo.prop = 10;

// 함수는 객체이므로 메서드를 소유할 수 있다.
foo.method = function () {
    console.log(this.prop);
};

foo.method(); // 10
```

하지만 일반 객체와 달리 함수는 호출할 수 있다. 따라서 함수 객체는 함수로서 동작하기 위해 함수 객체만을 위한 `[[Environment]], [[FormalParameters]]` 등의 내부 슬롯과 `[[Call]], [[Construct]]` 같은 내부 메서드를 추가로 가지고 있다.

함수는 일반 함수로서 호출되면 함수 객체의 내부 메서드 `[[Call]]` 이 호출되고` new `연산자와 함께 생성자 함수로서 호출되면 내부 메서드 `[[Construct]]` 가 호출된다.

```js
function foo() {}

// 일반적인 함수로서 호출: [[Call]]이 호출된다.
foo();

// 생성자 함수로서 호출: [[Construct]]가 호출된다.
new foo();
```

- 내부 메서드 `[[Call]]`을 갖는 함수 객체를 `callable` : 호출할 수 있는 객체, 즉, 함수
- 내부 메서드 `[[Construct]]`를 갖는 함수 객체를 `constructor` : 생성자 함수로서 호출할 수 있는 함수
- 내부 메서드 `[[Construct]]`를 갖지 않는 함수 객체를 `non-constructor` : 객체를 생성자 함수로서 호출할 수 없는 함수

_따라서 함수 객체는 `callable`이면서 `constructor`이거나 `callable`이면서 `non-construct`이다. 즉, 모든 함수 객체는 호출할 수 있지만 모든 함수 객체를 생성자 함수로서 호출할 수 있는 것은 아니다._

__5) constructor와 non-constructor의 구분__

JS 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 `constructor`와 `non-constructor`로 구분한다.

- constructor: 함수 선언문, 함수 표현식, 클래스(클래스도 함수)
- non-constructor: 메서드(ES6 메서드 축약 표현), 화살표 함수

이때 주의할 것은 ECMAScript 사양에서 메서드로 인정하는 범위가 일반적인 의미의 메서드보다 좁다는 것이다.
```js
// 일반 함수 정의: 함수 선언문, 함수 표현식
function foo() { }
const bar = function () { };
// 프로퍼티 x의 값으로 할당된 것은 일반 함수로 정의된 함수다. 이는 메서드로 인정하지 않는다.
const baz = {
    x: function () { }
};

// 일반 함수로 정의된 함수만이 constructor이다.
new foo(); // foo {}
new bar(); // bar {}
new baz.x(); // x {}

// 화살표 함수 정의
const arrow = () => { };

new arrow(); // TypeError: arrow is not a constructor

// 메서드 정의: ES6의 메서드 축약 표현만 메서드로 인정한다.
const obj = {
    x() { }
};

new obj.x(); // TypeError: obj.x is not a constructor
```
ECMAScript 사양에서 메서드란 ES6의 메서드 축약 표현만을 의미한다. 함수 정의 방식에 따라 constructor와 non-constructor를 구분한다

👀 생성자 함수로서 호출될 것을 기대하고 정의하지 않은 일반 함수(callable & constructor)에 new 연산자를 붙여 호출하면 생성자 함수처럼 동작할 수 있다.


__6) new 연산자__

`new` 연산자와 함께 함수를 호출하면 해당 함수는 생성자 함수(`[[Construct]]`)로 동작한다. 이때, 해당 함수는 constructor이어야 한다.
반대로 `new` 연산자 없이 생성자 함수를 호출하면 일반 함수로 호출된다. 즉, 함수 객체 내부 메서드 `[[Constructor]]`가 호출되는 것이 아니라 `[[Call]]`이 호출된다. 
```js
// 생성자 함수로서 정의하지 않은 일반 함수
function add(x, y) {
    return x + y;
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
let inst = new add();

// 함수가 객체를 반환하지 않았으므로 반환문이 무시된다. 따라서 빈 객체가 생성되어 반환된다.
console.log(inst); // {}

// 객체를 반환하는 일반 함수
function createUser(name, role) {
    return {name, role};
}

// 일반 함수를 new 연산자와 함께 호출
inst = new createUser("Lee", "admin");

// 함수가 생성한 객체를 반환한다.
console.log(inst); // { name: 'Lee', role: 'admin' }
```
```js
// 생성자 함수
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

// new 연산자 없이 생성자 함수를 호출하면 일반 함수로서 호출된다.
const circle = Circle(5);
console.log(circle); // undefined

// 일반 함수 내부의 this는 전역 객체 window를 가리킨다.
console.log(radius); // 5
console.log(getDiameter()); // 10

circle.getDiameter();
// TypeError: Cannot read property 'getDiameter' of undefined
```

일반 함수와 생성자 함수에 특별한 형식적 차이는 없다. __따라서 생성자 함수는 일반적으로 첫 문자를 대문자로 기술하는 파스칼 케이스로 명명해 일반 함수와 구별하자.__

__7) new.target__

__생성자 함수가 `new` 연산자 없이 호출되는 위험성을 회피하기 위해 ES6에서는 `new.target`을 지원한다.__

`this`와 유사하게 constructor인 모든 함수 내부에서 암묵적인 지역 변수와 같이 사용되며 메타 프로퍼티라고 부른다(IE에서는 지원하지 않는다.)

함수 내부에서 `new.target`을 사용하면 `new` 연산자와 함께 생성자 함수로서 호출되었는지를 확인할 수 있다.

- `new` 연산자와 함께 생성자 함수로서 호출 : 함수 내부의 `new.target`은 함수 자신을 가리킨다.
- `new` 연산자와 없이 일반 함수로서 호출 : 함수 내부의 `new.target`은 undefined다.

```js
// 생성자 함수
function Circle(radius) {
    // 이 함수가 new 연산자와 함께 호출되지 않았다면 new.target은 undefined
    if (!new.target) {
        // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환한다.
        return new Circle(radius);
    }

    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

// new 연산자 없이 생성자 함수를 호출하여도 new.target을 통해 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter()); // 10
```

대부분의 빌트인 생성자 함수(`Object, String, Number, Boolean, Function, Array, Date, RegExp, Promise`)는 `new` 생성자와 함께 호출되었는지를 확인한 후 적절한 값을 반환한다고 한다.
```js
let obj = new Object();
console.log(obj); // {}

obj = Object();
console.log(obj); // {}

let f = new Function("x", "return x ** x");
console.log(f); // ƒ anonymous(x) { return x ** x }

f = Function("x", "return x ** x");
console.log(f); // ƒ anonymous(x) { return x ** x }
```

하지만 `String, Number, Boolean` 생성자 함수는 `new` 연산자와 함께 호출했을 때 `String, Number, Boolean` 객체를 생성하여 반환하지만, `new` 연산자 없이 호출하면 문자열, 숫자, 불리언 값을 반환한다.
```js
const str = String(123);
console.log(str, typeof str); // 123 string

const num = Number("123");
console.log(num, typeof num); // 123 number

const bool = Boolean("true");
console.log(bool, typeof bool); // true boolean

```

