# 27. 배열

## 1. 배열이란?

- 배열은 여러 개의 값을 순차적으로 나열한 자료구조이다.

```js
const arr = ['apple', 'banana', 'orange'];
```

- 배열이 가지고 있는 값을 _요소_ 라고 하며, 배열의 요소는 배열에서 자신의 위치를 나타내는 0 이상의 정수 _인덱스_ 를 갖는다. 
- 요소에 접근할 때는 대괄호 표기법을 사용한다.
- 배열은 요소의 개수, 즉 배열의 길이를 나타내는 __length 프로퍼티__ 를 갖는다.
- 배열은 객체 타입이다.
  ```js
  typeof arr //object
  ```
- 배열은 배열 리터럴, Array 생성자 함수, `Array.of`, `Array.from` 메서드로 생성할 수 있다.
  ```js
  const arr = [1, 2, 3];

  arr.constructor === Array // true
  Object.getPrototypeOf(arr) === Array.prototype //true
  ```

📌 _배열 vs 일반 객체_

| 구분 | 객체 | 배열 |
| --- | --- | --- |
| 구조 | 프로퍼티 키와 프로퍼티 값 | 인덱스와 요소 |
| 값의 참조 | 프로퍼티 키  | 인덱스 |
| 값의 순서 | X | O |
| length 프로퍼티 | X | O |

- 일반 객체와 배열을 구분하는 가장 명확한 차이는 "값의 순서"와 "length 프로퍼티"다. 인덱스로 표현되는 값의 순서와 length 프로퍼티를 갖는 배열은 반복문을 통해 순차적으로 값에 접근하기 적합한 자료구조다.

## 2. 자바스크립트 배열은 배열이 아니다. 

자료구조에서 배열은 동일한 크기의 메모리 공간이 빈틈없이 연속적으로 나열된 자료구조를 말한다. 즉, 배열의 요소는 하나의 데이터 타입으로 통일되어 있으며 서로 연속적으로 인접해 있다. 이를 __밀집 배열(dense array)__ 라고 한다.
이와 같은 구조는 인덱스를 통해 단 한 번의 연산으로 임의의 요소에 접근할 수 있다. (O(1))
하지만 정렬되지 않는 배열의 경우 모든 원소를 차례대로 검색해야 한다. (O(n))

> 검색 대상 요소의 메모리 주소 = 배열의 시작 메모리 주소 + 인덱스 * 요소의 바이트 수

또한 배열에 요소를 삽입하거나 삭제하는 경우 배열의 요소를 연속적으로 유지하기 위해 요소를 이동시켜야 하는 단점이 있다.

__하지만 JS에서의 배열은 지금까지 살펴본 일반적인 배열과 다르다. 즉, 배열의 요소를 위한 각각의 메모리 공간은 동일한 크기를 갖지 않아도 되며, 연속적으로 이어져 있지 않을 수도 있다. 배열의 요소가 연속적으로 이어져있지 않은, 희소 배열(sparse array)이다.__

_JS에서 배열은 일반적인 배열의 동작을 흉내 낸 특수한 객체이다._

```js
const arr = [
  'string',
  10,
  true,
  null,
  undefined,
  NaN,
  Infinity,
  [ ],
  { },
  function () {}
];
```

JS 배열은 인덱스를 나타내는 문자열을 프로퍼티 키로 가지며, length 프로퍼티를 갖는 특수한 객체다. JS 배열의 요소는 사실 프로퍼티 값이다. JS에서 사용할 수 있는 모든 값은 객체의 프로퍼티 값이 될 수 있으므로 어떤 타입의 값이라도 배열의 요소가 될 수 있다. 

📌 _일반 배열 vs JS 배열 장단점_

- 일반적인 배열은 인덱스 요소에 빠르게 접근할 수 있다. 하지만 특정 요소를 검색하거나 삽입 또는 삭제하는 경우 효율적이지 않다.
- 자바스크립트 배열은 해시 테이블로 구현된 객체이므로 인덱스로 요소에 접근하는 경우 일반적인 배열보다 성능적인 면에서 느릴 수 밖에 없는 구조적인 단점이 있다. 하지만 특정 요소를 검색하거나 삽입 또는 삭제하는 경우 일반적인 배열보다 빠른 성능을 기대할 수 있다.

## 3. length 프로퍼티와 희소 배열

length 프로퍼티 값은 배열에 요소를 추가하거나 삭제하면 자동 갱신된다.
```js
const arr = [1, 2, 3];
console.log(arr.length); //3

//요소 추가
arr.push(4); //length는 4

arr.pop()l //length는 3으로 자동 갱신
```

length 프로퍼티 값은 요소의 개수, 즉 배열의 길이를 바탕으로 결정되지만 임의의 숫자 값을 명시적으로 할당할 수도 있다.
`arr.length = 3` 이런식으로 할당을 할 수 있다.

하지만 주의할 것은 _length 프로퍼티 값보다 큰 값을 length에 할당하는 경우다. 이때 length 프로퍼티 값은 변경되지만 실제로 배열의 길이가 늘어나지는 않는다._
```js
const arr = [1];

// 현재 length 프로퍼티 값인 1보다 큰 숫자 값 3을 length 프로퍼티에 할당
arr.length = 3;

// length 프로퍼티 값은 변경되지만 실제로 배열의 길이가 늘어나지는 않는다.
console.log(arr.length); // 3
console.log(arr); // [1, empty × 2]
```

JS에서 배열은 희소 배열이라고 했다.
__희소 배열은 length와 배열 요소의 개수가 일치하지 않는다. 희소 배열의 length는 희소 배열의 실제 요소 개수보다 언제나 크다.__
희소 배열은 사용하지 않는 게 좋다. _배열에는 같은 타입의 요소를 연속적으로 위치시키는 것이 최선이다._

## 4. 배열 생성

### 배열 리터럴

- 배열 리터럴은 0개 이상의 요소를 쉽표로 구분하여 대괄호 `[]`로 묶는다. 배열 리터럴은 객체 리터럴과 달리 프로퍼티 키가 없고 값만 존재한다.
  ```js
  const arr = [1, 2, 3];
  console.log(arr.length); //3
  ```
- 배열 리터럴에 요소를 하나도 추가하지 않으면 배열의 길이 length 프로퍼티 값은 0이다.
- 배열 리터럴에 요소를 생략하면 희소 배열이 생성된다.


### Array 생성자 함수

Array 생성자 함수는 전달된 인수의 개수에 따라 다르게 동작하므로 주의가 필요하다.

- 전달된 인수 1개 + 숫자 => length 프로퍼티 값이 인수인 배열 생성
  ```js
  const arr = new Array(10);

  console.log(arr); //[empty * 10]
  console.log(arr.length); //10
  ```
  - 이때 전달된 배열은 희소 배열. length 프로퍼티 값이 0이 아니지만 실제 배열 요소는 존재하지 않기 때문이다.
- 전달된 인수가 없으면 빈 배열을 생성한다. 즉, 배열 리터럴 `[]` 과 같다.
- 전달된 인수가 2개 이상이거나 숫자가 아닌 겨웅 인수를 요소로 갖는 배열을 생성한다.
  ```js
  //전달된 인수가 2개 이상이면 인수를 요소로 갖는 배열을 생성한다.
  new Array(1, 2, 3); // [1, 2, 3]

  //전달된 인수가 1개지만 숫자가 아니면 인수를 요소로 갖는 배열을 생성한다.
  new Array({}); //[{}]
  ```
  - Array 생성자 함수는 `new` 연산자와 함께 호추랗지 않아도, 즉 일반 함수로 호출해도 배열을 생성하는 생성자 함수로 동작한다. 이는 Array 생성자 함수 내부에서 `new.target`을 확인하기 때문이다.
    ```js
    Array(1, 2, 3); // [1, 2, 3]
    ```

### Array.of

ES6에 도입된 `Array.of` 메서드는 전달된 인수를 요소로 갖는 배열을 생성한다.

이는 Array 생성자 함수와 달리 인수가 1개이고 숫자이더라도 인수를 요소로 갖는 배열을 생성한다.

```js
// 전달된 인수가 1개이고 숫자이더라도 인수를 요소로 갖는 배열을 생성한다.
Array.of(1); // -> [1]

Array.of(1, 2, 3); // -> [1, 2, 3]

Array.of('string'); // -> ['string']
```

### Array.from

ES6에 도입된 `Array.from` 메서드는 _유사 배열 객체_ 또는 _이터러블 객체_ 를 인수로 전달받아 배열로 변환하여 반환한다.
이를 사용하면 두 번째 인수로 전달한 콜백 함수를 통해 값을 만들면서 요소를 채울 수 있다. `Array.from` 메서드는 두 번 째 인수로 전달한 콜백 함수에 첫 번째 인수에 의해 생성된 배열의 요소값과 인덱스를 순차적으로 전달하면서 호출하고, 콜백 함수의 반환값으로 구성된 배열을 반환한다.

```js
// 유사 배열 객체를 변환하여 배열을 생성한다.
Array.from({ length: 2, 0: 'a', 1: 'b' }); // -> ['a', 'b']

// 이터러블을 변환하여 배열을 생성한다. 문자열은 이터러블이다.
Array.from('Hello'); // -> ['H', 'e', 'l', 'l', 'o']

// Array.from에 length만 존재하는 유사 배열 객체를 전달하면 undefined를 요소로 채운다.
Array.from({ length: 3 }); // -> [undefined, undefined, undefined]

// Array.from은 두 번째 인수로 전달한 콜백 함수의 반환값으로 구성된 배열을 반환한다.
Array.from({ length: 3 }, (_, i) => i); // -> [0, 1, 2]
```

## 5. 배열 요소의 참조

배열 요소 참조 시 대괄호 `[]` 표기법을 사용한다. 대괄호 안에는 인덱스가 와야 한다. 정수로 평가되는 표현식이라면 인덱스 대신 사용할 수 있다. 인덱스는 값을 참조할 수 있다는 의미에서 객체의 프로퍼티 키와 같은 역할을 한다.
```js
const arr = [1, 2];

// 인덱스가 0인 요소를 참조
console.log(arr[0]); // 1
// 인덱스가 1인 요소를 참조
console.log(arr[1]); // 2

console.log(arr[2]); //undefined(존재하지 않는 요소이기 때문이다.)
```

```js
// 희소 배열
const arr = [1, , 3];

// 배열 arr에는 인덱스가 1인 요소가 존재하지 않는다.
console.log(Object.getOwnPropertyDescriptors(arr));
/*
{
  '0': {value: 1, writable: true, enumerable: true, configurable: true},
  '2': {value: 3, writable: true, enumerable: true, configurable: true},
  length: {value: 3, writable: true, enumerable: false, configurable: false}
*/

// 존재하지 않는 요소를 참조하면 undefined가 반환된다.
console.log(arr[1]); // undefined
console.log(arr[3]); // undefined
```

## 6. 배열 요소의 추가와 갱신

객체에 프로퍼티를 동적으로 추가할 수 있는 것처럼 배열에도 요소를 동적으로 추가할 수 있다. 존재하지 않는 인덱스를 사용해 값을 할당하면 새로운 요소가 추가된다. length 프로퍼티 값도 같이 자동으로 갱신된다.

```js
const arr = [0];

//배열 요소의 추가
arr[1] = 1;

console.log(arr); // [0 , 1]
console.log(arr.length); //2

```
```js
//현재 배열의 length 프로퍼티 값보다 큰 인덱스로 새로운 요소를 추가하면 희소 배열이 된다. 
arr[100] = 100;

console.log(arr); // [0, 1, empty × 98, 100]
console.log(arr.length); // 101
```
이때 인덱스로 요소에 접근해 명시적으로 값을 할당하지 않은 요소는 생성되지 않는다. 
```js
// 명시적으로 값을 할당하지 않은 요소는 생성되지 않는다.
console.log(Object.getOwnPropertyDescriptors(arr));
/*
{
  '0': {value: 0, writable: true, enumerable: true, configurable: true},
  '1': {value: 1, writable: true, enumerable: true, configurable: true},
  '100': {value: 100, writable: true, enumerable: true, configurable: true},
  length: {value: 101, writable: true, enumerable: false, configurable: false}
*/
```
이미 값이 있는 인덱스에 값을 재할당하면 값이 재할당된다.

## 7. 배열 요소의 삭제

배열은 사실 객체이기 떄문에 배열의 특정 요소를 삭제하기 위해 `delete` 연산자를 사용할 수 있다.
```js
const arr = [1, 2, 3];

// 배열 요소의 삭제
delete arr[1];
console.log(arr); // [1, empty, 3]

// length 프로퍼티에 영향을 주지 않는다. 즉, 희소 배열이 된다.
console.log(arr.length); // 3
```

위의 경우는 배열이 희소 배열이 되며 length 프로퍼티 값은 변하지 않는다.
따라서 희소 배열을 만드는 `delete` 연산자 사용하지 않는 것이 좋다. 

희소 배열을 만들지 않으면서 배열의 특정 요소를 완전히 삭제하기 위해서는 `Array.prototype.splice` 메서드를 사용하는 게 좋다. 

```js
const arr = [1, 2, 3];

// Array.prototype.splice(삭제를 시작할 인덱스, 삭제할 요소 수)
// arr[1]부터 1개의 요소를 제거
arr.splice(1, 1);
console.log(arr); // [1, 3]

// length 프로퍼티가 자동 갱신된다.
console.log(arr.length); // 2
```

## 8. 배열 메서드

### Array.prototype.push

push 메서드는 인수로 전달받은 모든 값을 원본 배열의 마지막 요소로 추가하고 변경된 length 프로퍼티 값을 반환한다. push 메서드는 원본 배열을 직접 변경한다.

```js
const arr = [1, 2];

// 인수로 전달받은 모든 값을 원본 배열 arr의 마지막 요소로 추가하고 변경된 length 값을 반환한다.
let result = arr.push(3, 4);
console.log(result); // 4

// push 메서드는 원본 배열을 직접 변경한다.
console.log(arr); // [1, 2, 3, 4]
```

push 메서드는 성능 면에서 좋지 않다. 마지막 요소로 추가할 요소가 하나뿐이라면 length 프로퍼티를 사용하여 배열의 마지막에 요소를 직접 추가할 수도 있다. 이 방법이 push 메서드보다 빠르다.
```js
const arr = [1, 2];

// arr.push(3)과 동일한 처리를 한다. 이 방법이 push 메서드보다 빠르다.
arr[arr.length] = 3;
console.log(arr); // [1, 2, 3]
```
push 메소드는 원본 배열을 직접 변경하는 부수 효과가 있다. 따라서 push 메소드보다는 ES6의 스프레드 문법을 사용하는 편이 좋다.
```js
const arr = [1, 2];

// ES6 스프레드 문법
const newArr = [...arr, 3];
console.log(newArr); // [1, 2, 3]
```

### Array.prototype.pop

pop 메서드는 원본 배열에서 마지막 요소를 제거하고 제거한 요소를 반환한다. 원본 배열이 빈 배열이면 undefined를 반환한다. pop 메서드는 원본 배열을 직접 변경한다.

```js
const arr = [1, 2];

// 원본 배열에서 마지막 요소를 제거하고 제거한 요소를 반환한다.
let result = arr.pop();
console.log(result); // 2

// pop 메서드는 원본 배열을 직접 변경한다.
console.log(arr); // [1]
```

### Array.prototype.unshift

unshift 메서드는 인수로 전달받은 모든 값을 원본 배열의 선두에 요소로 추가하고 변경된 length 프로퍼티 값을 반환한다. unshift 메서드는 원본 배열을 직접 변경한다.

```js
const arr = [1, 2];

// 인수로 전달받은 모든 값을 원본 배열의 선두에 요소로 추가하고 변경된 length 값을 반환한다.
let result = arr.unshift(3, 4);
console.log(result); // 4

// unshift 메서드는 원본 배열을 직접 변경한다.
console.log(arr); // [3, 4, 1, 2]
```
마찬가지로 스프레드 문법을 쓰는 것이 좋다.

```js
const arr = [1, 2];

// ES6 스프레드 문법
const newArr = [3, ...arr];
console.log(newArr); // [3, 1, 2]
```

### Array.prototype.shift

원본 배열에서 첫 번째 요소를 제거하고 제거된 요소를 반환한다. 원본 배열을 직접 변경한다.

```js
const arr = [1, 2];

// 원본 배열에서 첫 번째 요소를 제거하고 제거한 요소를 반환한다.
let result = arr.shift();
console.log(result); // 1

// shift 메서드는 원본 배열을 직접 변경한다.
console.log(arr); // [2]
```

shift와 push를 통해 큐를 규현할 수 있다고 한다.
```js
const Queue = (function () {
  function Queue(array = []) {
    if (!Array.isArray(array)) {
      // "47. 에러 처리" 참고
      throw new TypeError(`${array} is not an array.`);
    }
    this.array = array;
  }

  Queue.prototype = {
    // "19.10.1. 생성자 함수에 의한 프로토타입의 교체" 참고
    constructor: Queue,
    // 큐의 가장 마지막에 데이터를 밀어 넣는다.
    enqueue(value) {
      return this.array.push(value);
    },
    // 큐의 가장 처음 데이터, 즉 가장 먼저 밀어 넣은 데이터를 꺼낸다.
    dequeue() {
      return this.array.shift();
    },
    // 큐의 복사본 배열을 반환한다.
    entries() {
      return [...this.array];
    }
  };

  return Queue;
}());

const queue = new Queue([1, 2]);
console.log(queue.entries()); // [1, 2]

queue.enqueue(3);
console.log(queue.entries()); // [1, 2, 3]

queue.dequeue();
console.log(queue.entries()); // [2, 3]
```

### Array.prototype.concat

인수로 전달된 값들(배열 또는 원시값)을 원본 배열의 마지막 요소로 추가한 새로운 배열로 반환한다. 인수로 전달한 값이 배열인 경우 배열을 해체하여 새로운 배열의 요소로 추가한다. 원본 배열은 변경되지 않는다.
```js
const arr1 = [1, 2];
const arr2 = [3, 4];

// 배열 arr2를 원본 배열 arr1의 마지막 요소로 추가한 새로운 배열을 반환한다.
// 인수로 전달한 값이 배열인 경우 배열을 해체하여 새로운 배열의 요소로 추가한다.
let result = arr1.concat(arr2);
console.log(result); // [1, 2, 3, 4]

// 숫자를 원본 배열 arr1의 마지막 요소로 추가한 새로운 배열을 반환한다.
result = arr1.concat(3);
console.log(result); // [1, 2, 3]

// 배열 arr2와 숫자를 원본 배열 arr1의 마지막 요소로 추가한 새로운 배열을 반환한다.
result = arr1.concat(arr2, 5);
console.log(result); // [1, 2, 3, 4, 5]

// 원본 배열은 변경되지 않는다.
console.log(arr1); // [1, 2]
```
새로운 배열을 반환하며 반환값을 반드시 변수에 할당해야 한다.

스프레드 문법으로 대체할 수 있다.
```js
let result = [1, 2].concat([3, 4]);
console.log(result); // [1, 2, 3, 4]

// concat 메서드는 ES6의 스프레드 문법으로 대체할 수 있다.
result = [...[1, 2], ...[3, 4]];
console.log(result); // [1, 2, 3, 4]
```

### Array.prototype.splice

원본 배열의 중간에 요소를 추가하거나 중간에 있는 요소를 제거하는 경우 사용한다.

splice 메서드는 3 개의 매개변수가 있으며 원본 배열을 직접 변경한다.

- start : 원본 배열의 요소를 제거하기 시작할 인덱스다. 원본 배열의 start부터 모든 요소를 제거한다. start가 음수인 경우 배열의 끝에서의 인덱스를 나타낸다. start가 -1이면 마지막 요소를 가리키고 -n이면 마지막에서 n번째 요소를 가리킨다.
- deleteCount : 원본 배열의 요소를 제거하기 시작할 인덱스인 start부터 제거할 요소의 개수다. 0인 경우 아무런 요소도 제거되지 않는다. 생략 가능하다.
- items : 제거한 위치에 삽입할 요소들의 목록이다. 생략 가능하다.

```js
const arr = [1, 2, 3, 4];

// 원본 배열의 인덱스 1부터 2개의 요소를 제거하고 그 자리에 새로운 요소 20, 30을 삽입한다.
const result = arr.splice(1, 2, 20, 30);

// 제거한 요소가 배열로 반환된다.
console.log(result); // [2, 3]
// splice 메서드는 원본 배열을 직접 변경한다.
console.log(arr); // [1, 20, 30, 4]
```

### Array.prototype.slice

slice 메서드는 인수로 전달된 범위의 요소들을 복사하여 배열로 반환한다. 원본 배열은 변경되지 않는다.

slice 메서드는 두 개의 매개변수를 갖는다.

- start : 복사를 시작할 인덱스다. 음스인 경우 배열의 끝에서의 인덱스를 나타낸다. slice(-2)는 배열의 마지막 두 개의 요소를 복사하여 배열로 반환한다.
- end : 복사를 종료할 인덱스다. 이 인덱스에 해당하는 요소는 복사되지 않는다. end는 생략 가능하며 생략 시 기본값은 length 프로퍼티 값이다.

```js
const arr = [1, 2, 3];

// arr[0]부터 arr[1] 이전(arr[1] 미포함)까지 복사하여 반환한다.
arr.slice(0, 1); // -> [1]

// arr[1]부터 arr[2] 이전(arr[2] 미포함)까지 복사하여 반환한다.
arr.slice(1, 2); // -> [2]

// 원본은 변경되지 않는다.
console.log(arr); // [1, 2, 3]
```
```js
const arr = [1, 2, 3];

// arr[1]부터 이후의 모든 요소를 복사하여 반환한다.
arr.slice(1); // -> [2, 3]
```

### Array.prototype.includes

ES7에서 도입된 includes 메서드는 배열 내에 특정 요소가 포함되어 있는지 확인하여 true, false를 반환한다. 첫 번째 인수로 검색할 대상을 지정하고 두 번째 인수로 검색을 시작할 인덱스를 지정할 수 있다.

```js
const arr = [1, 2, 3];

// 배열에 요소 2가 포함되어 있는지 확인한다.
arr.includes(2); // -> true

// 배열에 요소 100이 포함되어 있는지 확인한다.
arr.includes(100); // -> false
```
```js
const arr = [1, 2, 3];

// 배열에 요소 1이 포함되어 있는지 인덱스 1부터 확인한다.
arr.includes(1, 1); // -> false

// 배열에 요소 3이 포함되어 있는지 인덱스 2(arr.length - 1)부터 확인한다.
arr.includes(3, -1); // -> true
```

## 9. 배열 고차 함수

### Array.prototype.sort

sort 메서드는 배열의 요소를 정렬한다. 원본 배열을 직접 변경하며 정렬된 배열을 반환한다.

sort 메서드는 기본적으로 오름차순으로 요소를 정렬한다.
```js
const fruits = ['Banana', 'Orange', 'Apple'];

// 오름차순(ascending) 정렬
fruits.sort();

// sort 메서드는 원본 배열을 직접 변경한다.
console.log(fruits); // ['Apple', 'Banana', 'Orange']
```
문자열 요소로 이루어진 배열의 정렬은 문제가 없지만 숫자 요소로 이루어진 배열을 정렬할 때는 주의가 필요하다.
```js
const points = [40, 100, 1, 5, 2, 25, 10];

points.sort();

// 숫자 요소들로 이루어진 배열은 의도한 대로 정렬되지 않는다.
console.log(points); // [1, 10, 100, 2, 25, 40, 5]
```

__sort메서드에는 정렬 순서를 정의하는 비교 함수를 인수로 전달해야 한다.__
```js
const points = [40, 100, 1, 5, 2, 25, 10];

// 숫자 배열의 오름차순 정렬. 비교 함수의 반환값이 0보다 작으면 a를 우선하여 정렬한다.
points.sort((a, b) => a - b);
console.log(points); // [1, 2, 5, 10, 25, 40, 100]

// 숫자 배열의 내림차순 정렬. 비교 함수의 반환값이 0보다 작으면 b를 우선하여 정렬한다.
points.sort((a, b) => b - a);
console.log(points); // [100, 40, 25, 10, 5, 2, 1]
```

객체를 요소로 갖는 배열을 정렬하는 예제는 다음과 같다.
```js
const todos = [
  { id: 4, content: 'JavaScript' },
  { id: 1, content: 'HTML' },
  { id: 2, content: 'CSS' }
];

// 비교 함수. 매개변수 key는 프로퍼티 키다.
function compare(key) {
  // 프로퍼티 값이 문자열인 경우 - 산술 연산으로 비교하면 NaN이 나오므로 비교 연산을 사용한다.
  // 비교 함수는 양수/음수/0을 반환하면 되므로 - 산술 연산 대신 비교 연산을 사용할 수 있다.
  return (a, b) => (a[key] > b[key] ? 1 : (a[key] < b[key] ? -1 : 0));
}

// id를 기준으로 오름차순 정렬
todos.sort(compare('id'));
console.log(todos);
/*
[
  { id: 1, content: 'HTML' },
  { id: 2, content: 'CSS' },
  { id: 4, content: 'JavaScript' }
]
*/

// content를 기준으로 오름차순 정렬
todos.sort(compare('content'));
console.log(todos);
/*
[
  { id: 2, content: 'CSS' },
  { id: 1, content: 'HTML' },
  { id: 4, content: 'JavaScript' }
]
*/
```


### Array.prototype.forEach

forEach 메서드는 for 문을 대체할 수 있는 고차 함수다. forEach 메서드는 반복문을 통해 자신을 호출한 배열을 순회하면서 수행해야 할 처리를 콜백 함수로 전달받아 반복 호출한다.

```js
const numbers = [1, 2, 3];
let pows = [];

// forEach 메서드는 numbers 배열의 모든 요소를 순회하면서 콜백 함수를 반복 호출한다.
numbers.forEach(item => pows.push(item ** 2));
console.log(pows); // [1, 4, 9]
```

forEach 메서드의 콜백 함수는 forEach 메서드를 호출한 _배열의 요소값, 인덱스, forEach 메서드를 호출한 배열 자체, 즉 this_ 를 순차적으로 전달받을 수 있다. 다시 말해 3개의 인수를 전달 받아 처리 할 수 있다.

더불어 forEach 메서드는 for문과 달리 break, continue 문을 사용할 수 없다. 또한, 희소 배열의 경우 존재하지 않는 요소는 순회 대상에서 제외된다. 이는 map, filter, reduce 메서드 등에서도 마찬가지다.


### Array.prototype.map

map 메서드는 자신을 호출한 배열의 모든 요소를 순회하면서 인수로 전달받은 콜백 함수를 반복 호출한다. 그리고 __콜백 함수의 반환값들로 구성된 새로운 배열을 반환__ 한다. 원본 배열은 변경되지 않는다.

```js
const numbers = [1, 4, 9];

// map 메서드는 numbers 배열의 모든 요소를 순회하면서 콜백 함수를 반복 호출한다.
// 그리고 콜백 함수의 반환값들로 구성된 새로운 배열을 반환한다.
const roots = numbers.map(item => Math.sqrt(item));

// 위 코드는 다음과 같다.
// const roots = numbers.map(Math.sqrt);

// map 메서드는 새로운 배열을 반환한다
console.log(roots);   // [ 1, 2, 3 ]
// map 메서드는 원본 배열을 변경하지 않는다
console.log(numbers); // [ 1, 4, 9 ]
```

forEach 메서드와 map 메서드의 공통점은 자신을 호출한 배열의 모든 요소를 순회하면서 인수로 전달받은 콜백 함수를 반복 호출한다는 것이다. 하지만 forEach 메서드는 언제나 undefined를 반환하고, map 메서드는 콜백 함수의 반환값들로 구성된 새로운 배열을 반환하는 차이가 있다. 즉, forEach 메서드는 단순히 반복문을 대체하기 위한 고차 함수이고, map 메서드는 요소값을 다른 값으로 매핑한 새로운 배열을 생성하기 위한 고차 함수다.

map도 forEach와 마찬가지로 베열의 요소값, 인덱스, 호출한 배열 자체, 즉 this를 순차적으로 전달받을 수 있다.

map 메서드가 생성하여 반환하는 새로운 배열의 length 프로퍼티 값은 map 메서드를 호출한 배열의 length 프로퍼티 값과 반드시 일치한다. __즉, map 메서드를 호출한 배열과 map 메서드가 생성하여 반환한 배열은 1:1 매핑된다.__

### Array.prototype.filter

filter 메서드는 자신을 호출한 배열의 모든 요소를 순회하면서 인수로 전달받은 콜백 함수를 반복 호출한다. 그리고 콜백 함수의 반환값이 true 요소로만 구성된 새로운 배열을 반환한다. 이때 원본 배열은 변경되지않는다. filter 또한 forEach, map과 마찬가지로 3개의 매개변수를 갖는다.
```js
const numbers = [1, 2, 3, 4, 5];

// filter 메서드는 numbers 배열의 모든 요소를 순회하면서 콜백 함수를 반복 호출한다.
// 그리고 콜백 함수의 반환값이 true인 요소로만 구성된 새로운 배열을 반환한다.
// 다음의 경우 numbers 배열에서 홀수인 요소만을 필터링한다(1은 true로 평가된다).
const odds = numbers.filter(item => item % 2);
console.log(odds); // [1, 3, 5]
```
filter 메서드가 생성하여 반환한 새로운 배열의 length 프로퍼티 값은 filter 메서드를 호출한 배열의 length 프로퍼티 값과 같거나 작다.

> 따라서 find 메서드의 경우 filter 메소드와 유사하지만 반환값이 true인 첫 번째 요소만 반환한다. 따라서 배열이 아니라 요소값이다. 단 하나의 조건에 해당하는 값을 판단할 때 사용하면 된다. 참고!
```js
const users = [
  { id: 1, name: 'Lee' },
  { id: 2, name: 'Kim' },
  { id: 2, name: 'Choi' },
  { id: 3, name: 'Park' }
];

// id가 2인 첫 번째 요소를 반환한다. find 메서드는 배열이 아니라 요소를 반환한다.
users.find(user => user.id === 2); // -> {id: 2, name: 'Kim'}
```

### Array.prototype.reduce

reduce 메서드는 자신을 호출한 배열을모든 요소를 순회하며 인수로 전달받은 콜백 함수를 반복 호출한다.
그리고 콜백 함수의 반환값을 다음 순회 시에 콜백 함수의 첫 번째 인수로 전달하면서 콜백 함수를 호출하여 하나의 결과값을 만들어 반환한다. 이때 원본 배열은 변경되지 않는다.

reduce 메서드는 두 개의 인자를 가진다. 첫 번째 인수로 콜백 함수, 두 번째 인수로는 초기값을 전달 받는다.
```js
const numbers = [1, 2, 3, 4, 5];

// filter 메서드는 numbers 배열의 모든 요소를 순회하면서 콜백 함수를 반복 호출한다.
// 그리고 콜백 함수의 반환값이 true인 요소로만 구성된 새로운 배열을 반환한다.
// 다음의 경우 numbers 배열에서 홀수인 요소만을 필터링한다(1은 true로 평가된다).
const odds = numbers.filter(item => item % 2);
console.log(odds); // [1, 3, 5]
```

__reduce 메서드는 하나의 결과값을 반환한다.__

reduce 메서드는 평균 구하기, 최대값 구하기, 요소의 중복 횟수 구하기, 중첩 배열 평탄화, 중복 요소 제거 등에 다양하게 활용될 수 있다.

---

## 🎯 퀴즈

<details>
<summary>1. `.forEach` 루프와 `.map` 루프 사이의 주요 차이점을 설명하라. 어떤 경우에 각각의 메서드를 활용할지 얘기하라.</summary>
<div>

`.forEach`와 `.map`의 가장 큰 차이점은 `.map`은 새로운 배열을 반환한다는 것입니다. 
결과가 필요하지만 원본 배열을 변경하고 싶지 않을 경우 `.map`을 활용하는 것이 좋고, 단순히 배열을 반복할 필요가 있다면, `forEach`가 좋은 선택입니다.
</div>
</details>

