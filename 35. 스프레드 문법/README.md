# 35. 스프레드 문법

ES6에서 도입된 스프레드 문법(전개 문법) `...` 은 하나로 뭉쳐 있는 여러 값들의 집합을 펼처서 개별적인 값들의 목록으로 만든다.

스프레드 문법을 사용할 수 있는 대상은 _Array, String, Map, Set, DOM 컬랙션(NodeList, HTMLCollection), arguments_ 와 같이 `for...of`문으로 순회할 수 있는 이터러블에 한정된다. 

```js
// ...[1, 2, 3]은 [1, 2, 3]을 개별 요소로 분리한다(→ 1, 2, 3)
console.log(...[1, 2, 3]); // 1 2 3

// 문자열은 이터러블이다.
console.log(...'Hello'); // H e l l o

// Map과 Set은 이터러블이다.
console.log(...new Map([['a', '1'], ['b', '2']])); // [ 'a', '1' ] [ 'b', '2' ]
console.log(...new Set([1, 2, 3])); // 1 2 3

// 이터러블이 아닌 일반 객체는 스프레드 문법의 대상이 될 수 없다.
console.log(...{ a: 1, b: 2 });
// TypeError: Found non-callable @@iterator

```

이때 주의할 점은 _스프레드 문법의 결과가 값이 아니라는 것이다._ 그냥 값들의 목록이다.
__즉, 스프레드 문법 `...` 이 피연산자를 연산하여 값을 생성하는 연산자가 아니기 때문에 스프레드 문법의 결과는 변수 할당이 불가능하다.__

- 스프레드 문법의 결과물을 사용하는 방법
  - 함수 호출문의 인수 목록
  - 배열 리터럴의 요소 목록
  - 객체 리터럴의 프로퍼티 목록
 
## 1. 함수 호출문의 인수 목록에서 사용하는 경우

요소들의 집합인 배열을 펼쳐서 개별적인 값들의 목록으로 만든 후, 이를 함수의 인수 목록으로 전달해야 하는 경우가 있다.

```js
const arr = [1, 2, 3];

//배열 arr의 요소 중에서 최대값을 구하기 위해 Math.max를 사용한다.
const max = Math.max(arr); //NaN

```

`Math.max` 의 경우 매개변수 개수를 확정할 수 없는 가변 인자 함수이며, 여러 개의 숫자를 인수로 전달받아 인수 중에서 최대값을 반환한다.

하지만 이때 숫자가 아닌 배열을 인수로 전달하면 최대값을 구할 수 없으므로 `NaN`을 반환한다.

이 문제를 해결하기 위해 배열을 펼쳐 요소들을 개별적인 값들의 목록으로 만든 후 `Math.max` 메서드의 인수로 전달하는 과정이 필요하다.

```js
const arr = [1, 2, 3];

//스프레드 문법을 사용해 배열 arr을 1, 2, 3으로 펼쳐서 Math.max에 전달하낟.
//Math.max(...[1, 2, 3])은 Math.max(1, 2, 3)과 같다.
const max = Math.max(...arr); //3
```

📌 __Rest 문법 <-> 스프레드 문법__

- Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받기 위해 붙인다.
- 스프레드 문법은 여러 개의 값이 하나로 뭉쳐 있는 배열과 같은 이터러블을 펼쳐 개별적인 값들의 목록을 만드는 것이다.

## 2. 배열 리터럴 내부에서 사용하는 경우

스프레드 문법을 배열 리터럴에서 사용하면 ES5에서 사용하던 기존의 방식보다 더욱 간결하고 가독성 좋게 표현할 수 있다.

### concat

```js
//ES5
var arr = [1, 2].concat([3, 4]);
console.log(arr); //[1, 2, 3, 4]

//스프레드 문법 사용 시 별도 메서드 없이 배열 리터럴만으로 2개의 배열을 1개의 배열로 합칠 수 있음
//ES6
const arr2 = [...[1, 2], ...[3, 4]];
console.log(arr2); //[1, 2, 3, 4]
```

### splice

어떤 배열의 중간에 다른 배열의 요소들을 추가하거나 제거할 때 모습이다.

```js
//ES5
var arr1 = [1, 4];
var arr2 = [2, 3];

//세 번째 인수 arr2를 해체하여 전달
//그렇지 않으면 arr1에 arr2 배열 자체가 추가된다.
arr1.splice(1, 0, arr2);
console.log(arr1); //[1, [2, 3], 4]

//ES5
var arr1 = [1, 4];
var arr2 = [2, 3];

Array.prototype.slice.apply(arr1, [1, 0].concat(arr2));
console.log(arr1); //[1, 2, 3, 4]

//ES6
const arr1 = [1, 4];
const arr2 = [2, 3];

arr1.splice(1, 0, ...arr2);
console.log(arr1); //[1, 2, 3, 4]
```

### 배열 복사

```js
// ES6
const origin = [1, 2];
const copy = [...origin];

console.log(copy); // [1, 2]
console.log(copy === origin); // false

// ES5 에서는 slice 메서드 활용
var origin = [1, 2];
var copy = origin.slice();

console.log(copy); // [1, 2]
console.log(copy === origin); // false
```

### 이터러블을 배열로 변환

```js
function sum() { //이터러블이면서 유사 배열 객체인 arguments를 배열로 변환
  return [...arguments].reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2, 3)); //6
```

Rest 파라미터를 사용한다면?

```js
//Rest 파라미터 args는 함수에 전달된 인수들의 목록을 배열로 전달받는다.
const sum = (args) => args.reduce((pre, cur) => pre + cur, 0);

console.log(sum(1, 2, 3)); //6
```
하지만 이터러블이 아닌 유사 배열 객체는 스프레드 문법의 대상이 될 수 없다. 이떄는 ES6의 `Array.from` 메서드를 이용한다.

## 3. 객체 리터럴 내부에서 사용하는 경우

스프레드 문법의 대상은 이터러블이어야 하지만 스프레드 프로퍼티 제안은 일반 객체를 대상으로도 스프레드 문법의 사용을 허용한다.

```js
// 객체 복사(얕은 복사)
const obj = { x: 1, y: 2 };
const copy = { ...obj };
console.log(copy); // { x: 1, y: 2 }
console.log(obj === copy); // false

// 객체 병합
const merged = { x: 1, y: 2, ...{ a: 3, b: 4 } };
console.log(merged); // { x: 1, y: 2, a: 3, b: 4 }
```

스프레드 프로퍼티가 제안되기 이전에는 ES6에서 도입된 Object.assign 메서드를 사용하여 여러 개의 객체를 병합하거나 특정 프로퍼티를 변경 또는 추가했다.

```js
// 객체 병합. 프로퍼티가 중복되는 경우, 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = Object.assign({}, { x: 1, y: 2 }, { y: 10, z: 3 });
console.log(merged); // { x: 1, y: 10, z: 3 }

// 특정 프로퍼티 변경
const changed = Object.assign({}, { x: 1, y: 2 }, { y: 100 });
console.log(changed); // { x: 1, y: 100 }

// 프로퍼티 추가
const added = Object.assign({}, { x: 1, y: 2 }, { z: 0 });
console.log(added); // { x: 1, y: 2, z: 0 }
```
스프레드 프로퍼티는 `Object.assign` 메서드를 대체할 수 있는 간편한 문법이다.

```js
// 객체 병합. 프로퍼티가 중복되는 경우, 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
console.log(merged); // { x: 1, y: 10, z: 3 }

// 특정 프로퍼티 변경
const changed = { ...{ x: 1, y: 2 }, y: 100 };
// changed = { ...{ x: 1, y: 2 }, ...{ y: 100 } }
console.log(changed); // { x: 1, y: 100 }

// 프로퍼티 추가
const added = { ...{ x: 1, y: 2 }, z: 0 };
// added = { ...{ x: 1, y: 2 }, ...{ z: 0 } }
console.log(added); // { x: 1, y: 2, z: 0 }
```

