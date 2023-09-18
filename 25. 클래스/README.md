# 25. 클래스

## 1. 클래스는 프로토타입의 문법적 설탕인가?

자바스크립트는 프로토타입 기반 객체지향 언어다. 프로토타입 기반 객체지향 언어는 클래스가 필요 없는(class free)객체지향 프로그래밍 언어다.

ES6에서 추가된 클래스가 기존의 프로토타입 기반 객체지향 모델을 폐지하고 새롭게 클래스 기반 객체지향 모델을 제공하는 것은 아니다.
클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 문법적 설탕(syntactic sugar)이라고 볼 수도 있다.

클래스와 생성자 함수는 모두 프로토타입 기반의 인스턴스를 생성하지만 정확히 동일하게 동작하지는 않는다.

차이점은 아래와 같다.

  1. 클래스를 `new` 연산자 없이 호출하면 에러가 발생한다.(생성자 함수를 new연산자 없이 호출하면 일반 함수로 호출된다.)
  2. 클래스는 상속을 지원하는 `extends`와 `super` 키워드를 제공한다.(생성자 함수는 지원하지 않는다.)
  3. 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다.(함수 선언문은 함수 호이스팅, 함수 표현식은 변수 호이스팅이 발생한다.)
  4. 클래스 내의 모든 코드에는 암묵적으로 strict mode가 지정되어 실행되며 해제할 수 없다.(생성자 함수는 지정되지 않는다.)
  5. 클래스의 constructor, 프로토타입 메서드, 정적 메서드는 열거되지 않는다.(모두 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false다.)

## 2. 클래스 정의

클래스는 파스칼 케이스문으로 생성한다.

```js
//클래스 선언문
class Person{}

//일반적이진 않지만 함수와 마찬가지로 표현식으로 클래스를 정의할 수도 있다. 이 때 클래스는 함수와 마찬가지로 이름이 있을 수도, 없을 수도 있다.
//익명 클래스
const Person = class {};
//기명 클래스
const Person = class MyClass {};
```

클래스를 표현식으로 정의할 수 있다는 것은 클래스가 값으로 사용할 수 있는 일급 객체라는 것을 의미한다. 클래스는 일급 객체로서 다음과 같은 특징을 갖는다.

- 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
- 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
- 함수의 매개변수에게 전달할 수 있다.
- 함수의 반환값으로 사용할 수 있다.


- 클래스 몸체에는 0개 이상의 메서드만 정의할 수 있다. 클래스 몸체에서 정의할 수 있는 메서드는 `constructor(생성자)`, `프로토타입 메서드`, `정적 메서드` 3가지가 있다.

  ```js
  // 클래스 선언문
  class Person {
      // 생성자
      constructor(name) {
          // 인스턴스 생성 및 초기화
          this.name = name; // name 프로퍼티는 public하다.
      }
  
      // 프로토타입 메서드
      sayHi() {
          console.log(`Hi! My name is ${this.name}`);
      }
  
      // 정적 메서드
      static sayHello() {
          console.log('Hello!');
      }
  }
  
  // 인스턴스 생성
  const me = new Person('Lee');
  
  // 인스턴스의 프로퍼티 참조
  console.log(me.name); // Lee
  // 프로토타입 메서드 호출
  me.sayHi(); // Hi! My name is Lee
  // 정적 메서드 호출
  Person.sayHello(); // Hello!
  ```
  ![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/d4803d42-8ffd-4fd8-a904-d084042cf615)

## 3. 클래스 호이스팅

__클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 소스코드 평가 과정, 즉 런타임 이전에 먼저 평가되어 함수 객체를 생성한다.__
이때 클래스가 평가되어 생성된 함수 객체는 생성자 함수로서 호출될 수 있는 함수, 즉 constructor다. 생성자 함수로서 호출할 수 있는 함수는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다. 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.

단, 클래스는 클래스 정의 이전에 참조할 수 있다.
```js
console.log(Person);
// ReferenceError: Cannot access 'Person' before initialization

// 클래스 선언문
class Person {}
```
클래스 선언문은 `const`, `let`과 같이 호이스팅이 발생되지만 발생하지 않는 것처럼 보인다. 하지만 그렇지 않다.
```js
const Person = '';

{
    // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
    console.log(Person);
    // ReferenceError: Cannot access 'Person' before initialization

    // 클래스 선언문
    class Person { }
}
```
즉, `var, let, const, function, function*, class` 키워드를 사용하여 선언된 모든 식별자는 호이스팅된다. 모든 선언문은 런타임 이전에 먼저 실행되기 때문이다.

## 4. 클래스 호이스팅

__클래스는 생성자 함수이며 `new` 연산자와 함께 호출되어 인스턴스를 생성한다.__

```js
class Person { }

// 인스턴스 생성
const me = new Person();
console.log(me); // Person {}
```

함수와 다르게 __클래스는 인스턴스를 생성하는 것이 유일한 존재 이유이므로 반드시 `new` 연산자와 함께 호출해야 한다.__
```js
class Person { }

// 클래스를 new 연산자 없이 호출하면 타입 에러가 발생한다.
const me = Person();
// TypeError: Class constructor Person cannot be invoked without 'new'
```
클래스 표현식으로 정의된 클래스의 경우 클래스를 가리키는 식별자(Person)을 사용해 인스턴스를 생성하지 않고 기명 클래스 표현식의 클래스 이름(MyClass)를 사용해 인스턴스를 생성하면 에러가 발생한다. 클래스 표현식에서 사용한 이름은 외부 코드에서 접근 불가능하기 때문이다.

```js
const Person = class MyClass { };

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new Person();

// 클래스 이름 MyClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자다.
console.log(MyClass); // ReferenceError: MyClass is not defined

const you = new MyClass(); // ReferenceError: MyClass is not defined
```

## 5. 메서드

__클래스 몸체에는 0개 이상의 메서드만 선언할 수 있다. 클래스 몸체에서 정의할 수 있는 메서드는 constructor(생성자), 프로토타입 메서드, 정적 메서드 3가지이다.__

### constructor

인스턴스를 생성하고 초기화하기 위한 특수한 메서드이다. 이는 이름을 변경할 수 없다. 
```js
class Person {
    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }
}

// 클래스는 함수다.
console.log(typeof Person); // function
console.dir(Person);
```

`constructor`는 메서드로 해석되는 것이 아니라 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다. 즉, 클래스 정의가 평가되면 `constructor` 기술된 동작을 하는 함수 객체가 생성된다.

`constructor`는 생성자 함수와 유사하지만 몇 가지 차이가 있다.

1. 클래스 내에서 `constructor`는 최대 한 개만 존재한다.
  - 2개 이상 포함 시 문법 에러가 발생한다.
2. `constructor` 생략 시 빈 contructor가 암묵적으로 정의된다.
3. 프로퍼티가 추가되어 초기화된 인스턴스를 사용하려면 `constructor` 내부에서 `this`에 인스턴스 프로퍼티를 추가한다.
4. 인스턴스를 생성할 때 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 `constructor`에 매개변수를 선언하고 인스턴스를 생성할 때 초기값을 전달한다.
5. `constructor` 는 별도의 반환문을 갖지 않아야 한다. `new` 연산자와 함께 클래스가 호출되면 생성자 함수와 동일하게 암묵적으로 `this`, 즉 인스턴스를 반환하기 때문이다.
6. `this`가 아닌 다른 객체를 명시적으로 반환하면 return 문에 명시된 객체가 반환된다.

### 프로토타입 메서드

생성자 함수를 이용해 인스턴스 생성 시 프로토타입 메서드를 생성하기 위해서는 명시적으로 프로토타입에 메서드를 추가해야 한다.
```js
//생성자 함수
function Person(name) {
  this.name;
}

//프로토타입 메서드
Person.prototype.sayHi = function() {
  console.log(`Hi! I am ${this.name}`);
};

const me = new Person('Lee');
me.sayHi(); //Hi! I am Lee
```

클래스 몸체에서 정의한 메서드는 생성자 함수 객체 생성 방식과 다르게 클래스의 `prototype` 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다. 
```js
class Person {
    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }

    // 프로토타입 메서드
    sayHi() {
        console.log(`Hi! My name is ${this.name}`);
    }
}

const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee
```

생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다. 인스턴스는 프로토타입 메서드를 상속받아 사용할 수 있다.

즉, 클래스는 생성자 함수와 같이 인스턴스를 생성하는 생성자 함수라고 볼 수 있다.

#### 정적 메서드

정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드이다.

생성자 함수의 경우 정적 메서드를 생성하기 위해서 명시적으로 생성자 함수에 메서드를 추가해야 한다. 
```js
// 생성자 함수
function Person(name) {
    this.name = name;
}

// 정적 메서드
Person.sayHi = function () {
    console.log('Hi!');
};

// 정적 메서드 호출
Person.sayHi(); // Hi!
```

클래스에서 메서드에 `static` 키워드를 붙이면 정적 메서드(클래스 메서드)가 된다.
```js
class Person {
    // 생성자
    constructor(name) {
        // 인스턴스 생성 및 초기화
        this.name = name;
    }

    // 정적 메서드
    static sayHi() {
        console.log('Hi!');
    }
}
```

정적 메서드는 인스턴스로 호출할 수 없다.(정적 메서드가 바인딩된 클래스는 인스턴스의 프로토타입 체인상에 존재하지 않는다.)

```js
// 인스턴스 생성
const me = new Person('Lee');
me.sayHi(); // TypeError: me.sayHi is not a function
```

### 정적 메서드와 프로토타입 메서드의 차이

1. 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
2. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.

### 클래스에서 정의한 메서드의 특징

1. `function` 키워드를 생략한 메서드 축약 표현을 사용한다.
2. 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
3. 암묵적으로 strict mode로 실행된다.
4. `for...in`문이나 `Object.keys` 메서드 등으로 열겨할 수 없다. 즉, 프로퍼티의 열거 가능 여부를 나타내며, 불리언 값을 갖는 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false이다.
5. 내부 메서드 [[Construct]]를 갖지 않는 `non-constructor`이다. 따라서 `new` 연산자와 함께 호출할 수 없다.

## 6. 클래스의 인스턴스 생성 과정

1. 인스턴스 생성과 this 바인딩

- `new` 연산자와 함께 클래스 호출 시 `contructor`의 내부 코드가 실행되기에 앞서 암묵저긍로 빈 객체가 생성된다. 완성되지는 않았지만, 이 빈 객체가 클래스가 생성한 인스턴스이다.
- 빈 객체는 this에 바인딩된다

2. 인스턴스 초기화
- `constructor` 내부의 코드가 실행되며 `this`에 바인딩되어 있는 인스턴스를 초기화한다.
- 인스턴스에 프로퍼티를 추가하고 초기화한다.

3. 인스턴스 반환
- 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.
  ```js
  class Person {
    // 생성자
    constructor(name) {
      // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
      console.log(this); // Person {}
      console.log(Object.getPrototypeOf(this) === Person.prototype); // true
  
      // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
      this.name = name;
  
      // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
    }
  }
  ```

## 7. 프로퍼티

### 인스턴스 프로퍼티

인스턴스 프로퍼티는 `constructor` 내부에서 정의해야 한다. 
인슨턴스에 프로퍼티가 추가가 된다면 인스턴스가 초기화된다.

```js
class Person {
    constructor(name) {
        // 인스턴스 프로퍼티
        this.name = name; // name 프로퍼티는 public하다.
    }
}

const me = new Person('Lee');

// name은 public하다.
console.log(me.name); // Lee
```

### 접근자 프로퍼티

__접근자 프로퍼티(accessor property)는 자체적으로는 값([[Value]])을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.__

접근자 프로퍼티는 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용되는 접근자 함수, `getter`와 `setter` 함수로 구성되어 있다. 
- `getter`는 인스턴스 프로퍼티에 접근할 때마다 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 쓰인다. 반드시 무언가를 반환해야 한다.
- `setter`는 인스턴스 프로퍼티에 값을 할당할 때마다 프로퍼티 값을 조작하거나 별도의 행위가 필요할 때 사용된다. 반드시 매개변수가 있어야 한다. 
- 이 둘은 인스턴스 프로퍼티처럼 사용된다.

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

const me = new Person('Ungmo', 'Lee');

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(`${me.firstName} ${me.lastName}`); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
me.fullName = 'Heegun Lee';
console.log(me); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(me.fullName); // Heegun Lee

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescriptor(Person.prototype, 'fullName'));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}
```

### 클래스 필드 정의 제안

__클래스 필드(or 멤버)는 클래스 기반 객체지향 언어에서 클래스 생성할 인스턴스의 프로퍼티를 가리키는 용어이다.__

```js
// 자바의 클래스 정의
public class Person {
  // ① 클래스 필드 정의
  // 클래스 필드는 클래스 몸체에 this 없이 선언해야 한다.
  private String firstName = "";
  private String lastName = "";

  // 생성자
  Person(String firstName, String lastName) {
    // ③ this는 언제나 클래스가 생성할 인스턴스를 가리킨다.
    this.firstName = firstName;
    this.lastName = lastName;
  }

  public String getFullName() {
    // ② 클래스 필드 참조
    // this 없이도 클래스 필드를 참조할 수 있다.
    return firstName + " " + lastName;
  }
}
```

- JS의 클래스에서 인스턴스 프로퍼티를 선언하고 초기화하려면 반드시 constructor 내부에서 this에 프로퍼티를 추가해야 한다.
- JS 클래스에서 인스턴스 프로퍼티를 참조하기 위해서는 반드시 this를 사용해 참조한다.
- 클래스 기반 객체지향 언어의 this는 언제나 클래스가 생성할 인스턴스를 가리킨다.
- JS의 클래스 몸체에는 메서드만 선언할 수 있다. 클래스 몸체에 클래스 필드 선언 시 문법 에러가 발생한다.

⚒ 자바와는 차이점이 많다.

하지만 아래 코드를 최신 브라우저 또는 최신 Node.js에서 실행하면 문법 에러가 발생하지 않고 정상 동작한다.
```js
class Person {
    // 클래스 필드 정의
    name = 'Lee';
}

const me = new Person();
console.log(me); // Person {name: "Lee"}
```
인스턴스 프로퍼티를 마치 클래스 기반 객체지향 언어의 클래스 필드처럼 정의할 수 있는 새로운 표준 사양이 제안되었기 때문이다. 이를 클래스 필드 정의 제안이라 한다. 이는 정식 표준 사양으로 승급되지 않았으나 최신 브라우저, Node.js에서 지원하고 있다.

### private 필드 정의 제안

클래스 필드 정의 제안을 사용하더라도 클래스 필드는 기본적으로 public하기 때문에 외부에 그대로 노출된다.

하지만, private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다.

private 필드의 선두에는 `#`을 붙여준다. 참조할 때도 #를 붙여주어야 한다. private 필드는 클래스 내부에서만 참조할 수 있다.

```js
class Person {
    // private 필드 정의
    #name = '';

    constructor(name) {
        // private 필드 참조
        this.#name = name;
    }
}

const me = new Person('Lee');

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class
```

👀 타입스크립트는 클래스 기반 객체지향 언어가 지원하는 접근 제한자인 public, private, protected를 모두 지원하며, 의미 또한 기본적으로 동일하다.

클래스 외부에서 private 필드에 직접 접근할 수 있는 방법은 없지만, 접근자 프로퍼티를 통해 간접적으로 접근하는 방법은 유효하다. 
```js
class Person {
    // private 필드 정의
    #name = '';

    constructor(name) {
        this.#name = name;
    }

    // name은 접근자 프로퍼티다.
    get name() {
        // private 필드를 참조하여 trim한 다음 반환한다.
        return this.#name.trim();
    }
}

const me = new Person(' Lee ');
console.log(me.name); // Lee
```

private 필드는 반드시 클래스 몸체에 정의해야 한다. private 필드를 직접 constructor에 정의하면 에러가 발생한다.

```js
class Person {
    constructor(name) {
        // private 필드는 클래스 몸체에서 정의해야 한다.
        this.#name = name;
        // SyntaxError: Private field '#name' must be declared in an enclosing class
    }
}
```

### static 필드 정의 제안

`static` 키워드를 사용하여 static public field, static private field, static private 메서드를 정의할 수 있는 새로운 표준 사양인 “Static class features”가 TC39 프로세스의 stage 3(candidate)에 제안되어 있다.(static-class-features)
최신 브라우저(chrome 72이상)와 Node.js(버전 12 이상)에 이미 구현되어 있다.

```js
class MyMath {
    // static public 필드 정의
    static PI = 22 / 7;

    // static private 필드 정의
    static #num = 10;

    // static 메서드
    static increment() {
        return ++MyMath.#num;
    }
}

console.log(MyMath.PI); // 3.142857142857143
console.log(MyMath.increment()); // 11
```

## 8. 상속에 의한 클래스 확장

### 클래스 상속과 생성자 함수 상속

__상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의하는 것이다.__

상속에 의한 클래스 확장은 코드 재사용 관점에서 매우 유용하다. 

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/0c17f5fa-a9a7-4942-9ea1-2bdaaf8168ac)

클래스는 상속을 통해 기존 클래스를 확장할 수 있는 문법이 기본적으로 제공되지만 _생성자 함수는 그렇지 않다._

### extends 키워드

상속을 통해 클래스를 확장하기 위해서 extends 키워드를 사용해 상속받을 클래스를 정의한다.
```js
// 수퍼(베이스/부모)클래스
class Base {}

// 서브(파생/자식)클래스
class Derived extends Base {}
```

### 동적 상속

extends 키워드는 클래스 뿐 만 아니라 생성자 함수를 상속받아 클래스를 확장할 수도 있다. 단, extends 키워드 앞에는 반드시 클래스가 와야 한다. 
```js
// 생성자 함수
function Base(a) {
  this.a = a;
}

// 생성자 함수를 상속받는 서브클래스
class Derived extends Base {}

const derived = new Derived(1);
console.log(derived); // Derived {a: 1}
```

extends 키워드 다음에는 클래스 뿐만 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다. 이를 통해 동적으로 상속 받을 대상을 결정할 수 있다.
```js
function Base1() {}

class Base2 {}

let condition = true;

// 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

### 서브클래스의 constructor

서브클래스에서 constructor를 생략하면 _클래스에 다음과 같은 constructor가 암묵적으로 정의된다._ 

args는 new 연산자와 함께 클래스를 호출할 때 전달한 인수의 리스트다. 
super()는 수퍼클래스의 constructor를 호출하여 인스턴스를 생성한다.
```js
// 수퍼클래스
class Base {
  constructor() {}
}

// 서브클래스
class Derived extends Base {
  constructor() { super(); }
}

const derived = new Derived();
console.log(derived); // Derived {}
```

### super 키워드

`super` 키워드는 함수처럼 호출할 수도 있고 this와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.

- super를 호출하면 수퍼클래스의 constructor를 호출한다.
- super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

__super 호출__

__super를 호출하면 수퍼클래스의 constructor(super-constructor)을 호출한다.__
수퍼클래스의 constructor내부에서 추가한 프로퍼티를 그대로 갖는 인스턴스를 생성한다면 서브클래스의 constructor를 생략할 수 있다. 이때 new 연산자와 함께 서브클래스를 호출하면서 전달한 인수는 모두 서브클래스에 암무적으로 정의된 cosntructor의 super호출을 통해 수퍼클래스의 constructor에 전달된다.
```js
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  // 다음과 같이 암묵적으로 constructor가 정의된다.
  // constructor(...args) { super(...args); }
}

const derived = new Derived(1, 2);
console.log(derived); // Derived {a: 1, b: 2}
```

수퍼클래스에서 추가한 프로퍼티와 서브클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브클래스의 constructor를 생략할 수 없다. 이때 new 연산자와 함꼐 서브클래스를 호출하면서 전달한 인수 중에서 수퍼 클래스의 constructor에 전달할 필요가 있는 인수는 서브클래스의 constructor에서 호출하는 super를 통해 전달한다.
```js
// 수퍼클래스
class Base {
    constructor(a, b) { // ④
        this.a = a;
        this.b = b;
    }
}

// 서브클래스
class Derived extends Base {
    constructor(a, b, c) { // ②
        super(a, b); // ③
        this.c = c;
    }
}

const derived = new Derived(1, 2, 3); // ①
console.log(derived); // Derived {a: 1, b: 2, c: 3}
```

👀 super 호출 시 주의사항
1. 서브클래스에서 constructor를 생략하지 않는 경우, 서브클래스의 constructor에서는 반드시 super를 호출해야 한다.
2. 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.
3. super는 반드시 서브클래스의 constructor에서만 호출해야 한다. 서브클래스가 아닌 클래스 또는 함수에서 super를 호출하면 에러가 발생한다.

__super 참조__

__메서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.__

```js
// 수퍼클래스
class Base {
    constructor(name) {
        this.name = name;
    }

    sayHi() {
        return `Hi! ${this.name}`;
    }
}

// 서브클래스
class Derived extends Base {
    sayHi() {
        // super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.
        return `${super.sayHi()}. how are you doing?`;
    }
}

const derived = new Derived('Lee');
console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

super 참조를 통해 수퍼클래스의 메서드를 참조하려면 super가 수퍼클래스의 메서드가 바인딩된 객체, 즉 수퍼클래스의 prototype 프로퍼티에 바인딩된 프로토타입을 참조할 수 있어야 한다.

```js
// 수퍼클래스
class Base {
    constructor(name) {
        this.name = name;
    }

    sayHi() {
        return `Hi! ${this.name}`;
    }
}

class Derived extends Base {
    sayHi() {
        // __super는 Base.prototype을 가리킨다.
        const __super = Object.getPrototypeOf(Derived.prototype);
        return `${__super.sayHi.call(this)} how are you doing?`;
    }
}
```

1. `super`는 자신을 참조하고 있는 메서드(`Derived-sayHi`)가 바인딩 되어 있는 객체(`Derived.prototype`)의 프로토타입(`Base.prototype`)을 가리키고 있다. 
2. `super.sayHi`는 `Base.prototype.sayHi`를 가리킨다.
3. `super.sayHi == Base.prototype.sayHi`를 호출할 때는 call 메서드를 사용해 this를 전달해야 한다.
4. `Base.prototype.sayHi`를 그대로 호출 시 메서드 내부의 this는 `Base.prototype`을 가리킨다.
5. `Base.prototype.sayHi` 메서드는 프로토타입 메서드이므로 내부의 this는 `Base.prototype`이 아닌 인스턴스를 가리켜야 한다. `name` 프로퍼티는 인스턴스에 존재하기 때문이다.
6. `super` 참조가 동작하기 위해서는 `super`를 참조하고 있는 메서드(`Derived-sayHi`)가 바인딩되어 있는 객체(`Derived.prototype`)의 프로토타입(`Base.prototype`)을 찾을 수 있어야 한다. 이를 위해 메서드 내부 슬롯 [[HomeObject]]를 가지며 자신이 바인딩하고 있는 객체를 가리킨다.

### 상속 클래스의 인스턴스 생성 과정

상속 관계에 있는 두 클래스를 어떻게 협력해 인스턴스를 생성할 수 있을지 확인해보자. 

```js
// 수퍼클래스
class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }

    getArea() {
        return this.width * this.height;
    }

    toString() {
        return `width = ${this.width}, height = ${this.height}`;
    }
}

// 서브클래스
class ColorRectangle extends Rectangle {
    constructor(width, height, color) {
        super(width, height);
        this.color = color;
    }

    // 메서드 오버라이딩
    toString() {
        return super.toString() + `, color = ${this.color}`;
    }
}

const colorRectangle = new ColorRectangle(2, 4, 'red');
console.log(colorRectangle); // ColorRectangle {width: 2, height: 4, color: "red"}

// 상속을 통해 getArea 메서드를 호출
console.log(colorRectangle.getArea()); // 8
// 오버라이딩된 toString 메서드를 호출
console.log(colorRectangle.toString()); // width = 2, height = 4, color = red
```

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/9809c46d-3ea3-42d3-b270-3d2b5510a34c)

__1. 서브클래스의 super호출__
JS 엔진은 클래스를 평가할 때 수퍼클래스와 서브클래스를 구분하기 위해 “base” 또는 “derive”를 값으로 하는 내부 슬롯[[ConstructorKind]]를 갖는다. 서브클래스는 자신이 직접 인스턴스를 생성하지 않고, 수퍼클래스에게 인스턴스 생성을 위임한다.

서브클래스가 new 연산자와 함께 호출되면 서브클래스 constructor 내부의 super 키워드가 함수처럼 호출된다.(수퍼클래스가 평가되어 생성된 함수 객체의 코드가 실행되기 시작한다.)

만약 서브클래스 constructor 내부에 super호출이 없으면 에러가 발생한다. 실제 인스턴스를 생성하는 주체는 수퍼클래스이므로 수퍼클래스의 constructor를 호출하는 super가 호출되지 않으면 인스턴스를 생성할 수 없기 때문이다.

__2.수퍼클래스의 인스턴스 생성과 this바인딩__
수퍼클래스의 constructor내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성한다.
이떄, 생성된 빈 객체는 this에 바인딩된다.

__3. 수퍼클래스의 인스턴스 초기화__
수퍼클래스의 constructor가 실행되어 this에 바인딩되어 있는 인스턴스를 초기화한다. 
즉, this에 바인딩 되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

__4. 서브클래스 constructor로의 복귀와 this 바인딩__
super 호출이 종료되고 제어 흐름이 서브클래스 constructor로 돌아온다. 
이때 super가 반환한 인스턴스가 this에 바인딩된다. 

서브클래스는 별도의 인스턴스를 생성하지 않고 super가 반환한 인스턴스를 this에 바인딩하여 그대로 사용한다.

__5. 서브클래스의 인스턴스 초기화__
super 호출 이후, 서브클래스의 constructor에 기술되어 있는 인스턴스 초기화가 진행된다. 즉, this에 바인딩되어 있는 인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

__6. 인스턴스 반환__

클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.


### 표준 빌트인 생성자 함수 확장

`extends` 키워드 다음으로 클래스 뿐만 아니라 [[Construct]] 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다. 표준 빌트인 객체도 `extends` 키워드를 사용하여 확장할 수 있다.

```js
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
    // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
    uniq() {
        return this.filter((v, i, self) => self.indexOf(v) === i);
    }

    // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
    average() {
        return this.reduce((pre, cur) => pre + cur, 0) / this.length;
    }
}

const myArray = new MyArray(1, 1, 2, 3);
console.log(myArray); // MyArray(4) [1, 1, 2, 3]

// MyArray.prototype.uniq 호출
console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
// MyArray.prototype.average 호출
console.log(myArray.average()); // 1.75
```

이때, Array.prototype 메서드 중에서 `map, filter`와 같이 새로운 배열을 반환하는 메서드가 `myArray` 클래스의 인스턴스를 반환한다는 것을 주의해야 한다. 

만약 새로운 배열을 반환하는 메서드가 `Array`의 인스턴스를 반환하면 `MyArray` 클래스의 메서드와 메서드 체이닝이 불가능하다.
```js
// 메서드 체이닝
// [1, 1, 2, 3] => [ 1, 1, 3 ] => [ 1, 3 ] => 2
console.log(myArray.filter(v => v % 2).uniq().average()); // 2
```
만약 `MyArray` 클래스의 `uniq` 메서드가 `MyArray` 클래스가 생성한 인스턴스가 아닌 `Array`가 생성한 인스턴스를 반환하게 하려면 `Symbol.species`를 사용하여 정적 접근자 프로퍼티를 추가한다.
```js
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
    // 모든 메서드가 Array 타입의 인스턴스를 반환하도록 한다.
    static get [Symbol.species]() { return Array; }

    // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
    uniq() {
        return this.filter((v, i, self) => self.indexOf(v) === i);
    }

    // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
    average() {
        return this.reduce((pre, cur) => pre + cur, 0) / this.length;
    }
}

const myArray = new MyArray(1, 1, 2, 3);

console.log(myArray.uniq() instanceof MyArray); // false
console.log(myArray.uniq() instanceof Array); // true

// 메서드 체이닝
// uniq 메서드는 Array 인스턴스를 반환하므로 average 메서드를 호출할 수 없다.
console.log(myArray.uniq().average());
// TypeError: myArray.uniq(...).average is not a function
```
