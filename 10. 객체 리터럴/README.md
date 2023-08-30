# 10. 객체 리터럴

## 1) 객체란? 


자바스크립트는 객체(object) 기반의 프로그래밍 언어이며, 원시 값을 제외한 나머지 값(함수, 배열, 정규 표현식 등)은 모두 객체이다.

__원시 타입의 값은 변경 불가능한 값(immutable value)이지만 객체는 변경 가능한 값(mutable value)이다.__

객체는 프로퍼티와 메서드로 구성된 집합체이다.
  - 프로퍼티: 객체의 상태를 나타내는 값(data)
  - 메서드: 프로퍼티(상태 데이터)를 참조하고 조작할 수 있는 동작(behavior)
    
    ```js
    var counter = {
      num: 0, //프로퍼티
      increase: function() { //메서드
        this.num++;
      }
    };
    ```

## 2) 객체 리터럴에 의한 객체 생성

C++이나 자바 같은 클래스 기반 객체지향 언어는 클래스를 사전에 정의하고 필요한 시점에 `new` 연산자와 함께 생성자(constructor)을 호출해 인스턴스를 생성해야 객체를 생성할 수 있다. 
하지만 JS의 경우 클래스 기반이 아닌 __프로토타입 기반 객체지향 언어로서 다양한 객체 생성 방법이 있다.__

  - 객체 리터럴✨ (가장 일반적이고 간단함)
  - Object 생성자 함수
  - 생성자 함수
  - Object.create 메서드
  - 클래스(ES6)

📌 __객체 리터럴__

> 리터럴(literal) : 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용하여 값을 생성하는 표기법

- 변수에 할당되는 시점에 JS 엔진은 객체 리터럴을 해석해 객체를 생성한다.
- 객체 리터럴의 중괄호는 코드 블록을 의미하지 않는다! <br>
  - 객체 리터럴은 _값으로 평가되는 표현식_ 이기 때문에 중괄호 뒤에 `;` 을 붙여줘야 한다.

 
    ```js
    var person = {
      name: 'Lee',
      sayHello: function() {
        console.log(`Hello! My name is  ${this.name}.`);
      }
    };
  
    console.log(typeof person); //object
    console.log(person); // {name: 'Lee', sayHello: f}
    ```

## 3) 프로퍼티

_객체는 프로퍼티의 집합이며, 프로퍼티는 키와 값으로 구성된다._

- __프로퍼티 키__: 빈 문자열을 포함하는 모든 문자열 또는 심벌 값
  👉 프로퍼티 값에 접근할 수 있는 이름으로서 식별자 역할을 함
- __프로퍼티 값__: JS에서 사용할 수 있는 모든 값
  
  ```js
  //프로퍼티를 나열할 때는 쉽표로 구분한다. 마지막은 생략해도 된다. 
  var person = {
    name: 'Lee', //프로퍼티 키는 name, 프로퍼티 값은 'Lee'
    age: 20
  };
  ```

🧩 프로퍼티 키에 관해 더 알아보자!
- 프로퍼티 키는 _식별자 네이밍 규칙을 따라야 한다. 따르지 않는다면 반드시 따옴표를 사용해야 한다._ <br>
  - `'last-name': 'Lee'` : 이는 식별자 네이밍 규칙을 준수하지 않는 프로퍼티 키이다. 자바스크립트 엔진은 따옴표를 생략한 last-name을 보고 `-` 연산자가 있는 표현식으로 해석해버린다.
- 문자열 혹은 문자열로 평가할 수 있는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 수 있다. 이 경우에는 프로퍼티 키로 사용할 표현식을 대괄호`[...]`로 묶어야 한다. <br>

    ```js
    var obj = {};
    var key = 'hello';

    //ES5: 프로퍼티 키 동적 생성
    obj[key] = 'world';
    //ES6: 계산된 프로퍼티 이름
    //var obj = { [key]: 'world' };

    console.log(obj); //{ hello: 'world' }
    ```
- 프로퍼티 키에 문자열이나 심벌 값 외에 값을 사용할 경우 암묵적 타입 변환을 통해 문자열이 된다.
- 이미 존재하는 프로퍼티 키를 중복 선언할 경우 에러가 나는 것이 아니라 나중에 선언한 프로퍼티가 먼저 선언한 프로퍼티를 덮어쓴다. 


## 4) 메서드

__메서드란 객체에 묶여 있는 함수이다.__

```js
var circle = {
  radius : 5, //프로퍼티
  //원의 지름
  getDiameter : function(){ //메서드
    return 2 * this.radius; //this는 circle을 가리킨다.
  }
};

console.log(circle.getDiameter()); //10
```
## 5) 프로퍼티 접근

- 마침표 프로퍼티 접근 연산자 `.`를 사용하는 __마침표 표기법(dot notation)__
  - 식별자 네이밍 규칙을 준수하는 이름일 때만 ⭕
- 대괄호 프로퍼티 접근 연산자 `[...]`를 사용하는 __대괄호 표기법(bracket notation)__
  - 식별자 네이밍 규칙을 준수 여부 상관없이 ⭕
  - _대괄호 프로퍼티 접근 연산자 내부에 지정하는 프로퍼티 키는 __반드시 따옴표로 감싼 문자열__ 이여야 한다._ 감싸지 않으면 식별자로 해석된다.
    
  ```js
  var person = {
    name: 'Lee'
  };

  //마침표 표기법에 의한 프로퍼티 접근
  console.log(person.name); //Lee

  //대괄호 표기법에 의한 프로퍼티 접근
  console.log(person['name']); //Lee
  ```

- __객체에 존재하지 않는 프로퍼티 접근 시 `undefined`를 반환한다. `ReferenceError`가 아니라!__
  ```js
  var person = {
    'last-name': 'Lee',
    1: 10
  };
  
  person.'last-name';  // -> SyntaxError: Unexpected string
  person.last-name;    // -> 브라우저 환경: NaN
                       // -> Node.js 환경: ReferenceError: name is not defined
  person[last-name];   // -> ReferenceError: last is not defined
  person['last-name']; // -> Lee
  
  // 프로퍼티 키가 숫자로 이뤄진 문자열인 경우 따옴표를 생략할 수 있다.
  person.1;     // -> SyntaxError: Unexpected number
  person.'1';   // -> SyntaxError: Unexpected string
  person[1];    // -> 10 : person[1] -> person['1']
  person['1'];  // -> 10
  ```

  <details>
  <summary>Person.last-name 의 결과가 Node.js 환경과 브라우저 환경에서 각각 다른 이유 </summary>
    
    1. `person.last-name`을 실행 시, 자바스크립트 엔진은 먼저 `person.last`를 평가한다.     <br>
    2. person 객체에는 "last"라는 key가 없으므로 `person.last`는 `undefined`로 해석된다.  <br>
    3. 따라서 `person.last-name`은 `undefined - name`과 같다. <br>
    4. 자바스크립트 엔진은 `name`이라는 __식별자__ 를 찾는다. (프로퍼티 키 X) <br>
  
  -   Node.js 환경에는 현재 어디에도 `name`이라는 식별자 선언이 없어 `ReferenceError: name is not defined`가 나타난다
  -   하지만 브라우저 환경에서는 `name`이라는 전역 변수(전역 객체 `window`의 프로퍼티)가 암묵적으로 존재한다. 이는 window의 이름을 가리키며, 기본값은 빈 문자열이다. 따라서 `undefined - ''`가 되어 `NaN`이 된다.
  </details>

## 6) 프로퍼티 값 갱신

이미 존재하는 프로퍼티에 값을 할당하면 프로퍼티 값이 갱신된다. 

```js
var person = {
  name: 'Lee'
};

//person 객체에 name 프로퍼티 존재하므로 name 프로퍼티 값 갱신
person.name = 'Kim';

console.log(person); // {name: 'Kim'}

```

## 7) 프로퍼티 동적 생성

존재하지 않는 프로퍼티에 값을 할당 시 프로퍼티가 동적으로 생성되어 추가되고 프로퍼티 값이 할당된다.

```js
var person = {
  name: 'Lee'
};

//person 객체에는 age 프로퍼티가 존재하지 않으므로 person 객체에 해당 프로퍼티가 동적으로 생성되고 값이 할당된다.
person.age = 20;

console.log(person); // {name: 'Lee', age: 20}

```

## 8) 프로퍼티 삭제

`delete` 연산자는 객체의 프로퍼티를 삭제한다. 단, `delete` 연산자의 피연산자는 프로퍼티 값에 접근할 수 있는 표현식이여야하는데, 만약 없는 프로퍼티를 삭제하면 에러 없이 무시된다.

```js
var person = {
  name: 'Lee'
};

//프로퍼티 동적 생성
person.age = 20;

//프로퍼티 삭제
delete person.age;

//존재하지 않는 프로퍼티 삭제했으므로 무시
delete person.address;

console.log(person); //{name : 'Lee'}

```

## 9) ES6에서 추가된 객체 리터럴의 확장 기능

__1) 프로퍼티 축약 표현__

객체 리터럴의 프로퍼티 값은 변수에 할당된 값, 즉 식별자 표현식일수도 있다.
이때, ES6에서 프로퍼티 값으로 변수를 사용하는 경우 변수 이름과 프로퍼티 키가 동일한 이름일 때 프로퍼티 키를 생략(property shorthand)할 수 있다. _이때 프로퍼티 키는 변수 이름으로 자동 생성된다._

```js
//ES5
var x = 1, y = 2;

var obj = {
  x: x,
  y: y
};

console.log(obj); // {x: 1, y:2}

//ES6
let x = 1, y = 2;

//프로퍼티 축약 표현
const obj = {x, y}; //여기서 x, y는 프로퍼티 값인데 변수이므로 프로퍼티 키는 자동으로 변수 이름으로 생성됨. 
console.log(obj); // {x : 1, y : 2}
```

__2) 계산된 프로퍼티 이름__

문자열로 타입 변환할 수 있는 값으로 평가되는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 수도 있다. 단, 대괄호 `[...]` 로 묶어야 한다.

```js
// ES5
var prefix = 'prop';
var i = 0;

var obj = {};

// 계산된 프로퍼티 이름으로 프로퍼티 키 동적 생성하기. 객체 리터럴 외부에서 대괄호 표기법을 사용한다.
obj[prefix + '-' + ++i] = i;
obj[prefix + '-' + ++i] = i;
obj[prefix + '-' + ++i] = i;

console.log(obj); // {prop-1: 1, prop-2: 2, prop-3: 3}

// ES6
const prefix = 'prop';
let i = 0;

// 객체 리터럴 내부에서 계산된 프로퍼티 이름으로 프로퍼티 키 동적 생성
const obj = {
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
};

console.log(obj); // {prop-1: 1, prop-2: 2, prop-3: 3}

```

__3) 메서드 축약 표현__

ES5에서는 메서드 정의 시 프로퍼티 값으로 함수를 할당했지만 ES6에서는 메서드 정의 시 function 키워드를 생략한 축약 표현을 사용할 수 있다. 
이때, 메서드 축약 표현으로 정의한 메서드는 프로퍼티에 할당한 함수와 다르게 동작한다. 

```js
// ES5
var obj = {
    name: 'Lee',
    sayHi: function () {
        console.log('Hi! ' + this.name);
    }
};

obj.sayHi(); // Hi! Lee

const obj = {
    name: 'Lee',
    // 메서드 축약 표현
    sayHi() {
        console.log('Hi! ' + this.name);
        //console.log(`Hi! ${ this.name}`); 이렇게 써도 된다. 
    }
};

obj.sayHi(); // Hi! Lee

```






