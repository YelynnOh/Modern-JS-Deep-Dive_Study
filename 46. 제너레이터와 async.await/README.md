# 46. 제네레이터와 async/await

## 1. 제너레이터란?

**제너레이터(generator)는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다.**

📌 **_제너레이터 함수 vs 일반 함수_**
- __제너레이터 함수는 함수 호출자에게 함수 실행의 제어권 양도 가능__
    - 일반 함수: 함수 호출자(caller)가 함수를 호출한 후에는 함수 실행을 제어 X
    - 제너레이터 함수: 함수 실행을 함수 호출자가 제어할 수 있으며, 함수 실행을 일시 중지하거나 재개할 수 있다. _즉, 함수의 제어권을 함수 호출자에게 양도(yield) 가능_
- __제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.__
    - 일반 함수: 함수가 실행되고 있는 동안에는 함수 외부에서 함수 내부로 값을 전달해 함수 상태 변경 불가
    - 제너레이터 함수: 함수 호출자에게 상태를 전달하거나 함수 호출자로부터 상태 전달 받을 수도 있음
- __제너레이터 함수 호출 시 제너레이터 객체를 반환한다.__
    - 일반 함수: 함수 호출 시 함수 코드 일괄 실행 후 값을 반환함
    - 제너레이터 함수: 함수 코드 실행 X, 이터러블이면서 동시에 이터레이터인 제너레이터 객체 반환

## 2. 제너레이터 함수의 정의

1. 제너레이터 함수는 `function *` 키워드로 선언하며 하나 이상의 `yield` 표현식을 포함

```js
//제너레이터 함수 선언문
function* getDecFunc(){
    yield 1;
}

//제너레이터 함수 표현식
const genExpFunc = function* () {
    yield 1;
};

//제너레이터 메서드
const obj = {
    * genObjMethod(){
        yield 1;
    }
}

//제너레이터 클래스 메서드
class MyClass {
    * genClsMethod(){
        yield 1;
    }
}
```

2. 애스터리스크(`*`)의 위치는 `function` 키워드와 함수 이름 사이이면 상관없다. 하지만 보통 일관성을 이유로 `function` 키워드 바로 뒤에 붙이는 것을 권장. 

```js
//권장
function* getFunc() { yield 1; }
```

3. 제너레이터 함수는 화살표 함수로 정의 불가능

```js
const genArrowFunc = * => {
    yield 1;
}; //SyntaxError: Unexpected token '*'
```

4. 제너레이터 함수는 `new` 연산자와 함께 생성자 함수로 호출할 수 없음

```js
function * genFunc(){
    yield 1;
}

new genFunc(); //TypeError: genFunc is not a constructor
```

## 3. 제너레이터 객체

__제너레이터 함수 호출 시 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환한다. 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.__

즉, 제너레이터 객체는 `Symbol.iterator` 메서드를 상속받는 이터러블이면서 `value, done` 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 `next` 메서드를 소유한 이터레이터

때문에 따로 `Symbol.iterator` 메서드를 호출해 별도로 이터레이터를 생성할 필요가 없음

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환
const generator = genFunc();

// 제너레이터 객체는 이터러블이면서 동시에 이터레이터
// 이터러블은 Symbol.iterator 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체다.
console.log(Symbol.iterator in generator); // true
// 이터레이터는 next 메서드를 갖는다.
console.log('next' in generator) // true
```

__이때, 제너레이터는 이터레이터에 없는 `return, throw` 메서드를 갖는다.__

📌 __제너레이터의 메서드: next, return, throw__

- `next` : 메서드 호출 시 `yield` 표현식까지 코드 블록 실행 & yield된 값을 `value` 프로퍼티 값으로, `false`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
- `return` : 메서드 호출 시 인수로 전달받은 값을 `value` 프로퍼티 값으로 & `true`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
- `throw` : 메서드 호출 시 인수로 전달받은 에러 발생 & `undefined`를 `value` 프로퍼티 값으로 & `true`를 `done` 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
    ```js
    function genFunc() {
    try {
        yield 1;
        yield 2;
        yield 3;
    } catch (e) {
        console.error(e);
    }
    }

    const generator = genFunc();

    console.log(generator.next()); // {value: 1, done: false}
    console.log(generator.return('End!')); // {value: 'End!', done: return}
    console.log(generator.throw('Error')); // {value: undefined, done: true}
    ```

## 4. 제너레이터의 일시 중지와 재개

제너레이터는 `yield` 키워드와 `next` 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개 가능함

__제너레이터 객체의 `next` 메서드를 호출할 경우 제너레이터 함수의 코드 블록이 `yield` 표현식까지만 실행됨.__

__`yield` 키워드는 제너레이터 함수의 실행을 일시 중지시키거나 `yield` 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환함!__

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc();

// 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 첫 번째 yield 표현식에서 yield된 값 1이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 1, done: false}

// 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 두 번째 yield 표현식에서 yield된 값 2가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 2, done: false}

// 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 세 번째 yield 표현식에서 yield된 값 3이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 3, done: false}

// 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행한다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 제너레이터 함수의 반환값 undefined가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었음을 나타내는 true가 할당된다.
console.log(generator.next()); // {value: undefined, done: true}

```

👉 ___제너레이터 객체의 `next` 메서드 호출시 `yield` 표현식까지 실행되고 일시 중지(suspend)되며, 함수의 제어권이 호출자로 양도됨!___

이때 `next` 메서드의 반환 결과는 _이터레이터 리절트 객체_ 인데, 해당 객체의 _1) `value` 프로퍼티에는 `yield` 표현식에서 `yield` 된 값(`yield` 키워드 뒤의 값)_ 이 할당되고 _2) `done` 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지 나타내는 불리언 값 할당_

```js
function* genFunc() {
  // 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 1은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // x 변수에는 아직 아무것도 할당되지 않았다. x 변수의 값은 next 메서드가 두 번째 호출될 때 결정된다.
  const x = yield 1;

  // 두 번째 next 메서드를 호출할 때 전달한 인수 10은 첫 번째 yield 표현식을 할당받는 x 변수에 할당된다.
  // 즉, const x = yield 1;은 두 번째 next 메서드를 호출했을 때 완료된다.
  // 두 번째 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 x + 10은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  const y = yield (x + 10);

  // 세 번째 next 메서드를 호출할 때 전달한 인수 20은 두 번째 yield 표현식을 할당받는 y 변수에 할당된다.
  // 즉, const y = yield (x + 10);는 세 번째 next 메서드를 호출했을 때 완료된다.
  // 세 번째 next 메서드를 호출하면 함수 끝까지 실행된다.
  // 이때 제너레이터 함수의 반환값 x + y는 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // 일반적으로 제너레이터의 반환값은 의미가 없다.
  // 따라서 제너레이터에서는 값을 반환할 필요가 없고 return은 종료의 의미로만 사용해야 한다.
  return x + y;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이며 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc(0);

// 처음 호출하는 next 메서드에는 인수를 전달하지 않는다.
// 만약 처음 호출하는 next 메서드에 인수를 전달하면 무시된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 첫 번째 yield된 값 1이 할당된다.
let res = generator.next();
console.log(res); // {value: 1, done: false}

// next 메서드에 인수로 전달한 10은 genFunc 함수의 x 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 두 번째 yield된 값 20이 할당된다.
res = generator.next(10);
console.log(res); // {value: 20, done: false}

// next 메서드에 인수로 전달한 20은 genFunc 함수의 y 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값 30이 할당된다.
res = generator.next(20);
console.log(res); // {value: 30, done: true}
```
이처럼 제너레이터 함수는 `next`와 `yield` 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있음

## 5. 제너레이터의 활용

### 이터러블의 구현

제너레이터 함수 사용시 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블 구현 가능!

#### 무한 피보나치 수열 생성 함수

- 이터러블 프로토콜 이용
```js
const infiniteFibonacci = (function () {
  let [pre, cur] = [0, 1];
  
  return {
    [Symbol.iterator]() { return this; },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한 이터러블이므로 done 프로퍼티 생략
      return { value: cur };
    }
  }
}());

for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8 --
}
```


- 제너레이터 이용
```js
// 이터레이션 프로토콜 준수해 생성하는 방식보다 간단히 구현할 수 있음.
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
}());

for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // ...
}
```

### 비동기 처리 

`next` 메서드와 `yield` 표현식을 통해 함수 호출자와 함수 상태를 주고 받을 수 있다는 점에서 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현 가능

## 6. async/await

__프로미스의 후속 처리 없이 마치 동기 처럼 프로미스가 처리 결과를 반환할 수 있도록 하는 async/await가 ES8에서 등장했다.__

프로미스를 기반으로 동작하며, 프로미스의 후속 처리 메서드(try/catch/finally) 없이 마치 동기 처리 처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

```js
const fetch = require('node-fetch');

async function fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

### async 함수

- await 키워드는 반드시 async 함수 내부에서 사용
- async 함수가 명시적으로 프로미스를 반환하지 않더라도 async 함수는 암묵적으로 반환값을 resolve하는 프로미스 반환

```js
async function foo(n) { return n; }
foo(1).then(v =. console.log(v)); // 1

const bar = async function(n) { return n; }
bar(2).then(v =. console.log(v)); // 2

const baz = async n => n;
baz(3).then(v =. console.log(v)); // 3

const obj = {
  async foo(b) { return n; }
}
obj.foo(4).then(v => console.log(v)); // 4

class MyClass {
  async bar(n) { return n; }
}
const myClass = new MyClass();
myClass.bar(5).then(v => console.log(v)); // 5
```

- 클래스의 `constructor` 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환해야 하므로 `constructor` 메서드는 async 메서드가 될 수 없음!
```js
class MyClass {
    async constructor () { }
    //SyntaxError: Class constructor may not be an async method
}
```

### await 키워드 

- await 키워드는 프로미스가 settled 상태(_비동기 처리가 수행된 상태_)가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과 반환
- await 키워드는 반드시 프로미스 앞에서 사용함

```js
const fetch = require('node-fetch');

const getGithubUserName = async id => {
  const res = await fetch(`https://api.github.com/users/${id}`); // (1)
  const { name } = await res.json(); // (2)
  console.log(name); // Ungmo Lee
};

getGithubUserName('ungmo2');
```
👉 await 키워드는 프로미스가 settled 된 상태가 될까지 기다리므로 (1)의 `fetch` 함수가 수행한 HTTP 요청에 대한 서버의 응이 도착해서 `fetch` 함수가 반환한 프로미스가 settled 상태가 될 때까지 (1)은 대기!

👉 이후 _프로미스가 settled 상태가 된다면 프로미스가 resolve한 처리 결과가 res 변수에 할당 > 이후 (2) 코드 실행_


- 모든 프로미스에 await 키워드 사용하는 것을 주의해야 함! 서로 연관이 없이 개별적으로 수행되는 비동기 처리에서는 순차적으로 처리할 필요가 없음. 만약 비동기 처리의 순서가 보장되어야 할 때는 모든 프로미스에 await 키워드를 사용하여 순차적으로 처리함.
```js
//순서 보장 불필요
async function foo(){
    const res = await Promise.all([
        new Promise(resolve => setTimeout(() => resolve(1), 3000)),
        new Promise(resolve => setTimeout(() => resolve(2), 2000)),
        new Promise(resolve => setTimeout(() => resolve(3), 1000))
    ]);

    console.log(res); //[1, 2, 3]
}

foo(); //약 3초 소요된다.

//순서 보장 필요
async function bar(n) {
  const a = await new Promise(resolve => setTimeout(() => resolve(n), 3000));
  // 두 번째 비동기 처리를 수행하려면 첫 번째 비동기 처리 결과가 필요하다.
  const b = await new Promise(resolve => setTimeout(() => resolve(a + 1), 2000));
  // 세 번째 비동기 처리를 수행하려면 두 번째 비동기 처리 결과가 필요하다.
  const c = await new Promise(resolve => setTimeout(() => resolve(b + 1), 1000));

  console.log([a, b, c]); // [1, 2, 3]
}

bar(1); // 약 6초 소요된다.
```

### 에러 처리

- async/await에서 try/catch 문을 사용해 에러 처리 가능
- 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확!
```js
const fetch = require('node-fetch');

const foo = async () => {
  try {
    const wrongUrl = 'https://wrong.url';

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.error(err); // TypeError: Failed to fetch
  }
};

foo();
```
👉 `foo` 함수의 `catch`문은 HTTP 통신에서 발생한 네트워크 에러 뿐 아니라 `try` 블록 내의 모든 문에서 발생한 일반적인 에러까지 모두 캐치할 수 있음

- _async 함수 내에서 catch문을 사용해 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스 반환함._ 이때는 `Promise.prototype.catch` 후속 처리 메서드 사용해 에러 캐치 가능
```js
const fetch = require('node-fetch');

const foo = async () => {
  const wrongUrl = 'https://wrong.url';

  const response = await fetch(wrongUrl);
  const data = await response.json();
  return data;
};

foo()
  .then(console.log)
  .catch(console.error); // TypeError: Failed to fetch
```
