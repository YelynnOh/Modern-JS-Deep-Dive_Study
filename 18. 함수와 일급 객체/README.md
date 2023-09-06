# 18. 함수와 일급 객체

## 1. 일급 객체

일급 객체란?

- 무명의 리터럴로 생성 가능. 즉, 런타임에 생성 가능
- 변수나 자료구조(객체, 배열 등)에 저장할 수 있음
- 함수의 매개변수에 전달 가능
- 함수의 반환값으로 사용 가능

JS의 함수는 위의 조건을 모두 만족하므로 일급 객체임!

```js
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임(할당 단계)에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
    return ++num;
};

const decrease = function (num) {
    return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = {increase, decrease};

// 3. 함수의 매개변수에게 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.
function makeCounter(aux) {
    let num = 0;

    return function () {
        num = aux(num);
        return num;
    };
}

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(auxs.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const decreaser = makeCounter(auxs.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

함수가 일급 객체라는 것은 함수를 객체와 동일하게 사용할 수 있다는 것이다. 객체는 값이므로 함수는 값과 동일하게 취급할 수 있다. <br>
👉 따라서 _함수는 값을 사용할 수 있는 곳(변수 할당문, 객체의 프로퍼티 값, 배열의 요소, 함수 호출의 인수, 함수 반환문)이라면 어디서든지 리터럴로 정의할 수 있으면 런타임에 함수 객체로 평가된다._

일급 객체로서 함수가 가지는 가장 큰 특징은 __일반 객체와 같이 함수의 매개변수에 전달할 수 있으며, 함수의 반환값으로 사용할 수도 있다는 것이다. 이는 함수형 프로그래밍을 가능케 하는 JS의 장점 중 하나다.__
다만 일반 객체는 호출할 수 없지만 함수 객체는 호출할 수 있다. 함수 객체는 일반 객체에는 없는 함수 고유의 프로퍼티를 소유한다.

## 2. 함수 객체의 프로퍼티

함수는 객체이기 때문에 프로퍼티를 가진다.

```js
function square(number) {
    return number * number;
}

console.dir(square); //함수 객체의 내부를 들여다 볼 수 있다
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/e44287d4-2c7c-4c66-b5a6-5b2081cad813)

`square` 함수의 모든 프로퍼티의 프로퍼티 어트리뷰트는 `Object.getOwnPropertyDescriptors` 메서드로 확인하기
```js
function square(number) {
    return number * number;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
{
    length : { value: 1, writable: false, enumerable: false, configurable: true },
    name : { value: "square", writable: false, enumerable: false, configurable: true },
    arguments: { value: null, writable: false, enumerable: false, configurable: false },
    caller: { value: null, writable: false, enumerable: false, configurable: false },
    prototype: { value: {…}, writable: true, enumerable: false, configurable: false }
}
*/

// __proto__는 square 함수의 프로퍼티가 아니다.
console.log(Object.getOwnPropertyDescriptors(square, '__proto__')); // undefined

// __proto__는 Object.prototype 객체의 접근자 프로퍼티다.
// square 함수는 Object.prototype 객체로부터 __proto__ 접근자 프로퍼티를 상속받는다.
console.log(Object.getOwnPropertyDescriptors(Object.prototype, '__proto__'));
// {configurable: true, enumerable: false, get: ƒ, set: ƒ }
```
이처럼 `arguments, caller, length, name, prototype` 프로퍼티는 모두 함수 객체의 데이터 프로퍼티이다. 이는 일반 객체에는 없는 함수 객체 고유의 프로퍼티이다.
하지만 `__proto__`는 접근자 프로퍼티이며, 함수 객체 고유 프로퍼티가 아니라 `Object.prototype` 객체의 프로퍼티를 상속받은 것을 알 수 있다. `Object.prototype` 객체의 프로퍼티는 모든 객체가 상속받아 사용할 수 있기 때문에, `__proto__` 접근자 프로퍼티는 모든 객체가 사용할 수 있다.

### `arguments` 프로퍼티

함수 객체의 `arguments` 프로퍼티 값은 arguments 객체이다. arguments 객체는 함수 호출 시 전달된 인수(argument)들의 정보를 담고 있는 _순회 가능한 유사 배열(iterable) 객체이며, 함수 내부에서 지역 변수처럼 사용된다. 
즉, 함수 외부에서는 참조 불가능하다._


```js
// JS는 함수의 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.
// 인자로 넘긴 인수들은 arguments에 저장된다.
function multiply(x, y) {
    console.log(arguments);
    return x * y;
}

console.log(multiply()); // NaN
console.log(multiply(1)); // NaN
console.log(multiply(1, 2)); // 2
console.log(multiply(1, 2, 3)); // 2
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/36069c43-7378-473d-8027-c57070d3eb0e)


`arguments` 객체는 매개변수 개수를 확정할 수 없는 __가변 인자 함수__ 를 구현할 때 유용하다. 

```js

function sum() {
    let res = 0;

    // arguments 객체는 length 프로퍼티가 있는 유사 배열 객체이므로 for 문으로 순회할 수 있다.
    for (let i = 0; i < arguments.length; i++) {
        res += arguments[i];
    }

    return res;
}

console.log(sum()); // 0
console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3)); // 6
```
유사 배열 객체는 배열이 아니므로 배열 메서드 사용 시 에러가 발생한다. 따라서 배열 메서드를 사용하려면 `Function.prototype.call`, `Function.prototype.apply`를 사용해 간접 호출해야 하는 번거로움이 있다. 
때문에 ES6에서는 `Rest` 파라미터를 도입했다. 

```js
function sum() {
    // arguments 객체를 배열로 변환
    const array = Array.prototype.slice.call(arguments);
    return array.reduce(function (pre, cur) {
        return pre + cur;
    }, 0);
}

console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3, 4, 5)); // 15

//Rest 사용하기
// ES6 Rest parameter
function sum(...args) {
    return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3, 4, 5)); // 15
```

### caller 프로퍼티

함수 자신을 호출한 함수를 가리킨다.

### length 프로퍼티

함수를 정의할 때 선언한 매개변수의 개수를 가리킨다.

```js
function foo() {}
console.log(foo.length); // 0

function bar(x) {
    return x;
}
console.log(bar.length); // 1

function baz(x, y) {
    return x * y;
}
console.log(baz.length); // 2
```

📌 _arguments 객체의 length 프로퍼티와 함수 객체의 length 프로퍼티 값은 다를 수 있다!_
- arguments 객체의 length  프로퍼티: 인자(arguments)의 갯수
- 함수 객체의 length 프로퍼티 : 매개변수(parameter)의 갯수

### name 프로퍼티

이는 함수 이름을 나타낸다. ES6과 ES5에서 동작을 달리하기 때문에 주의를 해야 한다. 익명 함수 표현식의 경우 ES5에서 name 프로퍼티는 빈 문자열을 값으로 갖는다. 하지만 ES6에서는 함수 객체를 가리키는 식별자를 값으로 갖는다.

```js
// 기명 함수 표현식
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

// 익명 함수 표현식
var anonymousFunc = function () {};
// ES5: name 프로퍼티는 빈 문자열을 값으로 갖는다.
// ES6: name 프로퍼티는 함수 객체를 가리키는 변수 이름을 값으로 갖는다.
console.log(anonymousFunc.name); // anonymousFunc

// 함수 선언문(Function declaration)
function bar() {}
console.log(bar.name); // bar
```

### `__proto__` 접근자 프로퍼티

모든 객체는 [[Prototype]]이라는 내부 슬롯을 갖는다. 
`__proto__` 프로퍼티는 [[Prototype]] 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티이다. 내부 슬롯에 직접 접근할 수 없고, 간접적인 접근 방법을 제공하는 경우에 한해 접근할 수 있다.
```js
const obj = {a: 1};

// 객체 리터럴 방식으로 생성한 객체의 프로토타입 객체는 Object.prototype이다.
console.log(obj.__proto__ === Object.prototype); // true

// 객체 리터럴 방식으로 생성한 객체는 프로토타입 객체인 Object.prototype의 프로퍼티를 상속받는다.
// hasOwnProperty 메서드는 Object.prototype의 메서드다.
console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("__proto__")); // false
```
> `hasOwnProperty` 메서드
> 인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 true를 반환하고 상속받은 프로토타입의 프로퍼티 키인 경우 false를 반환한다.

### prototype 프로퍼티

생성자 함수로 호출할 수 있는 함수 객체, 즉 `constructor` 만이 소유하는 프로퍼티다. 일반 객체와 생성자 함수로 호출할 수 없는 `non-constructor`에는 prototype 프로퍼티가 없다. 
이는 함수가 객체를 생성하는 생성자 함수로 호출될 때 생성자 함수가 생성할 인스턴스의 프로토타입 객체를 가리킨다. 

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty("prototype"); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty("prototype"); // -> false
```
