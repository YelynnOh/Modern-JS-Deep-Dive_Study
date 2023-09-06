# 16. 프로퍼티 어트리뷰트

## 1. 내부 슬롯과 내부 메서드

프로퍼티 어트리뷰트를 이해하기 위해서는 __내부 슬롯(internal slot)__ 과 __내부 메서드(internal method)__ 의 개념을 알아야한다.

내부 슬롯과 내부 메서드는 JS 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티(pseudo property)와 의사 메서드(pseudo method)다. 

원칙적으로는 JS 엔진의 내부 로직이므로 원칙적으로 JS는 내부 슬롯과 내부 메서드에 짖겁적으로 접근하거나 호출할 수 있는 방법을 제공하지 않는다. 단, 일부는 간접적으로 접근할 수 있는 수단을 제공한다. 허용이 되는 예시는 아래와 같다.

```js
const o = {};

//내부 슬롯은 JS 엔진의 내부 로직이므로 직접 접근할 수 없다.
o.[[Prototype]] //uncaught SyntaxError: Unexpected toekn '['
//단, 일부 내부 슬롯과 메서드에 한해 간접적인 접근 가능!
o.__proto__ //Object.prototype
```

## 2. 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

__JS 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의한다.__
여기서 프로퍼티 상태란, _프로퍼티 값(value), 값의 갱신 가능 여부(writable), 열거 가능 여부(enumerable), 재정의 가능 여부(configurable)_ 를 말한다. 

📌 프로퍼티 어트리뷰트는 JS 엔진이 관리하는 내부 상태 값(meta-property)인 내부 슬롯`[[Value]], [[Writable]], [[Enumerable]], [[Configurable]]`이다. 

직접 접근할 수는 없지만, `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 확인할 수는 있다. 이를 호출할 때는 첫 번째 매개변수에 객체의 참조를 전달하고, 두 번째 매개변수에 프로퍼티 키를 문자열로 전달한다. 
이때 이 메서드는 프로퍼티 어트리뷰트 정보를 제공하는 _프로퍼티 디스크립터(Property Descriptor) 객체를 반환한다._ 

`Object.getOwnPropertyDescriptor`는 하나의 프로퍼티에 대해 프로퍼티 디스크립터 객체를 반환하지만 `Object.getOwnPropertyDescriptors` 메서드는 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.

```js
const person = {
    name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```
```js
const person = {
    name: 'Lee',
};

// 프로퍼티 동적 생성
person.age = 20;

// 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: { value: 'Lee', writable: true, enumerable: true, configurable: true },
  age: { value: 20, writable: true, enumerable: true, configurable: true }
}
*/
```

## 3. 데이터 프로퍼티와 접근자 프로퍼티

__데이터 프로퍼티(data property)__

키와 값으로 구성된 일반적인 프로퍼티다. 지금까지 살펴본 모든 프로퍼티는 데이터 프로퍼티이다. 

아래와 프로퍼티 어트리뷰트를 갖는다. 기본값으로 자동 정의된다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/040d9262-8750-48c3-9f40-f754ed69ef78)


__접근자 프로퍼티(accessor property)__

자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수(accessor function)로 구성된 프로퍼티이다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/f59f8501-e302-495f-abb4-4ba5e7c741cd)

접근자 함수는 getter/setter 함수라고도 부른다. 접근자 프로퍼티는 getter와 setter 함수를 모두 정의할 수도 있고 하나만 정의할 수도 있다.

```js
const person = {
    // 데이터 프로퍼티
    firstName: 'Ungmo',
    lastName: 'Lee',

    // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
    // getter 함수
    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set fullName(name) {
        // 배열 디스트럭처링 할당: " "을 기준으로 분해하여 firstName, lastName 프로퍼티에 할당한다.
        [this.firstName, this.lastName] = name.split(' ');
    }
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + ' ' + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장.
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = 'Heegun Lee'; //여기 프로퍼티 동적 생성인가?
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조.
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

// fistName은 데이터 프로퍼티다.
// 데이터 프로퍼티는 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// {value: "Heegun", writable: true, enumerable: true, configurable: true}

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 [[Get]], [[Set]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```

## 4. 프로퍼티 정의

__프로퍼티 정의란 새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의하는 것을 말한다.__

`Object.defineProperty` 메서드 활용 시 프로퍼티의 어트리뷰트를 정의할 수 있다.

```js
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, 'firstName', {
    value: 'Ungmo',
    writable: true,
    enumerable: true,
    configurable: true
});

Object.defineProperty(person, 'lastName', {
    value: 'Lee'
});

let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log('firstName', descriptor);
// firstName {value: "Ungmo", writable: true, enumerable: true, configurable: true}

// 디스크립터 객체의 프로퍼티를 누락시키면 undefined, false가 기본값이다.
descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// [[Enumerable]]의 값이 false인 경우
// 해당 프로퍼티는 for...in 문이나 Object.keys 등으로 열거할 수 없다.
// lastName 프로퍼티는 [[Enumerable]]의 값이 false다.
console.log(Object.keys(person)); // ["firstName"]

// [[Writable]]의 값이 false인 경우 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없다.
// lastName 프로퍼티는 [[Writable]]의 값이 false다.
// 이떄 값을 변경하면 에러는 발생하지 않고 무시된다.
person.lastName = 'Kim';

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 삭제할 수 없다.
// lastName 프로퍼티는 [[Configurable]]의 값이 false이므로 삭제할 수 없다.
// 이때 프로퍼티를 삭제하면 에러는 발생하지 않고 무시된다.
delete person.lastName;

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 재정의할 수 없다.
// object.defineProperty(person, 'lastName', { enumerable: true });
// TypeError: Cannot redefine property: lastName

descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// 접근자 프로퍼티 정의
Object.defineProperty(person, 'fullName', {
    // getter 함수
    get() {
        return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set(name) {
        [this.firstName, this.lastName] = name.split(' ');
    },
    enumerable: true,
    configurable: true
});

descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log('fullName', descriptor);
// fullName {get: ƒ, set: ƒ, enumerable: true, configurable: true}

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

만약 해당 메서드로 프로퍼티를 정의할 때 프로퍼티 디스크립터 객체의 프로퍼티를 일부 생략하면 각 프로퍼티 어트리뷰트마다 다른 기본값이 적용된다.

- value ([[Value]])
    - undefined
- get ([[Get]])
    - undefined
- set ([[Set]])
    - undefined
- writable ([[Writeable]])
    - false
- enumerable ([[Enumerable]])
    - false
- configurable ([[Configurable]])
    - false
 
`Object.defineProperty` 메서드는 한 번에 하나의 프로퍼티만 정의할 수 있지만 `Object.defineProperties` 메서드를 사용하면 여러 개의 프로퍼티를 한 번에 정의할 수 있다. 

```js
const person = {};

Object.defineProperties(person, {
    firstName: {
        value: 'Ungmo',
        writable: true,
        enumerable: true,
        configurable: true
    },
    lastName: {
        value: 'Lee',
        writable: true,
        enumerable: true,
        configurable: true
    },
    // 접근자 프로퍼티 정의
    fullName: {
        // getter 함수
        get() {
            return `${this.firstName} ${this.lastName}`;
        },
        // setter 함수
        set(name) {
            [this.firstName, this.lastName] = name.split(' ');
        },
        enumerable: true,
        configurable: true
    }
});

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

## 5. 객체 변경 방지

객체는 변경 가능한 값이므로 재할당 없이 직접 변경할 수 있다. 즉, 프로퍼티를 추가하거나 삭제할 수 있고, 프로퍼티 값을 갱신할 수 있으며, `Object.defineProperty` 또는 `Object.defineProperties` 메서드를 이용하면 프로퍼티 어트리뷰트를 재정의할 수도 있다.

JS는 객체의 변경을 방지하는 다양한 메서드를 제공한다.
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/0cc5e1c2-9860-442b-b22b-3cfc1288e096)

__객체 확장 금지__

- 프로퍼티 추가 금지, 즉, 확장이 금지된 객체는 프로퍼티 추가가 금지됨!
- 확장 가능 여부는 `Object.isExtensible` 메서드로 확인할 수 있다.
  ```js
  const person = {name: "Lee"};

  // person 객체는 확장이 금지된 객체가 아니다.
  console.log(Object.isExtensible(person)); // true
  
  // person 객체의 확장을 금지하여 프로퍼티 추가를 금지한다.
  Object.preventExtensions(person);
  
  // person 객체는 확장이 금지된 객체다.
  console.log(Object.isExtensible(person)); // false
  
  // 프로퍼티 추가가 금지된다.
  person.age = 20; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 추가는 금지되지만 삭제는 가능하다.
  delete person.name;
  console.log(person); // {}
  
  // 프로퍼티 정의에 의한 프로퍼티 추가도 금지된다.
  Object.defineProperty(person, "age", {value: 20});
  // TypeError: Cannot define property age, object is not extensible
  ```

  ❓ 프로퍼티 추가는 동적 추가 & 정의에 의한 추가 2개인지 확인

  __객체 밀봉__

  - 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지, 즉, 읽기와 쓰기만 가능하다.
  - `Object.isSealed` 메서드로 밀봉된 객체 여부를 파악할 수 있다.
 
    ```js
    const person = {name: "Lee"};

    // person 객체는 밀봉(seal)된 객체가 아니다.
    console.log(Object.isSealed(person)); // false
    
    // person 객체를 밀봉(seal)하여 프로퍼티 추가, 삭제, 재정의를 금지한다.
    Object.seal(person);
    
    // person 객체는 밀봉(seal)된 객체다.
    console.log(Object.isSealed(person)); // true
    
    // 밀봉(seal)된 객체는 configurable이 false다.
    console.log(Object.getOwnPropertyDescriptors(person));
    /*
    {
      name: {value: "Lee", writable: true, enumerable: true, configurable: false},
    }
    */
    
    // 프로퍼티 추가가 금지된다.
    person.age = 20; // 무시. strict mode에서는 에러
    console.log(person); // {name: "Lee"}
    
    // 프로퍼티 삭제가 금지된다.
    delete person.name; // 무시. strict mode에서는 에러
    console.log(person); // {name: "Lee"}
    
    __// 프로퍼티 값 갱신은 가능하다.__
    person.name = "Kim";
    console.log(person); // {name: "Kim"}
    
    // 프로퍼티 어트리뷰트 재정의가 금지된다.
    Object.defineProperty(person, "name", {configurable: true});
    // TypeError: Cannot redefine property: name
    ```

__객체 동결__

- 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지, 프로퍼티 값 갱신 금지를 의미한다. 즉, 이는 읽기만 가능하다.
- 동결된 객체 여부는 `Object.isFrozen` 로 확인할 수 있다.
  ```js
  const person = {name: "Lee"};

  // person 객체는 동결(freeze)된 객체가 아니다.
  console.log(Object.isFrozen(person)); // false
  
  // person 객체를 동결(freeze)하여 프로퍼티 추가, 삭제, 재정의, 쓰기를 금지한다.
  Object.freeze(person);
  
  // person 객체는 동결(freeze)된 객체다.
  console.log(Object.isFrozen(person)); // true
  
  // 동결(freeze)된 객체는 writable과 configurable이 false다.
  console.log(Object.getOwnPropertyDescriptors(person));
  /*
  {
    name: {value: "Lee", writable: false, enumerable: true, configurable: false},
  }
  */
  
  // 프로퍼티 추가가 금지된다.
  person.age = 20; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 삭제가 금지된다.
  delete person.name; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 값 갱신이 금지된다.
  person.name = "Kim"; // 무시. strict mode에서는 에러
  console.log(person); // {name: "Lee"}
  
  // 프로퍼티 어트리뷰트 재정의가 금지된다.
  Object.defineProperty(person, "name", {configurable: true});
  // TypeError: Cannot redefine property: name
  ```

__불변 객체__

지금까지 변경 방지 메서드들은 _얕은 변경 방지(shallow only)_ 로 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 줄 수 없다. 때문에 아까 보았던 `Object.freeze` 메서드를 사용한다고 해도 중첩 객체까지 동결할 수 없다.

```js
const person = {
    name: "Lee",
    address: {city: "Seoul"},
};

// 얕은 객체 동결
Object.freeze(person);

// 직속 프로퍼티만 동결한다.
console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결하지 못한다.
console.log(Object.isFrozen(person.address)); // false

person.address.city = "Busan";
console.log(person); // { name: 'Lee', address: { city: 'Busan' } }
```

만약 객체의 중첩 객체까지 동결해 변경이 불가능한 읽기 전용의 불변 객체를 구현할 시 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 `Object.freeze`를 호출해야 한다.

```js
function deepFreeze(target) {
    // 객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
    if (target && typeof target === "object" && !Object.isFrozen(target)) {
        Object.freeze(target);
        /*
            모든 프로퍼티를 순회하며 재귀적으로 동결한다.
            Object.keys 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.
            forEach 메서드는 배열을 순회하며 배열의 각 요소에 대하여 콜백 함수를 실행한다.
        */
        Object.keys(target).forEach((key) => deepFreeze(target[key]));
    }
    return target;
}

const person = {
    name: "Lee",
    address: {city: "Seoul"},
};

// 깊은 객체 동결
deepFreeze(person);

console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결한다.
console.log(Object.isFrozen(person.address)); // true

person.address.city = "Busan";
console.log(person); // { name: 'Lee', address: { city: 'Seoul' } }
```
