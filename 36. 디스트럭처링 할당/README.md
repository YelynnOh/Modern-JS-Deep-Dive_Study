# 36. 디스트럭처링 할당

디스트럭처링 할당(구조 분해 할당)은 궂화된 배열과 같은 이터러블 또는 객체를 destructuring(비구조화)하여 1개 이상의 변수에 개별적으로 할다앟는 것을 말한다. 배열과 같은 이터러블 또는 객체 리터럴에서 필요한 값만 추출해 변수에 할당할 때 유용하다.

## 1. 배열 디스트럭처링 할당

ES6에서 배열 디스트럭처링 할당은 배열의 각 요소를 배열로부터 추출해 1개 이상의 변수에 할당한다.
이때 _배열 디스트럭처링 할당의 대상(할당문의 우변)은 이터러블이어야 하며, 할당 기준은 배열의 인덱스다. 즉, 순서대로 할당된다._

```js
const arr = [1, 2, 3];

//ES6 배열 디스트럭처링 할당
//변수 one, two, tree를 선언하고 배열 arr을 디스트럭처링 해 할당
const [one, two, three] = arr;

console.log(one, two, three); //1 2 3
```

배열 디스트럭처링 할당을 위해서는 할당 연산자 왼쪽에 값을 할당받을 변수를 선언해야 한다. 이 때 변수를 배열 리터럴 형태로 선언한다.

```js
const [x, y] = [1, 2];
//만약 우변에 이터러블을 할당하지 않으면 에러가 발생한다.
```

배열 디스트럭처링 할당의 기준은 인덱스이므로 변수의 개수와 이터러블의 개수가 일치할 필요는 없다.

```js
const [a, b] = [1, 2];
console.log(a, b); // 1 2

const [c, d] = [1];
console.log(c, d); // 1 undefined

const [e, f] = [1, 2, 3];
console.log(e, f); // 1 2

const [g, , h] = [1, 2, 3];
console.log(g, h); // 1 3
```

배열 디스트럭처링 할당을 위한 변수에 Rest 파라미터와 유사하게 Rest 요소 `...`을 사용할 수 있다.

Rest 요소는 Rest 파라미터와 마찬가지로 반드시 마지막에 위치해야 한다.

```js
// Rest 요소
const [x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [ 2, 3 ]
```
## 2. 객체 디스트럭처링 할당

ES6의 객체 디스트럭처링 할당은 객체의 각 프로퍼티를 객체로부터 추출하여 1개 이상의 변수에 할당한다. 이때 객체 디스트럭처링 할당의 대상(할당문의 우변)은 객체이여야 하며, _할당 기준은 프로퍼티 키이다._
__즉, 순서는 상관없으며 선언된 변수 이름과 프로퍼티 키가 일치하면 할당된다.__

```js
const user = { firstName: 'Ungmo', lastName: 'Lee' };

// ES6 객체 디스트럭처링 할당
// 변수 lastName, firstName을 선언하고 user 객체를 디스트럭처링하여 할당한다.
// 이때 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다. 순서는 의미가 없다.
const { lastName, firstName } = user;

console.log(firstName, lastName); // Ungmo Lee
```

객체 또는 객체로 평가될 수 있는 표현식(문자열, 숫자, 배열 등)을 할당하지 않으면 에러가 발생한다.

```js
const { lastName, firstName };
// SyntaxError: Missing initializer in destructuring declaration

const { lastName, firstName } = null;
// TypeError: Cannot destructure property 'lastName' of 'null' as it is null.

// 에러는 안나지만 할당 값이 없다.
const { lastName, firstName } = [1,2];
console.log(lastName, firstName) // undefined undefined
```

객체 리터럴 형태로 선언한 변수는 프로퍼티 축약 표현을 통해 선언한 것이다.

```js
const { lastName, firstName } = user;
// 위와 아래는 동치다.
const { lastName: lastName, firstName: firstName } = user;
```

따라서 객체의 프로퍼티 키와 다른 변수 이름으로 프로퍼티 값을 할당받으려면 다음과 같이 사용해야 한다.

```js
const user = { firstName: 'Ungmo', lastName: 'Lee' };

// 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다.
// 프로퍼티 키가 lastName인 프로퍼티 값을 ln에 할당하고,
// 프로퍼티 키가 firstName인 프로퍼티 값을 fn에 할당한다.
const { lastName: ln, firstName: fn } = user;

console.log(fn, ln); // Ungmo Lee
```

객체 디스트럭처링 할당을 위한 변수에 Rest 파라미터와 유사하게 Rest요소 `...`을 사용할 수 있다. 이때, Rest 요소는 Rest 파라미터와 마찬가지로 반드시 마지막에 위치해야 한다.

```js
// Rest 프로퍼티
const { x, ...rest } = { x: 1, y: 2, z: 3 };
console.log(x, rest); // 1 { y: 2, z: 3 }
```
