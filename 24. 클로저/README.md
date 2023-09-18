# 24. 클로저

> 클로저의 정의
> A closure is the combination of a function and the lexical environment within which that function was declared.
> 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

## 1. 렉시컬 스코프

JS 엔진은 함수를 어디서 호출했는지가 아니라 __함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다.__

렉시컬 환경의 `외부 렉시컬 환경에 대한 참조` 에 저장할 참조값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경(위치)에 의해 결정된다. 이것이 바로 _렉시컬 스코프_ 이다.

## 2. 함수 객체의 내부 슬롯 [[Environment]]

__함수는 자신의 내부 슬롯 [[Environment]]에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장한다.__

즉, 함수 정의가 평가되어 함수 객체를 생성할 때 자신이 정의된 환경(위치)에 의해 결정된 상위 스코프의 참조를 함수 객체 자신의 내부 슬롯 [[Environment]]에 저장한다.
_이때, 자신의 내부 슬롯 [[Environment]]에 저장된 상위 스코프의 참조는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 따른다._

왜냐하면 함수 정의가 평가되어 함수 객체를 생성하는 시점은 함수가 정의된 환경, 즉 상위 함수(또는 전역 코드)가 평가 또는 실행되고 있는 시점이며, 이떄 현재 실행중인 실행 컨텍스트는 상위 함수(또는 전역 코드)의 실행 컨텍스트이기 때문이다.

따라서 함수 객체의 내부 슬롯 [[Environment]]에 저장된 현재 실행중인 실행 컨텍스트의 렉시컬 환경의 참조가 바로 상위 스코프다. 또한 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 "외부 레시컬 환경에 대한 참조"에 저장할 참조값이다. 함수 객체는 내부 슬롯 [[Envrionment]]에 저장한 렉시컬 환경의 참조, 즉, 상위 스코프를 자신이 존재하는 한 기억한다.

```js
const x = 1;

function foo() {
    const x = 10;

    // 상위 스코프는 함수 정의 환경(위치)에 따라 결정된다.
    // 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
    bar();
}

// 함수 bar는 자신의 상위 스코프, 즉 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
function bar() {
    console.log(x);
}

foo(); // 1
bar(); // 1
```

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/7be6702b-4760-4fa6-a4f1-b3e9048ee314)

__외부 렉시컬 환경에 대한 참조에는 함수 객체의 내부 슬롯 [[Environment]]에 저장된 렉시컬 환경의 참조가 할당된다.__

## 3. 클로저와 렉시컬 환경

__클로저란 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수가 이미 생명 주기가 종료한 외부 함수의 변수를 참조하는 것을 의미한다.__

JS의 모든 함수는 자신의 상위 스코프를 기억한다. 모든 함수가 기억하는 상위 스코프는 함수를 어디서 호출하든 상관없이 유지된다. 따라서 함수를 어디서 호출하든 상관없이 함수는 언제나 자신이 기억하는 상위 스코프의 식별자를 참조할 수 있다.

```js
const x = 1;

// ①
function outer() {
    const x = 10;
    const inner = function () { console.log(x); }; // ②
    return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 pop되어 제거된다.
const innerFunc = outer(); // ③
innerFunc(); // ④ 10
```

위 예제에서 발생하는 일을 쭉 정리해보면,

  1. `outer` 함수가 평가되어 함수 객체를 생성할 때 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉 전역 렉시컬 환경을 `outer` 함수 객체의 [[Environment]] 내부 슬롯에 상위 스코프로서 저장한다.
  2. `outer` 함수를 호출하면 `outer` 함수의 렉시컬 환경이 생성되고 앞서 `outer` 함수 객체의 [[Environment]] 내부 슬롯에 저장된 전역 렉시컬 환경을 `outer` 함수 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 할당한다.
  3. 중첩 함수 `inner`을 평가한다.(런타임에!)
  4. `inner`는 자신의 [[Environment]] 내부 슬롯에 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉, `outer` 함수의 렉시컬 환경을 상위 스코프로 저장한다.
  5. `outer` 함수 실행 종료 시 `inner` 함수를 반환하고 생명 주기 종료된다. 즉, 실행 컨텍스트 스택에서 함수의 실행 컨텍스트가 팝된다. 이때, __`outer` 함수의 렉시컬 환경까지 소멸하는 것은 아니다. `inner`가 참조하고 있기 때문이다.__
  6. `outer` 함수가 반환한 `inner` 함수를 호출할 시 `inner` 함수의 실행 컨텍스트 생성, 스택에 푸시가 된다. 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에는 `inner` 함수 객체의 [[Environment]] 내부 슬롯에 저장되어 있는 참조값 할당된다.

결론적으로 `inner` 함수는 외부 함수 `outer` 보다 더 오래 생존했다. 

하지만 외부 함수보다 중첩 함수가 오래산다고 해서 꼭 클로저인 것은 아니다.

__중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우 클로저의 예시에 속한다.__
또한, 상위 스코프의 식별자 중에서 클로저가 참조하고 이는 식별자만 기억이 된다. 이를 _자유 변수(free variable)_ 이라고 한다.

## 4. 클로저의 활용

__클로저는 상태를 안전하게 변경하고 유지하기 위해 사용한다. 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용한다.__

```js
// 카운트 상태 변경 함수
const increase = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저
  return function () {
    // 카운트 상태를 1만큼 증가 시킨다.
    return ++num;
  };
}());

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드가 실행되면 즉시 실행 함수가 호출되고 즉시 실행 함수가 반환한 함수가 increase 변수에 할당된다. increase 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 스코프인 즉시 실행 함수의 렉시컬 환경을 기억하는 클로저다.
즉시 실행 함수는 한 번만 실행되므로 increase가 호출될 때마다 num 변수가 재차 초기화될 일은 없다. 또한 num 변수는 직접 접근할 수 없는 은닉된 private 변수이므로 전역 변수를 사용했을 때와 같이 의도치 않게 변경될 위험도 없다.

이처럼 클로저는 상태가 의도치 않게 변경되지 않도록 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 변경하고 유지하기 위해 사용한다.

```js
const counter = (function () {
    // 카운트 상태 변수
    let num = 0;

    // 클로저인 메서드를 갖는 객체를 반환한다.
    // 객체 리터럴은 스코프를 만들지 않는다.
    // 따라서 아래 메서드들의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.
    return {
        // num: 0, // 프로퍼티는 public하므로 은닉되지 않는다.
        increase() {
            return ++num;
        },
        decrease() {
            return num > 0 ? --num : 0;
        }
    };
}());

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

위 코드에는 클로저를 활용하면서 감소 기능이 추가가 된 것이다. 

- 생성자 함수로 표현
  ```js
  const Counter = (function () {
    // ① 카운트 상태 변수
    let num = 0;
  
    function Counter() {
      // this.num = 0; // ② 프로퍼티는 public하므로 은닉되지 않는다.
    }
  
    Counter.prototype.increase = function () {
      return ++num;
    };
  
    Counter.prototype.decrease = function () {
      return num > 0 ? --num : 0;
    };
  
    return Counter;
  }());
  
  const counter = new Counter();
  
  console.log(counter.increase()); // 1
  console.log(counter.increase()); // 2
  
  console.log(counter.decrease()); // 1
  console.log(counter.decrease()); // 0
  ```

- 고차 함수를 이용한 클로저
  - 자유 변수 counter를 공유하고 싶다면 렉시컬 환경을 공유하는 클로저를 만들어야 한다. 때문에 makeCounter 함수를 두 번 호출하지 말아야 한다.
  ```js
  // 함수를 반환하는 고차 함수
  // 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
  const counter = (function () {
    // 카운트 상태를 유지하기 위한 자유 변수
    let counter = 0;
  
    // 함수를 인수로 전달받는 클로저를 반환
    return function (aux) {
      // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
      counter = aux(counter);
      return counter;
    };
  }());
  
  // 보조 함수
  function increase(n) {
    return ++n;
  }
  
  // 보조 함수
  function decrease(n) {
    return --n;
  }
  
  // 보조 함수를 전달하여 호출
  console.log(counter(increase)); // 1
  console.log(counter(increase)); // 2
  
  // 자유 변수를 공유한다.
  console.log(counter(decrease)); // 1
  console.log(counter(decrease)); // 0
  ```

## 5. 캡슐화와 정보 은닉

__캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작(behavior)인 메서드를 하나로 묶는 것을 말한다. 이는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉(information hiding)이라고 한다.__

JS의 경우 다른 언어와 다르게 `public, private, protected`와 같이 캡슐화를 위한 접근 제한자를 제공하지 않는다. 따라서 _JS 객체의 모든 프로퍼티와 메서드는 기본적으로 외부에 공개되어 있다. 즉, 객체의 모든 프로퍼티와 메서드는 기본적으로 `public`하다._

- 인스턴스 메서드에서 지역 변수 참조
  ```js
  function Person(name, age) {
    this.name = name; // public
    let _age = age;   // private
  
    // 인스턴스 메서드
    this.sayHi = function () {
      console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
    };
  }
  
  const me = new Person('Lee', 20);
  me.sayHi(); // Hi! My name is Lee. I am 20.
  console.log(me.name); // Lee
  console.log(me._age); // undefined
  
  const you = new Person('Kim', 30);
  you.sayHi(); // Hi! My name is Kim. I am 30.
  console.log(you.name); // Kim
  console.log(you._age); // undefined
  ```

- 프로토타입 메서드에서 지역 변수 참조
  ```js
  function Person(name, age) {
    this.name = name; // public
    let _age = age;   // private
  }
  
  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    // Person 생성자 함수의 지역 변수 _age를 참조할 수 없다
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };
  ```

- 프로토타입 메서드를 클로저로 활용
  ```js
  const Person = (function () {
    let _age = 0; // private
  
    // 생성자 함수
    function Person(name, age) {
      this.name = name; // public
      _age = age;
    }
  
    // 프로토타입 메서드
    Person.prototype.sayHi = function () {
      console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
    };
  
    // 생성자 함수를 반환
    return Person;
  }());
  
  const me = new Person('Lee', 20);
  me.sayHi(); // Hi! My name is Lee. I am 20.
  console.log(me.name); // Lee
  console.log(me._age); // undefined

  const you = new Person('Kim', 30);
  you.sayHi(); // Hi! My name is Kim. I am 30.
  console.log(you.name); // Kim
  console.log(you._age); // undefined
  ```

  이 예제는 즉시 실행 함수로 감싸서 그 안에 지역 변수 `_age`를 선언하고 그 안에 Person생성자 함수와 프로토타입 메서드를 선언하고 생성자 함수를 반환하도록 한다.

  그럼 즉시 실행 함수는 종료되었지만 반환된 Person 생성자 함수는 여전히 지역 변수 `_age`를 참조할 수 있는 클로저이다.

  하지만 이 방법도 완벽하지 않다. 이는 `Person.prototype.sayHi` 메서드가 단 한 번 생성되는 클로저이기 때문에 발생하는 현상이다. 이에 `_age` 변수의 상태가 유지가 되지 않는다.

  결론적으로 JS는 정보 은닉을 완전하게 지원하지 못한다. 대신 `private` 필드를 정의할 수 있는 새로운 표준 사양이 제안되고 있는 상황이다.

## 6. 자주 발생하는 실수

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
    funcs[i] = function () { return i; }; // ①
}

for (var j = 0; j < funcs.length; j++) {
    console.log(funcs[j]()); // ②
}
```

- 0 1 2가 출력될 것을 기대하였지만 3 3 3이 출력된다. 이유는 `var`로 선언된 변수는 전역 변수로 선언되었기 때문에 함수 선언문에 `i` 값을 전달하였더라도, 호출하는 시점에는 `i` 값이 3이기 때문에 3 3 3이 출력된다.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
    funcs[i] = (function (id) { // ①
        return function () {
            return id;
        };
    }(i));
}

for (var j = 0; j < funcs.length; j++) {
    console.log(funcs[j]());
}
```

- 클로저로 바르게 고치면 위와 같이 된다.
- for문 내부에 함수를 선언할 때 즉시 실행 함수로 감싸고 i값을 전달해 주도록 수정한다. 이렇게 하면 새로운 함수 레벨 스코프가 반복문을 반복하면서 계속 생겨 각각의 스코프에 i값이 id 매개변수에 저장되게 된다. 즉, id는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유 변수가 되어 그 값이 유지된다.

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () { return i; };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
```

- 하지만 `let`을 사용하면 더 간단하게 할 수 있는데, 이는 `let`이 블록 스코프를 따르기 때문에 렉시컬 환경이 반복문이 실행될 때마다 생성되기 때문이다.
