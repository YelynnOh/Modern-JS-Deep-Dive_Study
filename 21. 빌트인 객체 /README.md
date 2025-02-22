# 21. 빌트인 객체

## 1. 자바스크립트 객체 분류

1. 표준 빌트인 객체(standard built-in objects/native objects/global objects)
   - ECMAScript에 정의된 객체
   - 애플리케이션 전역의 공통 기능 제공
   - JS 실행 환경(브라우저 또는 Node.js 환경)과 관계없이 언제나 사용 가능
   - 전역 객체의 프로퍼티로서 제공이 되기 때문에 별도 선언 없이 전역 변수처럼 언제나 참조 가능
2. 호스트 객체(host objects)
  - ECMAScript 사양에 정의되어 있지 않지만, JS 실행 환경에서 추가로 제공하는 객체
3. 사용자 정의 객체(user-defined objects)
  - 표준 빌트인 객체와 호스트 객체러럼 기본 제공되는 객체가 아닌 사용자가 직접 정의한 객체

## 2. 표준 빌트인 객체

JS는 `Object, String, Number, Boolean, Symbol, Date, Math, RegExp, Array, Map/Set, WeakMap/WeakSet, FUnction, Promise, Reflect, Proxy, JSON, Error` 등 40 여개 표준 빌트인 객체 제공

이 중 `Math, Reflect, JSON`을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 __생성자 함수 객체__ 이며, 이는 _프로토타입 메서드와 정적 메서드를 제공한다._
그에 반해 __생성자 함수 객체가 아닌__ 표준 빌트인 객체는 _정적 메서드만 제공한다._

```js
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee'); // String {"Lee"}
console.log(typeof strObj);       // object

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123); // Number {123}
console.log(typeof numObj);     // object

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj= new Boolean(true); // Boolean {true}
console.log(typeof boolObj);      // object

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function('x', 'return x * x'); // ƒ anonymous(x )
console.log(typeof func);                       // function

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3); // (3) [1, 2, 3]
console.log(typeof arr);        // object

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i); // /ab+c/i
console.log(typeof regExp);         // object

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();  // Fri May 08 2020 10:43:25 GMT+0900 (대한민국 표준시)
console.log(typeof date); // object
```

생성자 함수인 표준 빌트인 객체가 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 prototype 프로퍼티에 바인딩된 객체이다. 
이는 다양한 빌트인 프로토타입 메서드를 제공하며, 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공한다.

```js
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee'); // String {"Lee"}

// String 생성자 함수를 통해 생성한 strObj 객체의 프로토타입은 String.prototype이다.
console.log(Object.getPrototypeOf(strObj) === String.prototype); // true
```
```js
// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(1.5); // Number {1.5}

// toFixed는 Number.prototype의 프로토타입 메서드다.
// Number.prototype.toFixed는 소수점 자리를 반올림하여 문자열로 반환한다.
console.log(numObj.toFixed()); // 2

// isInteger는 Number의 정적 메서드다.
// Number.isInteger는 인수가 정수(integer)인지 검사하여 그 결과를 Boolean으로 반환한다.
console.log(Number.isInteger(0.5)); // false
```

## 3. 원시값과 래퍼 객체(wrapper object)

`String, Number, Boolean`의 경우 원시값이 이미 존재하지만 각각의 객체를 생성하는 표준 빌트인 생성자 함수도 존재한다.

원시값은 마치 객체처럼 마침표 표기법(or 대괄호 표기법)으로 접근이 가능하다. 이는 JS 엔진이 일시적으로 원시값을 연관된 `객체`로 변환해주기 때문이다.

_즉, 원시값을 객체처럼 사용하면 JS 엔진은 암묵적으로 연관된 객체를 생성해 생성된 객체로 프로퍼티에 접근하거나 메서드를 호출하고 다시 원시값으로 되돌린다._

이처럼 문자열, 숫자, 불리언 값에 대해 객체처럼 접근하면 생성되는 임시 객체를 __래퍼 객체(wrapper object)__ 라고 한다. 

```js
const str = 'hello';

// 원시 타입인 문자열이 프로퍼티와 메서드를 갖고 있는 객체처럼 동작한다.
console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO
```
예를 들어, 문자열에 대해 마침표 푝법으로 접근하면 그 순간 래퍼 객체인 String 생성자 함수의 인스턴스가 생성이 되고 문자열은 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된다.
이때 문자열 래퍼 객체인 String 생성자 함수의 인스턴스는 `String.prototype` 메서드를 상속받아 사용할 수 있다.
```js
//1. 식별자 str은 문자열을 값으로 가지고 있다.
const str = 'hello';

//2. 식별자 str은 암묵적으로 생성된 래퍼 객체를 가리킨다.
//식별자 str의 값 'hello'는 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된다.
//래퍼 객체에 name 프로퍼티 동적 추가
str.name = 'Lee';

//3. 식별자 str은 다시 원래의 문자열, 즉, 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된 우너시값을 갖는다.
//이때 2에서 생성된 래퍼 객체는 아무도 참조하지 않는 상태이므로 가비지 컬랙션의 대상이 된다.

//4. 식별자 str은 새롭게 암묵적으로 생성된(2에서 생성된 래퍼 객체와는 다른) 래퍼 객체를 가리킨다.
//새롭게 생성된 래퍼 객체에는 name 프로퍼티가 없다!
console.log(str.name); //undefined

//5. 식별자 str은 다시 원래의 문자열, 즉 래퍼 객체의 [[StringData]] 내부 슬롯에 할당된 원시값을 갖는다.
//이때 4에서 생성된 래퍼 객체는 아무도 참조하지 않는 상태이므로 가비지 컬렉션의 대상이 된다.
console.log(typeof str, str); //string hello
```

숫자도 문자열과 마찬가지로 작동한다.

불리언 값은 문자열과 숫자와 마찬가지이지만 불리언 값으로 메서드를 호출하는 경우는 없으므로 그다지 유용하지는 않다.

ES6에서 새롭게 도입된 원시값인 심벌도 래퍼 객체를 생성하지만 리터럴 표기법으로 생성할 수 있고 Symbol 함수를 통해 생성해야 하므로 다른 원시값과는 차이가 있다.

_따라서 String, Number, Boolean 생성자 함수를 `new` 연산자와 함께 호출하여 문자열, 숫자, 불리언 인스턴스를 생성할 필요가 없으며 권장하지도 않는다._

## 4. 전역 객체

전역 객체(global object)는 코드가 실행되기 이전 단계에 JS 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체이다.
__전역 객체는 계층적 구조상 어떤 객체에도 속하지 않은 모든 빌트인 객체(표준 빌트인 객체와 호스트 객체)의 최상위 객체이다.__

브라우저 환경에서는 window가 전역 객체를 가리키지만 Node.js 환경에서는 global이 전역 객체를 가리킨다. 

- 전역 객체의 프로퍼티
  - 표준 빌트인 객체(Object, String, Number, Function, Array)
  - 환경에 따른 호스트 객체(클라이언트 Web API 또는 Node.js의 호스트 API)
  - `var` 키워드로 선언한 전역 변수와 전역 함수

- 전역 객체의 특징
  - 개발자가 의도적으로 생성할 수 없다. 즉, 전역 객체를 생성할 수 있는 생성자 함수 X
  - 전역 객체 프로퍼티 참조 시 `window` 생략 가능
    ```js
    window.parseInt('F', 16); //15
    //window 생략 가능
    parseInt('F', 16); //15

    window.parseInt === parseInt; //true
    ```
  - 전역 객체는 `Object, String, Number, Boolean, Function, Array, RegExp, Date, Math, Promise`와 같은 모든 표준 빌트인 객체를 프로퍼티로 가짐
  - JS 실행 환경에 따라 추가적으로 프로퍼티와 메서드를 가짐
  - `var` 키워드로 선언한 전역 변수와 선언하지 않은 변수에 값을 할당한 _암묵적 전역_, 그리고 전역 함수는 전역 객체의 프로퍼티가 됨
    ```js
    // var 키워드로 선언한 전역 변수
    var foo = 1;
    console.log(window.foo); // 1
    
    // 선언하지 않은 변수에 값을 암묵적 전역. bar는 전역 변수가 아니라 전역 객체의 프로퍼티다.
    bar = 2; // window.bar = 2
    console.log(window.bar); // 2
    
    // 전역 함수
    function baz() {
        return 3;
    }
    console.log(window.baz()); // 3
    ```
  - `let`이나 `const` 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아님. 즉, `window.foo`처럼 접근 불가. `let`이나 `const` 키워드로 선언한 전역 변수는 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드) 내에 존재하기 때문이다.
    ```js
    let foo = 123;
    console.log(window.foo); //undefined
    ```
  - 브라우저 환경의 모든 JS 코드는 하나의 전역 객체 `window`를 공유한다. 여러 개의 script 태그를 통해 JS 코드를 분리해도 하나의 전역 객체 window를 공유하는 것은 변함이 없다. 즉, 분리되어 있는 JS 코드가 하나의 전역을 공유한다는 의미이다.
 
### 빌트인 전역 프로퍼티
 
  - 빌트인 전역 프로퍼티(built-in global property)는 전역 객체의 프로퍼티를 의미한다.
 
  - Infinity
      - 무한대를 나타내는 숫자값 Infinity를 갖는다.
        ```js
        // 전역 프로퍼티는 window를 생략하고 참조할 수 있다.
        console.log(window.Infinity === Infinity); // true
        
        // 양의 무한대
        console.log(3/0);  // Infinity
        // 음의 무한대
        console.log(-3/0); // -Infinity
        // Infinity는 숫자값이다.
        console.log(typeof Infinity); // number
        ```

  - NaN
    - NaN 프로퍼티는 숫자가 아님을 나타내는 숫자값 NaN을 갖는다. NaN 프로퍼티는 Number NaN 프로퍼티와 동일하다.
      ```js
      console.log(window.Nan); //NaN

      console.log(Number('xyz')); //NaN
      console.log(1 * 'string'); //NaN
      console.log(typeof NaN); //number
      ```
  - undefined
    - 원시 타입 undefined를 값으로 갖는다.
      ```js
      console.log(window.undefined); // undefined
      
      var foo;
      console.log(foo); // undefined
      console.log(typeof undefined); // undefined
      ```

### 빌트인 전역 함수

빌트인 전역 함수(built-in global function)는 애플리케이션 전역에서 호출할 수 있는 빌트인 함수로서 전역 객체의 메서드다. 

#### eval

eval 함수는 JS 코드를 나타내는 문자열을 인수로 전달받는다. 전달받은 문자열 코드가 표현식이라면 eval 함수는 문자열 코드를 런타임에 평가해 값을 생성하고 전달받은 인수가 표현식이 아닌 문이면 문자열 코드를 런타임에 실행한다.
문자열 코드가 여러 개의 문을 이뤄져 있다면 모든 문을 실행한다.

```js
/**
 * 주어진 문자열 코드를 런타임에 평가 또는 실행한다.
 * @param {string} code - 코드를 나타내는 문자열
 * @return {*} 문자열 코드를 평가/실행한 결과값
 */
eval(code);
```
```js
// 표현식인 문
eval("1 + 2;"); // -> 3
// 표현식이 아닌 문
eval("var x = 5;"); // -> undefined

// eval 함수에 의해 런타임에 변수 선언문이 실행되어 x 변수가 선언되었다.
console.log(x); // 5

// 객체 리터럴은 반드시 괄호로 둘러싼다.
const o = eval("({ a: 1 })");
console.log(o); // {a: 1}

// 함수 리터럴은 반드시 괄호로 둘러싼다.
const f = eval("(function() { return 1; })");
console.log(f()); // 1

//여러개의 문으로 이루어져 있다면 모든 문을 실행한 다음, 마지막 결과값을 반환한다.
console.log(eval("1 + 2; 3 + 4;")); // 7
```
__eval 함수는 자신이 호출된 위치에 해당하는 기존의 스코프를 런타임에 동적 수정한다.__
```js
const x = 1;

function foo() {
  // eval 함수는 런타임에 foo 함수의 스코프를 동적으로 수정한다.
  eval('var x = 2;');
  console.log(x); // 2
}

foo();
console.log(x); // 1
```
함수는 원래 호출이 되면 런타임 이전에 먼저 함수 몸체 내부 선언문을 모두 선실행하고 그 결과를 스코프에 등록한다. 때문에 eval 함수가 호출이 될 때는 이미 foo 함수의 스코프가 존재한다.
__하지만 eval 함수는 기존의 스코프를 런타임에 동적 수정하고,__ eval 함수에 전달된 코드는 이미 그 위치에 존재하던 코드처럼 동작하기 떄문에 eval 함수가 호출된 foo 함수의 스코프에서 실행된다. 

하지만 strict mode에서 eval 함수는 기존의 스코프를 수정하지 않고 eval 함수 자신의 자체적인 스코프를 생성한다.
```js
const x = 1;

function foo() {
  'use strict';

  // strict mode에서 eval 함수는 기존의 스코프를 수정하지 않고 eval 함수 자신의 자체적인 스코프를 생성한다.
  eval('var x = 2; console.log(x);'); // 2
  console.log(x); // 1
}

foo();
console.log(x); // 1
```

또한 인수로 전달받은 문자열 코드가 `let`, `const` 키워드를 사용한 변수 선언문이라면 암묵적으로 strict mode가 적용된다.
```js
const x = 1;

function foo() {
  eval('var x = 2; console.log(x);'); // 2
  // let, const 키워드를 사용한 변수 선언문은 strict mode가 적용된다.
  eval('const x = 3; console.log(x);'); // 3
  console.log(x); // 2
}

foo();
console.log(x); // 1
```

🔥 __eval 함수를 통해 사용자로부터 입력받은 콘텐츠를 실행하는 것은 보안에 매우 취약하다! 그리고 처리 속도가 느리기 때문에 eval 함수의 사용은 금지해야 한다.__

#### isFinite

전달받은 인수가 정상적인 유한수인지 검사하여 유한수이면 true를 반환하고 무한수이면 false를 반환한다. 
전달받은 인수의 타입이 숫자가 아닌 경우, 숫자로 타입 변환 후 검사를 수행한다. NaN이면 false를 반환한다.

```js
// 인수가 유한수이면 true를 반환한다.
isFinite(0);    // -> true
isFinite(2e64); // -> true
isFinite('10'); // -> true: '10' → 10(문자열->숫자)
isFinite(null); // -> true: null → 0(null->0)

// 인수가 무한수 또는 NaN으로 평가되는 값이라면 false를 반환한다.
isFinite(Infinity);  // -> false
isFinite(-Infinity); // -> false

// 인수가 NaN으로 평가되는 값이라면 false를 반환한다.
isFinite(NaN);     // -> false
isFinite('Hello'); // -> false
isFinite('2005/12/12'); // -> false
```

#### isNaN

전달받은 인수가 NaN인지 검사하여 그 결과를 불리언 타입으로 반환한다.
전달받은 인수의 타입이 숫자가 아닌 경우 숫자로 타입을 변환한 후 검사를 수행한다.

```js
// 숫자
isNaN(NaN); // -> true
isNaN(10);  // -> false

// 문자열
isNaN('blabla'); // -> true: 'blabla' => NaN
isNaN('10');     // -> false: '10' => 10
isNaN('10.12');  // -> false: '10.12' => 10.12
isNaN('');       // -> false: '' => 0
isNaN(' ');      // -> false: ' ' => 0

// 불리언
isNaN(true); // -> false: true → 1
isNaN(null); // -> false: null → 0

// undefined
isNaN(undefined); // -> true: undefined => NaN

// 객체
isNaN({}); // -> true: {} => NaN

// date
isNaN(new Date());            // -> false: new Date() => Number
isNaN(new Date().toString()); // -> true:  String => NaN
```

#### parseFloat

전달받은 문자열 인수를 부동 소수점 숫자(floating point number), 즉 실수로 해석(parsing)하여 반환한다.

```js
// 문자열을 실수로 해석하여 반환한다.
parseFloat('3.14');  // -> 3.14
parseFloat('10.00'); // -> 10

// 공백으로 구분된 문자열은 첫 번째 문자열만 변환한다.
parseFloat('34 45 66'); // -> 34
parseFloat('40 years'); // -> 40

// 첫 번째 문자열을 숫자로 변환할 수 없다면 NaN을 반환한다.
parseFloat('He was 40'); // -> NaN

// 앞뒤 공백은 무시된다.
parseFloat(' 60 '); // -> 60
```

#### parseInt
전달받은 문자열 인수를 정수(integer)로 해석(parsing)하여 반환한다. 또, 두 번째 인수로 진법을 나타내는 기수(2-36)을 전달할 수 있다.
```js
// 문자열을 정수로 해석하여 반환한다.
parseInt('10');     //10
parseInt('10.123'); //10

parseInt(10);     //10
parseInt(10.123); //10

// 10'을 10진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10'); //10
// '10'을 2진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10', 2); //2
// '10'을 8진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10', 8); //8
// '10'을 16진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10', 16); //16
```
참고로 기수를 지정해 10진수 숫자를 해당 기수의 문자열로 변환해 반환하고 싶을 때는 `Number.prototype.toString` 메서드를 사용한다.
```js
const x = 15;

// 10진수 15를 2진수로 변환하여 그 결과를 문자열로 반환한다.
x.toString(2); // -> '1111'
// 문자열 '1111'을 2진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString(2), 2); // -> 15

// 10진수 15를 8진수로 변환하여 그 결과를 문자열로 반환한다.
x.toString(8); // -> '17'
// 문자열 '17'을 8진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString(8), 8); // -> 15

// 10진수 15를 16진수로 변환하여 그 결과를 문자열로 반환한다.
x.toString(16); // -> 'f'
// 문자열 'f'를 16진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString(8), 8); // -> 15

// 숫자값을 문자열로 변환한다.
x.toString(); // -> '15'
// 문자열 '15'를 10진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString()); // -> 15
```

#### encodeURI / decodeURI

encodeURI 함수는 완전한 URI를 문자열로 전달받아 이스케이프처리를 위해 인코딩한다. URI는 인터넷에 있는 자원을 나타내는 유일한 주소이고, URI의 하위 개념으로 URL, URN이 있다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/439773f2-d9e4-4d9a-ac51-63fe667e0fae)

### 암묵적 전역

아래 예시를 통해 암묵적 전역의 개념을 알아보자

```js
var x = 10; // 전역 변수

function foo () {
  // 선언하지 않은 식별자에 값을 할당
  y = 20; // window.y = 20;
}
foo();

// 선언하지 않은 식별자 y를 전역에서 참조할 수 있다.
console.log(x + y); // 30
```

foo 함수 내의 y는 선언이 되지 않은 식별자이다. 따라서 `y=20;` 이 실행이 된다면 참조 에러가 발생할 것처럼 보인다. 
__하지만 선언하지 않은 식별자 y는 마치 선언된 전역 변수처럼 동작한다. 이는 선언하지 않은 식별자에 값을 할당할 경우 전역 객체의 프로퍼티가 되기 때문이다.__

👀 _함수 실행 흐름_
1. foo 함수 호출
2. JS 엔진은 y 변수에 값 할당 위해 먼저 스코프 체인을 통해 선언된 변수인지 확인
3. foo 함수 스코프와 전역 스코프 어디에서도 y 변수를 찾을 수 없으므로 참조 에러 발생
4. JS 엔진은 `y = 20;` 를 `window.y = 20;`로 해석하여 전역 객체에 프로퍼티를 _동적 생성_
5. y는 전역 객체의 프로퍼티가 되어 마치 전역 변수처럼 동작(__암묵적 전역(implicit global)__)

> 이때 y는 변수 선언 없이 단지 전역 객체 프로퍼티로 추가된 것이므로 변수는 아니다. 따라서 변수 호이스팅이나 `delete` 연산자로 지울 수 없다.
