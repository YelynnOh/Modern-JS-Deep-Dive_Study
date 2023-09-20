# 26. ES6 함수의 추가 기능

## 1. 함수의 구분

ES6 이전까지 js 함수는 별다른 구분 없이 다양한 목적으로 사용되었다.
JS 함수는 일반적인 함수로 호출할 수도 있고, `new` 연산자를 사용하는 생성자 함수로도 호출할 수 있었으며, 객체에 바인딩되어 메서드로서 호출할 수도 있었다.

즉, ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로 호출할 수 있었다. 다시 말해 모든 함수는 `callable` 이면서 `constructor`이다.

주의할 것은 ES6 이전에 일반적으로 메서드라고 부르던 객체에 바인딩된 함수도 `callable`이며 `constructor`이다. 따라서 객체에 바인딩된 함수도 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.

```js
//프로퍼티 f에 바인딩된 함수는 callable 이자 constructor
var obj = {
  x: 10,
  f: function () {return this.x;}
};

//프로퍼티 f에 바인딩된 함수를 메서드로서 호출
console.log(obj.f()); //10

//일반 함수로서 호출
var bar = obj.f;
console.log(bar()); //undefined

//생성자 함수로서 호출
console.log(new obj.f()); //f{}
```

__하지만 객체에 바인딩된 함수를 생성자 함수로 호출하는 경우가 가능하다는 것은 문제가 있고, 성능 면에서도 문제가 있다.__
_객체에 바인딩된 함수가 constructor이라는 것은 객체에 바인딩된 함수가 prototype 프로퍼티를 가지며, 프로토타입 객체도 생성한다는 것을 의미하기 떄문이다._

_콜백 함수_ 도 마찬가지다. 콜백 함수도 `constructor`이기 떄문에 불필요한 프로토타입 객체를 생성한다. 
```js
//콜백 함수를 사용하는 고차 함수 map. 콜백 함수도 constructor이며 프로토타입을 생성한다.
[1, 2, 3].map(function (item) {
  return item * 2;
}); //[2, 4, 6]
```

__이처럼 ES6 이전의 모든 함수는 사용 목적에 따라 명확한 구분이 없어 호출 방식에 제약이 없었다. 이는 혼란과 실수를 유발하며, 성능에도 좋지 않다.__

__이에 ES6에서는 함수를 사용 목적에 따라 세 가지 종류로 명확하게 구분한다.__
| ES6 함수 구분 | constructor | prototype | super | arguments |
| --- | --- | --- | --- | --- |
| 일반 함수(Normal) | O | O | X | O |
| 메서드(Method) | X | X | O | O |
| 화살표 함수(Arrow) | X | X | X | X |

## 2. 메서드

__ES6에서 메서드란 메서드 축약 표현으로 정의된 함수이다.__
```js
const obj = {
  x:1,
  //foo는 메서드
  foo() {return this.x;},
  //bar에 바인딩된 함수는 메서드가 아닌 일반 함수다.
  bar: function() {return this.x;}
};

console.log(obj.foo()); //1
console.log(obj.bar()); //1
```
> 메서드 특징
1) 메서드는 인스턴스를 생성할 수 없는 `non-constructor`이다. 따라서 생성자 함수로서 호출할 수 없다.
   ```js
   new obj.foo(); //TypeError: obj.foo is not a constructor
   new obj.bar(); //bar {}
   ```
2) 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.
   ```js
   //obj.foo는 constructor가 아닌 ES6 메서드이므로 prototype 프로퍼티가 없다.
   obj.foo.hasOwnProperty('prototype'); // false
   ```
   - 참고로 표준 빌트인 객체가 제공하는 프로토타입 메서드와 정적 메서드는 모두 `non-constructor`라고 한다.
3) 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다. `super` 참조는 내부 슬롯 `[[HomeObject]]`를 사용해 수퍼클래스의 메서드를 참조하므로 메서드는 `super` 키워드를 사용할 수 있다.
  ```js
  const base = {
    name: 'Lee',
    sayHi() {
      return `Hi! ${this.name}`;
    }
  };
  
  const derived = {
    __proto__: base,
    // sayHi는 ES6 메서드다. ES6 메서드는 [[HomeObject]]를 갖는다.
    // sayHi의 [[HomeObject]]는 sayHi가 바인딩된 객체인 derived를 가리키고
    // super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킨다.
    sayHi() {
      return `${super.sayHi()}. how are you doing?`;
    }
  };
  
  console.log(derived.sayHi()); // Hi! Lee. how are you doing?
  const derived = {
    __proto__: base,
    // sayHi는 ES6 메서드가 아니다.
    // 따라서 sayHi는 [[HomeObject]]를 갖지 않으므로 super 키워드를 사용할 수 없다.
    sayHi: function () {
      // SyntaxError: 'super' keyword unexpected here
      return `${super.sayHi()}. how are you doing?`;
    }
  };
  ```
  - __ES6 메서드가 아닌 함수는 `super` 키워드를 사용할 수 없다. 내부 슬롯 `[[HomeObject]]`가 없기 때문이다.__

__이처럼 메서드는 본연의 기능(`super`)을 추가하고 의미적으로 맞지 않는 기능(constructor)는 제거했다.__

## 3. 화살표 함수

__화살표 함수(arrow function)는 `function` 키워드 대신 화살표(`=>`, fat arrow)를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있다.__
표현만 간략히 하는 것이 아니라 내부 동작도 기존 함수보다 간략하다. 특히 콜백 함수는 내부에서 `this`가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용한다.

### 화살표 함수 정의

#### 함수 정의

함수 선언문으로 정의 못하고 _함수 표현식으로 정의해야한다._

```js
const multiply = (x, y) => x * y;
multiply(2, 3); // 6
```

#### 매개변수 선언

- 매개변수가 여러 개인 경우 소괄호 `()` 안에 매개변수를 선언한다.
  ```js
  const arrow = (x, y) => {...};
  ```
- 매개변수가 한 개이면 소괄호 `()`를 생략할 수 있다.
  ```js
  const arrow = x => { ... };
  ```
- 매개변수가 없는 경우 소괄호 `()`를 생략할 수 없다.
  ```js
  const arrow = () => { ... };
  ```

#### 함수 몸체 정의

- 함수 몸체가 하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 `{}`를 생략할 수 있다. 이때, 함수 몸체 내부의 문이 값으로 평가될 수 있는 표현식인 문이라면 암묵적으로 반환된다.
  ```js
  //concise바디는 중괄호'{}'로 묶이지않은 한줄짜리 바디이고 block바디는 중괄호로 묶인 바디
  //concise body
  const power = x => x ** 2;
  power(2); // 4

  //위 표현은 다음과 동일하다.
  //block body
  const power = x => { return x ** 2; };
  ```
- 함수 몸체를 감싸는 중괄호 `{}`를 생략한 경우 함수 몸체 내부의 문이 표현식이 아닌 문이라면 에러가 발생한다. 표현식이 아닌 문은 반환할 수 없기 때문이다.
  ```js
  const arrow = () => const x = 1; //SyntaxError: unexpected token 'const'

  //위 표현은 아래와 같이 해석
  const arrow = () => {return const x = 1; };

  //이렇게 써야 된당
  const arrow = () => {const x = 1;};
  ```
- 객체 리터럴 반환하는 경우 객체 리터럴을 소괄호 `()`로 감싸 주어야 한다.
  ```js
  const create = (id, content) => ({id, content});
  create(1, 'Javascript'); // {id: 1, content: "Javascript"}

  //위 표현은 다음과 동일하다
  const create = (id, content) => {return {id, content};};
  ```
  만약 소괄호로 감싸주지 않으면 객체 리터럴의 중괄호 `{}`를 함수 몸체를 감싸는 중괄호 `{}`로 잘못 해석한다.
  ```js
  //{id, content}를 함수 몸체 내의 쉼표 연산자문으로 해석
  const create = (id, content) => {id, content};
  create(1, 'Javascript'); //undefined
  ```
- 함수 몸체가 여러 개의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 `{}`를 생략할 수 없다! 반환값이 있다면 명시적으로 반환한다.
  ```js
  const sum = (a, b) => {
    const result = a + b;
    return result;
  };  
  ```
- 화살표 함수도 즉시 실행 함수(IFE)로 사용할 수 있다.
  ```js
  const person = (name => ({
    sayHi() {return `Hi? My name is ${name}.`;}
  }))('Lee');

  console.log(person.sayHi()); //Hi? My name is Lee.
  ```
- 화살표 함수도 일급 객체이므로 `Arrow.prototype.map`, `Array.prototype.filter`, `Array.prototype.reduce` 같은 고차 함수(Higher-Order function, HDF)에 인수로 전달할 수 있다. 이 경우 일반적인 함수 표현식보다 표현이 간결하고 가독성이 좋다.
  ```js
  //ES5
  [1, 2, 3].map(function(v){
    return v * 2;
  });

  //ES6
  [1, 2, 3].map(v => v * 2); //2, 4, 6
  ```

#### 화살표 함수 vs 일반 함수

1. 화살표 함수는 인스턴스를 생성할 수 없는 `non-constructor`이다.
   - 때문에 인스턴스를 생성할 수 없고, prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.
2. 화살표 함수는 중복된 매개변수 이름을 선언할 수 없다.
   - 일반 함수는 중복된 매개변수 이름을 선언해도 에러가 발생하지 않는다.
     `function normal(a, a) {return a + a; }` 이런식으로.
   - 단, strict mode에서 중복된 매개변수 이름 선언 시 에러가 발생한다.
   - 화살표 함수는 중복된 매개변수 이름 선언 시 에러가 발생한다.
3. 화살표 함수는 함수 자체의 `this, arguments, super, new.target` 바인딩 값을 갖지 않는다.
   - 따라서 화살표 함수 내부에서 `this, arguments, super, new.target` 참조 시 스코프 체인을 통해 상위 스코프의 `this, arguments, super, new.target`를 참조한다.
   - 만약 화살표 함수끼리 중첩되어 있다면 상위 화살표 함수에 `this, arguments, super, new.target` 바인딩이 없으므로 스코프 체인 상에서 가장 가까운 상위 함수 중 화살표 함수가 아닌 함수의 `this, arguments, super, new.target`를 참조한다.
  
### this

__화살표 함수의 `this`는 일반 함수의 `this`와 다르게 동작한다.__

this 바인딩은 함수의 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.

_일반 함수로서 호출되는 모든 함수 내부의 this는 전역 객체를 가리킨다. 근데 클래스 내부의 모든 코드에는 strict mode가 적용이 되기 때문에 this에는 전역 객체가 아니라 `undefined`가 바인딩된다.
따라서 일반 함수로서 콜백 함수를 실행할 경우, 콜백 함수 내부의 this에는 `undefined`가 반환된다._

_화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 this 참조 시 상위 스코프의 this를 그대로 참조한다. 이를 __lexical this__ 라고 한다._
```js
// 화살표 함수는 상위 스코프의 this를 참조한다.
() => this.x;

// 익명 함수에 상위 스코프의 this를 주입한다. 위 화살표 함수와 동일하게 동작한다.
(function () { return this.x; }).bind(this);
```

만약 화살표 함수와 화살표 함수가 중첩되어 있다면 상위 화살표 함수에도 this 바인딩이 없으므로 스코프 체인 상에서 가장 가까운 상위 함수 중 화살표 함수가 아닌 함수의 this를 참조한다.
```js
// 중첩 함수 foo의 상위 스코프는 즉시 실행 함수다.
// 따라서 화살표 함수 foo의 this는 상위 스코프인 즉시 실행 함수의 this를 가리킨다.
(function () {
  const foo = () => console.log(this);
  foo();
}).call({ a: 1 }); // { a: 1 }

// bar 함수는 화살표 함수를 반환한다.
// bar 함수가 반환한 화살표 함수의 상위 스코프는 화살표 함수 bar다.
// 하지만 화살표 함수는 함수 자체의 this 바인딩을 갖지 않으므로 bar 함수가 반환한
// 화살표 함수 내부에서 참조하는 this는 화살표 함수가 아닌 즉시 실행 함수의 this를 가리킨다.
(function () {
  const bar = () => () => console.log(this);
  bar()();
}).call({ a: 1 }); // { a: 1 }
```

만약 화살표 함수가 전역 함수라면 화살표 함수의 this는 전역 객체를 가리킨다. 전역 함수의 상위 스코프는 전역이고, 전역에서 this는 전역 객체를 가리키기 때문이다. 
```js
// 전역 함수 foo의 상위 스코프는 전역이므로 화살표 함수 foo의 this는 전역 객체를 가리킨다.
const foo = () => console.log(this);
foo(); // window
```

화살표 함수가 전역 함수라면 화살표 함수의 this는 전역 객체를 가리킨다.

프로퍼티에 할당한 화살표 함수도 스코프 체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 this를 참조한다.

```js
// increase 프로퍼티에 할당한 화살표 함수의 상위 스코프는 전역이다.
// 따라서 increase 프로퍼티에 할당한 화살표 함수의 this는 전역 객체를 가리킨다.
const counter = {
    num: 1,
    print: () => console.log(this)
};

counter.print(); // window
```

화살표 함수는 함수 자체의 this 바인딩을 갖지 않기 때문에 Function.prototype.call/apply/bind 메서드를 사용해도 화살표 함수 내부의 this를 교체할 수 없다.

```js
window.x = 1;

const normal = function () { return this.x; };
const arrow = () => this.x;

console.log(normal.call({ x: 10 })); // 10
console.log(arrow.call({ x: 10 }));  // 1
```

화살표 함수가 Function.prototype.call/apply/bind 메서드를 호출할 수 없다는 의미는 아니다. 화살표 함수는 함수 자체의 this 바인딩을 갖지 않기 때문에 this를 교체할 수 없고 언제나 상위 스코프의 this 바인딩을 참조한다.

```js
const add = (a, b) => a + b;

console.log(add.call(null, 1, 2));    // 3
console.log(add.apply(null, [1, 2])); // 3
console.log(add.bind(null, 1, 2)());  // 3
```

메서드를 화살표 함수로 정의하는 것은 피해야 한다. 메서드를 정의할 때는 ES6 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋다.

```js
// Bad
const person = {
    name: 'Lee',
    sayHi: () => console.log(`Hi ${this.name}`)
};

// sayHi 프로퍼티에 할당된 화살표 함수 내부의 this는 상위 스코프인 전역의 this가 가리키는
// 전역 객체를 가리키므로 이 예제를 브라우저에서 실행하면 this.name은 빈 문자열을 갖는
// window.name과 같다. 전역 객체 window에는 빌트인 프로퍼티 name이 존재한다.
person.sayHi(); // Hi

//Good
const person = {
  name: 'Lee',
  sayHi() {
    console.log(`Hi ${this.name}`);
  }
};

person.sayHi(); // Hi Lee
```

클래스 필드에 할당한 화살표 함수도 프로토타입 메서드가 아니라 인스턴스 메서드가 되므로 메서드를 정의할 때 ES6 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋다. 
```js
// Good
class Person {
  // 클래스 필드 정의
  name = 'Lee';

  sayHi() { console.log(`Hi ${this.name}`); }
}
const person = new Person();
person.sayHi(); // Hi Lee
```

### super

화살표 함수는 함수 자체의 super 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 super 참조 시 this와 마찬가지로 상위 스코프의 super를 참조한다.

```js
class Base {
    constructor(name) {
        this.name = name;
    }

    sayHi() {
        return `Hi! ${this.name}`;
    }
}

class Derived extends Base {
    // 화살표 함수의 super는 상위 스코프인 constructor의 super를 가리킨다.
    sayHi = () => `${super.sayHi()} how are you doing?`;
}

const derived = new Derived('Lee');
console.log(derived.sayHi()); // Hi! Lee how are you doing?
```

### arguments

화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않는다. 화살표 함수 내부에서 arguments를 참조 시 상위 스코프의 arguments를 참조한다.

```js
(function () {
    // 화살표 함수 foo의 arguments는 상위 스코프인 즉시 실행 함수의 arguments를 가리킨다.
    const foo = () => console.log(arguments); // [Arguments] { '0': 1, '1': 2 }
    foo(3, 4);
}(1, 2));

// 화살표 함수 foo의 arguments는 상위 스코프인 전역의 arguments를 가리킨다.
// 하지만 전역에는 arguments 객체가 존재하지 않는다. arguments 객체는 함수 내부에서만 유효하다.
const foo = () => console.log(arguments);
foo(1, 2); // ReferenceError: arguments is not defined
```
arguments 객체는 함수 정의 시 매개변수 갯수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다. 하지만 화살표 함수에는 이를 사용할 수 없다. 
따라서 화살표 함수에서 가변 인자 함수 구현 시 반드시 `Rest 파라미터`를 사용해야한다.

## 4. Rest 파라미터

### 기본 문법

__Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 세개의 점 `...`을 붙여서 정의한 매개변수를 의미한다. Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다.__

```js
function foo(...rest) {
  //매개변수 rest는 인수들의 목록을 배열로 전달받는 Rest 파라미터
  console.log(rest); // [1, 2, 3, 4, 5]
}

foo(1, 2, 3, 4, 5);
```

_먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당되기 때문에 Rest 파라미터는 반드시 마지막 파라미터여야 한다._

```js
function foo(param, ...rest) {
  console.log(param); // 1
  console.log(rest);  // [ 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);

function bar(param1, param2, ...rest) {
  console.log(param1); // 1
  console.log(param2); // 2
  console.log(rest);   // [ 3, 4, 5 ]
}

bar(1, 2, 3, 4, 5);
```

Rest 파라미터는 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티에 영향을 주지 않는다.

```js
function foo(...rest) {}
console.log(foo.length); // 0

function bar(x, ...rest) {}
console.log(bar.length); // 1

function baz(x, y, ...rest) {}
console.log(baz.length); // 2
```

### Rest 파라미터와 arguments 객체

가변 인자 함수에서 arguments 객체를 통해 인수를 전달받는 방법이 있다. 하지만 arguments 객체는 배열이 아닌 유사 배열 객체이므로 배열 메서드를 사용하려면 `Function.prototype.call/apply` 메서드를 사용해 arguments 객체를 배열로 변환해야 하는 번거로움이 있다.

```js
function sum() {
    // 유사 배열 객체인 arguments 객체를 배열로 변환한다.
    var array = Array.prototype.slice.call(arguments);

    return array.reduce(function (pre, cur) {
        return pre + cur;
    }, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

하지만 rest 파라미터를 사용하여 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있다. 이를 통해 유사 배열 객체인 arguments 객체를 배열로 변환하는 번거로움을 피할 수 있다.

```js
function sum(...args) {
    // Rest 파라미터 args에는 배열 [1, 2, 3, 4, 5]가 할당된다.
    return args.reduce((pre, cur) => pre + cur, 0);
}
console.log(sum(1, 2, 3, 4, 5)); // 15
```

## 5. 매개변수 기본값

JS 엔진은 매개변수의 개수와 인수의 개수를 체크하지 않는다.

인수가 전달되지 않은 매개변수의 값은 `undefined`이다. 이를 방치할 경우 의도치 않은 결과가 나올 수도 있다.

따라서 매개변수에 인수가 전달되었는지 확인하여 인수가 전달되지 않은 경우 매개변수에 기본값을 할당할 필요가 있다. _즉, 방어코드가 필요하다._

```js
function sum(x, y) {
  //인수가 전달되지 않아 매개변수 값이 undefined이면 기본값 할당
  x = x || 0;
  y = y || 0;

  return x + y;
}

console.log(sum(1, 2)); //3
console.log(sum(1)); //1
```

ES6에 도입된 매개변수 기본값 사용 시 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다.

```js
function sum(x = 0, y = 0) {
    return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1));    // 1
```

매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효하다.
```js
function logName(name = 'Lee') {
    console.log(name);
}

logName();          // Lee
logName(undefined); // Lee
logName(null);      // null
```

Rest 파라미터는 기본값을 지정할 수 없다.

```js
function foo(...rest = []) {
  console.log(rest);
}
// SyntaxError: Rest parameter may not have a default initializer
```

매개변수 기본값은 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티와 arguments 객체에 아무런 영향을 주지 않는다.
