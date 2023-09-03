# 15. let, const 키워드와 블록 레벨 스코프

## 1. `var` 키워드로 선언한 변수의 문제점

### 변수 중복 선언 허용

`var` 키워드로 성넌한 변수는 중복 선언이 가능하다.

이때, 중복 선언을 하더라도 초기화문이 있는 변수 선언문은 JS 엔진에 의해 `var` 키워드가 없는 것처럼 동작하고, 초기화문이 없으면 무시된다. 에러는 발생하지 않는다.

때문에 만약 동일한 이름의 변수가 이미 선언되어 있는 것을 모르고 변수를 중복 선언 및 초기화를 해버리면 의도치 않게 먼저 선언된 변수 값이 변경되는 부작용이 발생한다.

### 함수 레벨 스코프

`var` 키워드로 선언한 변수는 오로지 함수의 코드 블록만을 지역 스코프로 인정한다.
따라서 함수 외부에서 `var` 키워드로 선언한 변수는 코드 블록 내에서 선언해도 모두 전역 변수가 된다. 즉, 전역 변수가 중복 선언되고 남발되어 부작용을 발생할 수 있다.

```js
var x = 1;

if (true) {
    // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
    // 이는 의도치 않게 변수 값이 변경되는 부작용을 발생시킨다.
    var x = 10;
}

console.log(x); // 10
```

```js
// for 문에서 선언한 i는 전역 변수다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
    console.log(i); // 0 1 2 3 4
}

// 의도치 않게 변수의 값이 변경되었다.
console.log(i); // 5
```

### 변수 호이스팅

`var` 키워드로 변수 선언 시 변수 호이스팅에 의해 변수 선언문이 스코프의 선두로 끌어 올려진 것처럼 동작한다. 즉, 해당 변수는 변수 선언문 이전에 참조할 수 있는데 이는 프로그램 흐름 상 적절치 않고 가독성을 떨어트린다. 또한, 오류를 발생시킬 여지를 남긴다.

## 2. `let` 키워드

### 변수 중복 선언 금지

`let` 키워드로 이름이 같은 변수를 중복 선언하면 문법 에러(SyntaxError)가 발생한다. 
```js
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

### 블록 레벨 스코프

`let` 키워드로 선언한 변수는 모든 코드 블록(함수, if문, for문, while문, try/catch문 등)을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다. 

```js
let foo = 1; // 전역 변수

{
    let foo = 2; // 지역 변수
    let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/518a7dfa-5919-4e0f-b5cd-6e80de16ac1b)

코드에서 본다면 전역에서 선언된 `foo` 변수와 코드 블록 내에서 선언된 `foo` 변수는 다른 별개의 변수다. 또한 `bar` 변수도 블록 레벨 스코프를 갖는 지역 변수이다. 따라서 전역에서는 `bar` 변수를 참조할 수 없다. 

### 변수 호이스팅

`let` 키워드로 선언한 변수는 변수 호이스팅이 발생하지 않는 것처럼 동작한다. 

`let` 키워드로 선언한 변수는 __선언 단계__ 와 __초기화 단계__ 가 분리되어 진행된다. 즉, 런타임 이전에 JS 엔진에 의해 암묵적으로 선언 단계가 먼저 실행되지만 초기화 단계는 변수 선언문에 도달했을 때 실행된다. 
```js
//var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수 참조가 가능하다.
console.log(foo); //undefined

var foo;
console.log(foo); //undefined

foo = 1;
console.log(foo); //1
--- 
근데 let 키워드를 사용했다면 에러가 발생한다.
console.log(foo); //ReferenceError
let foo;
```

만약 초기화 단계가 실행되기 이전에 변수에 접근하려고 하면 참조 에러(Reference Error)가 발생한다. 
`let` 키워드로 선언한 변수는 스코프의 시작 지점부터 초기화 단계 시작 지점(변수 선언문)까지 변수를 참조할 수 없다. 스코프의 시작 지점부터 초기화 시작 지점까지 변수를 참조할 수 없는 구간을 __일시적 사각지대(Temporal Dead Zone)__ 이라 부른다.

```js
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/1700079a-d50a-4084-9c8c-90013a0a8556)

하지만 `let` 키워드로 선언한 변수는 변수 호이스팅이 발생하지 '않는 것 처럼' 보이는거지 실제로 발생하지 않는 게 아니다.
JS에서는 `let, const, var, function, function*, class` 등을 호이스팅한다. 다만 `let const class`는 안하는 것처럼 보이고. 

### 전역 객체와 `let`

`var` 키워드로 선언한 전역 변수와 전역 함수, 그리고 선언하지 않은 변수에 값을 할당한 암묵적 전역은 전역 객체 windows의 프로퍼티가 된다. 전역 객체의 프로퍼티를 참조할 때 windows는 생략할 수 있다.

```js
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // ƒ foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // ƒ foo() {}
```

하지만 `let` 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다. 즉, _`window.foo`_ 와 같이 접근할 수 없다. `let` 전역 변수는 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하게 된다. 
```js
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

## 3. `const` 키워드

`const` 키워드는 상수를 선언하기 위해 사용한다. (물론 기능이 더 많기는 한데, 뒤에 더 알아보자!)

### 선언과 초기화

__`const` 키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야 한다.__ 아니면 문법에러(SyntaxError)가 발생한다.

```js
const foo; //SyntaxError: Missing initializer in const declaration
```

`const` 키워드로 선언한 변수는 `let` 키워드로 선언한 변수와 마찬가지로 블록 레벨 스코프를 가지며, 변수 호이스팅이 발생하지 않는 것처럼 동작한다. 
```js
{
    // 변수 호이스팅이 발생하지 않는 것처럼 동작한다
    console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
    const foo = 1;
    console.log(foo); // 1
}

// 블록 레벨 스코프를 갖는다.
console.log(foo); // ReferenceError: foo is not defined
```

### 재할당 금지

`var`, `let` 과 다르게 __`const` 키워드로 선언한 변수는 재할당이 금지된다.__

```js
const foo = 1;
foo = 2; //TypeError: Assignmnet to constant variable
```

### 상수

상수란 재할당이 금지된 변수를 의미한다. `const` 키워드를 사용한다면 상수를 만드는 것이다.

### `const` 키워드와 객체

__`const` 키워드로 선언된 변수에 객체(원시값X)를 할당한 경우 값을 변경할 수 있다!__
변경 가능한 값인 객체는 재할당 없이도 직접 변경이 가능하기 때문이다. 

```js
const person = {
    name: "Lee",
};

// 객체는 변경 가능한 값이다. 따라서 재할당 없이 변경이 가능하다.
person.name = "Kim";
console.log(person); // {name: "Kim"}
```
const 키워드는 재할당을 금지할 뿐 _불변을 의미하지는 않는다._

## 4. `var` vs `let` vs `const`

변수 선언에는 기본적으로 `const`를 사용하고, `let`은 재할당이 필요한 경우에 한정해 사용하는 것이 좋다.
`const` 키워드를 사용하면 의도치 않은 재할등을 방지하기 때문에 좀 더 안전하다고 한다!

- ES6를 사용한다면 `var` 키워드는 사용하지 않는다.
- 재할당이 필요한 경우에 한정해 `let` 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
- 변경이 발생하지 않고 읽기 전용으로 사용하는(재할당이 필요 없는 상수) 원시 값과 객체에는 `const` 키워드를 사용한다. `const` 키워드는 재할당을 금지하므로 `var`, `let` 키워드보다 안전하다.
