# 37. Set과 Map

## 1. Set
Set 객체는 중복되지 않는 유일한 값들의 집합이다. Set 객체는 배열과 유사하지만 차이가 있다.

| 구분  | 배열 | Set 객체 |
| --- | --- | --- |
| 동일한 값을 중복하여 포함할 수 있다 | O | X |
| 요소 순서에 의미가 있다 | O | X |
| 인덱스로 요소에 접근할 수 있다 | O | X |

### Set 객체의 생성

Set 객체는 Set 생성자 함수로 생성한다. 인수를 전달하지 않으면 빈 Set 객체가 생성된다.

_Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다. 이때 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다._

```js
const set1 = new Set([1, 2, 3, 3]);
console.log(set1); // Set(3) {1, 2, 3}

const set2 = new Set('hello');
console.log(set2); // Set(4) {"h", "e", "l", "o"}
중복을 허용하지 않는 Set 객체의 특성을 활용하여 배열에서 중복된 요소를 제거할 수 있다.

// Set을 사용한 배열의 중복 요소 제거
const uniq = array => [...new Set(array)];
console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
```

Set 객체의 요소 개수를 확일할 때는 `Set.prototype.size` 프로퍼티를 사용한다.

size 프로퍼티는 setter 함수 없이 getter 함수만 존재하는 접근자 프로퍼티다. 따라서 Set 객체의 요소 개수를 변경할 수 없다!

```js
const { size } = new Set([1, 2, 3, 3]);
console.log(size); // 3

set.size = 10; // 무시
console.log(set.size); // 3
```

### 요소 추가

Set 객체에 요소를 추가할 때는 `Set.prototype.add` 메서드를 사용한다.

add 메서드는 새로운 요소가 추가된 Set 객체를 반환한다.

```js
const set = new Set();
console.log(set); // Set(0) {}

set.add(1);
console.log(set); // Set(1) {1}
```

Set 객체는 객체나 배열과 같이 자바스크립트의 모든 값을 요소로 저장할 수 있다.

```js
const set = new Set();

set
  .add(1)
  .add('a')
  .add(true)
  .add(undefined)
  .add(null)
  .add({})
  .add([]);

console.log(set); // Set(7) {1, "a", true, undefined, null, {}, []}
```

Set 객체에 특정 요소가 존재하는지 확인하려면 `Set.prototype.has` 메서드를 사용한다. 이는 특정 요소의 존재 여부를 나타내는 불리언 값을 반환해준다.

```js
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

### 요소 삭제
Set 객체의 특정 요소를 삭제하려면 `Set.prototype.delete` 메서드를 사용한다. 

여기서 `delete` 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환한다.

`delete` 메서드에는 인덱스가 아니라 _요소값을 인수로 전달_ 해야 한다. _Set 객체는 순서에 의미가 없다. 즉, 인덱스를 갖지 않는다._

```js

const set = new Set([1, 2, 3]);

// 요소 2를 삭제한다.
set.delete(2);
console.log(set); // Set(2) {1, 3}

// 요소 1을 삭제한다.
set.delete(1);
console.log(set); // Set(1) {3}
const set = new Set([1, 2, 3]);

// 존재하지 않는 요소 0을 삭제하면 에러없이 무시된다.
set.delete(0);
console.log(set); // Set(3) {1, 2, 3}
```
delete 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환하기 때문에 연속적으로 호출할 수 없다.

```js
const set = new Set([1, 2, 3]);

// delete는 불리언 값을 반환한다.
set.delete(1).delete(2); // TypeError: set.delete(...).delete is not a function
```

Set 객체의 모든 요소를 일괄 삭제하려면 `clear` 메서드를 사용한다. `clear` 메서드는 언제나 undefined를 리턴한다.

```js
const set = new Set([1, 2, 3]);

set.clear();
console.log(set); // Set(0) {}
```

### 요소 순회
Set 객체의 요소를 순회하려면 `Set.prototype.forEach` 메서드를 사용한다. 이는 `Array.prototype.forEach` 메서드와 유사하지만 두 번째 인수가 다르다. 

📌 `Set.prototype.forEach` 메서드의 콜백 함수 인자

- 첫 번째 인수 : 현재 순회 중인 요소값
- 두 번째 인수 : 현재 순회 중인 요소값
- 세 번째 인수 : 현재 순회 중인 Set 객체 자체

첫 번째 인수와 두 번째 인수는 같은 값이다. 이처럼 동작하는 이유는 `Array.prototype.forEach` 메서드와 인터페이스를 통일하기 위함이며 다른 의미는 없다. 
배열에서 forEach의 두 번째 인자는 인덱스를 전달 받지만, Set 객체는 순서에 의미가 없어 인덱스를 갖지 않는다.

```js
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));
/*
1 1 Set(3) {1, 2, 3}
2 2 Set(3) {1, 2, 3}
3 3 Set(3) {1, 2, 3}
*/
```

Set 객체는 이터러블이다. 따라서 `for...of` 문으로 순회할 수 있으며, 스프레드 문법과 배열 디스트럭처링의 대상이 될 수도 있다.

```js
const set = new Set([1, 2, 3]);

// Set 객체는 Set.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in set); // true

// 이터러블인 Set 객체는 for...of 문으로 순회할 수 있다.
for (const value of set) {
  console.log(value); // 1 2 3
}

// 이터러블인 Set 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...set]); // [1, 2, 3]

// 이터러블인 Set 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, ...rest] = [...set];
console.log(a, rest); // 1, [2, 3]

```

### 집합 연산
Set 객체는 수학적 집합을 구현하기 위한 자료구조다. Set 객체를 통해 교집합, 합집합, 차집합 등을 구현할 수 있다.

#### 교집합

```js
Set.prototype.intersection = function (set) {
  const result = new Set();

  for (const value of set) {
    // 2개의 set의 요소가 공통되는 요소이면 교집합의 대상이다.
    if (this.has(value)) result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 교집합
console.log(setA.intersection(setB)); // Set(2) {2, 4}
// setB와 setA의 교집합
console.log(setB.intersection(setA)); // Set(2) {2, 4}
```

#### 합집합

```js
Set.prototype.union = function (set) {
  // this(Set 객체)를 복사
  const result = new Set(this);

  for (const value of set) {
    // 합집합은 2개의 Set 객체의 모든 요소로 구성된 집합이다. 중복된 요소는 포함되지 않는다.
    result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 합집합
console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}
// setB와 setA의 합집합
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```

#### 차집합

```js
Set.prototype.difference = function (set) {
  // this(Set 객체)를 복사
  const result = new Set(this);

  for (const value of set) {
    // 차집합은 어느 한쪽 집합에는 존재하지만 다른 한쪽 집합에는 존재하지 않는 요소로 구성된 집합이다.
    result.delete(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA에 대한 setB의 차집합
console.log(setA.difference(setB)); // Set(2) {1, 3}
// setB에 대한 setA의 차집합
console.log(setB.difference(setA)); // Set(0) {}
``` 

## 2. Map
Map 객체는 키와 값의 쌍으로 이루어진 컬렉션이다. Map 객체는 객체와 유사하지만 차이가 있다.

| 구분  | 객체 | Map 객체 |
| --- | --- | --- |
| 키로 사용할 수 있는 값 | 문자열 또는 심벌 값 | 객체를 포함한 모든 값 |
| 이터러블 | X | O |
| 요소 개수 확인 | Object.keys(obj).length | map.size |
 

### Map 객체의 생성

Map 객체는 Map 생성자 함수로 생성한다. 인수를 전달하지 않으면 빈 Map 객체가 생성된다.

Map 생성자 함수는 이터러블을 인수로 전달받아 Map 객체를 생성한다. 이때 인수로 전달되는 이터러블은 키로와 값의 쌍으로 이루어진 요소로 구성된다.

```js
const map1 = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(map1); // Map(2) {"key1" => "value1", "key2" => "value2"}

// 단순 배열이다. 키와 값의 쌍으로 만들어줘야함 // new Map([[1,2]])
const map2 = new Map([1, 2]); // TypeError: Iterator value 1 is not an entry object
```

Map 생성자 함수의 인수로 전달한 이터러블에 중복된 키를 갖는 요소가 존재하면 값이 덮어씌어진다.

따라서 _Map 객체에는 중복된 키를 갖는 요소가 존재할 수 없다._ 에러는 발생하지 않는다.

```js
const map = new Map([['key1', 'value1'], ['key1', 'value2']]);
console.log(map); // Map(1) {"key1" => "value2"}
```

Map 객체의 요소 개수를 확인할 때는 `Map.prototype.size` 프로퍼티를 사용한다.

size 프로퍼티는 setter 함수 없이 getter 함수만 존재하는 접근자 프로퍼티다. 따라서 Map 객체의 요소 개수를 변경할 수 없다.

```js
const { size } = new Map([['key1', 'value1'], ['key2', 'value2']]);
console.log(size); // 2

map.size = 10; // 무시
console.log(map.size); // 2
```

### 요소 추가
Map 객체에 요소를 추가할 때는 `Map.prototype.set` 메서드를 사용한다.

```js
const map = new Map();
console.log(map); // Map(0) {}

map.set('key1', 'value1');
console.log(map); // Map(1) {"key1" => "value1"}
set 메서드는 새로운 요소가 추가된 Map 객체를 반환한다. 따라서 연속적으로 호출 할 수 있다.

const map = new Map();

map
  .set('key1', 'value1')
  .set('key2', 'value2');

console.log(map); // Map(2) {"key1" => "value1", "key2" => "value2"}
 
```

객체는 문자열 또는 심벌 값만 키로 사용할 수 있다. 하지만 Map 객체는 키 타입에 제한이 없다.

따라서 객체를 포함한 모든 값을 키로 사용할 수 있다. 이는 Map 객체와 일반 객체의 가장 두드러지는 차이점이다.

```js
const map = new Map();

const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

// 객체도 키로 사용할 수 있다.
map
  .set(lee, 'developer')
  .set(kim, 'designer');

console.log(map);
// Map(2) { {name: "Lee"} => "developer", {name: "Kim"} => "designer" }
 
```

### 요소 취득
Map 객체에서 특정 요소를 취득하려면 `Map.prototype.get` 메서드를 사용한다. get 메서드의 인수로 키를 전달하면 Map 객체에서 인수로 전달한 키를 갖는 값을 반환한다. 존재하지 않으면 undefined를 반환한다.

```js
const map = new Map();

const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

map
  .set(lee, 'developer')
  .set(kim, 'designer');

console.log(map.get(lee)); // developer
console.log(map.get('key')); // undefined
```

### 요소 존재 여부 확인
Map 객체에 특정 요소가 존재하는지 확인하려면 `Map.prototype.has` 메서드를 사용한다. 
`has` 메서드는 특정 요소의 존재 여부를 나타내는 불리언 값을 반환한다.

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

console.log(map.has(lee)); // true
console.log(map.has('key')); // false
```

### 요소 삭제
Map 객체의 요소를 삭제하려면 `Map.prototype.delete` 메서드를 사용한다.

존재하지 않는 요소를 삭제하면 에러 없이 무시된다.

`delete` 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환한다. 따라서 연속적으로 호출 할 수 없다.

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

map.delete(kim);
console.log(map); // Map(1) { {name: "Lee"} => "developer" }

// 존재하지 않는 키 'key2'로 요소를 삭제하려 하면 에러없이 무시된다.
map.delete('key2');
console.log(map); // Map(1) {"key1" => "value1"}
 
```
 

Map객체의 모든 요소를 일괄 삭제하려면 `clear` 메서드를 사용한다. `clear` 메서드는 언제나 undefined를 반환한다.

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

map.clear();
console.log(map); // Map(0) {}
```

### 요소 순회
Map 객체의 요소를 순회하려면 `Map.prototype.forEach` 메서드를 사용한다. 이는 `Array.prototype.forEach` 메서드와 유사하지만 두 번째 인수는 다르다. 

📌 `Set.prototype.forEach` 메서드의 콜백 함수 인자

- 첫 번째 인수 : 현재 순회 중인 요소값
- 두 번째 인수 : 현재 순회 중인 요소키
- 세 번째 인수 : 현재 순회 중인 Map 객체 자체

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

map.forEach((v, k, map) => console.log(v, k, map));
/*
developer  {name: "Lee"}  Map(2){{name: "Lee"} => "developer", {name: "Kim"} => "designer"}
designer  {name: "Kim"}  Map(2){{name: "Lee"} => "developer", {name: "Kim"} => "designer"}
*/
```

__Map 객체는 이터러블이다.__ 따라서 `for...of` 문으로 순회할 수 있으며, 스프레드 문법과 배열 디스트럭처링 할당의 대상이 될 수도 있다.

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

// Map 객체는 Map.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in map); // true

// 이터러블인 Map 객체는 for...of 문으로 순회할 수 있다.
for (const entry of map) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}

// 이터러블인 Map 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...map]);
// [[{name: "Lee"}, "developer"], [{name: "Kim"}, "designer"]]

// 이터러블인 Map 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, b] = map;
console.log(a, b); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
```

Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드를 제공한다.

👀 Map 메서드 
- `Map.prototype.keys` :	Map 객체에서 요소키를 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
- `Map.prototype.values` :	Map 객체에서 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
- `Map.prototype.entries` :	Map 객체에서 요소키와 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.

```js
const lee = { name: 'Lee' };
const kim = { name: 'Kim' };

const map = new Map([[lee, 'developer'], [kim, 'designer']]);

// Map.prototype.keys는 Map 객체에서 요소키를 값으로 갖는 이터레이터를 반환한다.
for (const key of map.keys()) {
  console.log(key); // {name: "Lee"} {name: "Kim"}
}

// Map.prototype.values는 Map 객체에서 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const value of map.values()) {
  console.log(value); // developer designer
}

// Map.prototype.entries는 Map 객체에서 요소키와 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const entry of map.entries()) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}
```
Map 객체는 요소의 순서에 의미를 갖지 않지만 Map 객체를 순회하는 순서는 요소가 추가된 순서를 따른다. 이는 ECMAScript 사양에 규정되어 있지는 않지만 다른 이터러블의 순회와 호환성을 유지하기 위함이다.
