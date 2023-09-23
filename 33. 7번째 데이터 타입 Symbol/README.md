# 33. 7번째 데이터 타입 Symbol

## 1. 심벌이란?

심벌은 변경 불가능한 원시 타입의 값이다. 심벌 값은 다른 값과 중복되지 않는 유일무이한 값이다. 따라서 _주로 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용한다._

프로퍼티 키로 사용할 수 있다.

## 2. 심벌 값의 생성

### Symbol 함수 

다른 원시값(문자열, 숫자, 불리언, undefined, null) 타입의 값은 리터럴 표기법을 통해 값을 생성할 수 있지만 심벌 값은 Symbol 함수를 호출하여 생성해야 한다.

이때 생성된 심벌 값은 외부로 노출되지 않아 확인할 수 없으며, _다른 값과 중복되지 않는 유일무이한 값이다._

```js
//Symbol 함수를 호출하여 유일무이한 심벌 값을 생성한다.
const mySymbol = Symbol();
console.log(typeof mySymbol); //symbol

//심벌 값은 외부로 노출되지 않아 확인할 수 없다.
console.log(mySymbol); //Symbol()
```

생성자 함수로 객체를 생성하는 것처럼 보이지만 Symbol 함수는 String, Number, Boolean 생성자 함수와는 달리 `new` 연산자를 사용하지 않는다. `new` 연산자와 함께 생성자 함수 또는 클래스를 호출하면 객체(인스턴스)가 생성되지만 심벌 ㄱ밧은 변경 불가능한 원시 값이다.

Symbol 함수에는 선택적으로 문자열을 인수로 전달할 수 있다. 
이 문자열은 생성된 심벌 값에 대한 설명(description)으로 디버깅 용도로만 사용되며, 심벌 값 생성에 어떠한 영향도 주지 않는다. 즉, 심벌 값에 대한 설명이 같더라도 생성된 심벌 값은 유일무이한 값이다.
```js
//심벌 값에 대한 설명이 같더라도 유일무이한 심벌 값을 생성한다.
const mySymbol1 = Symbol('mySymbol');
const mySymbol2 = Symbol('mySymbol');

console.log(mySymbol1 === mySymbol2); //false
```

심벌 값도 문자열, 숫자, 불리언과 같이 객체처럼 접근하면 암묵적으로 래퍼 객체를 생성한다. 다음 예제의 description 프로퍼티와 toString 메서드는 `Symbool.prototype`의 프로퍼티다.

```js
const mySymbol = Symbol('mySymbol');

//심벌도 래퍼 객체를 생성한다.
console.log(mySymbol.description); //mySymbol
console.log(mySymbol.toString()); //Symbol(mySymbol)
```

심벌 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않는다. 하지만 불리언 값으로는 암묵적 타입 변환이 된다. 이를 통해 if문 등에서 존재 확인이 가능하다. 
```js
const mySymbol = Symbol();

//문자열이나 숫자 타입으로는 암묵적 변환이 일어나지 않는다.
console.log(mySymbol + ''); //TypeError
console.log(+mySymbol); //TypeError

//불리언은 암묵적 타입 변환이 된다
if (mySymbol) console.log('mySymbol is no empty');
```

### Symbol.for / Symbol.keyFor 메서드

`Symbol.for` 메서드는 인수로 전달받은 문자열을 키로 사용해 키와 심벌 값의 쌍들이 저장되어 있는 전역 심벌 레지스트리(global symbol registry)에서 해당 키와 일치하는 심벌 값을 검색한다.

- 검색에 성공하면 새로운 심벌 값을 생성하지 않고 검색된 심벌 값을 반환한다.
- 검색에 실패 시 새로운 심벌 값을 생성하여 `Symbol.for` 메서드의 인수로 전달된 키로 전역 심벌 레지스트리에 저장한 후, 생성된 심벌 값을 반환한다. 

> 심벌 레지스트리란?
> 자바스크립트 엔진이 관리하는 전역 심볼 레지스트리는 사용 가능한 모든 심볼이 저장되어 있다.
> 전역 심볼 레지스트리는 대부분 자바스크립트 컴파일 인프라에 내장되어 있고, 레지스트리 내용은 자바스크립트 런타임 환경에서는 Symbol.for, Symbol.keyFor 메서드를 사용하지 않고서는 접근이 불가능하다.

```js
//전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for('mySymbol');
//전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 있으면 해당 심벌 값을 반환
const s2 = Symbol.for('mySymbol');

console.log(s1 === s2); //true
```

Symbol 함수는 호출될 때마다 유일무이한 심벌 값을 생성한다. 
이때 JS 엔진이 관리하는 심벌 값 저장소인 전역 심벌 레지스트리에서 심벌 값을 검색할 수 있는 키를 지정할 수 없으므로 전역 심벌 레지스트리에 등록되어 관리되지 않는다. 

하지만 `Symbol.for` 메서드를 사용하면 애플리케이션 전역에서 중복되지 않는 유일무이한 상수인 심벌 값을 단 하나만 생성하여 전역 심벌 레지스트리를 통해 공유할 수 있다. 

`Symbol.keyFor` 메서드를 사용하면 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출할 수 있다. 

```js
//전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for('mySymbol');
//전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s1); // mySymbol

//Symbol 함수를 호출해 생성한 심벌 값은 전역 심벌 레지스트리에 등록되어 관리되지 않는다.
const s2 = Symbol('foo');
//전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s2); //undefined
```

## 3. 심벌과 상수

변경/중복될 가능성이 있는 무의미한 상수 대신 중복될 가능성이 없이 유일무이한 심벌 값을 사용할 수 있다.

```js
const Direction = {
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  Left: Symbol('left'),
  RIGHT: Symbol('right')
};

const myDirection = Direction.UP;

if(myDirection === Direction.UP){
  console.log('You are going UP');
}
```

> __enum__
> enum은 명명된 숫자 상수의 집합으로 열거형이라고 부른다. JS는 enum을 지원하지 않지만 C, 자바, 파이썬 등 여러 프로그래밍 언어와 JS의 상위 확장인 TS에서는 enum을 지원한다.
> JS에서 enum을 흉내 내어 사용하려면 다음과 같이 객체의 변경을 방지하기 위해 객체를 동결하는 `Object.freeze` 메서드와 심벌 값을 사용한다.
> ```js
>//Javascript enum
>//Direction 객체는 불변 객체이며 프로퍼티 값은 유일무이한 값
>const Direction = Object.freeze({
>  UP: Symbol('up'),
>  DOWN: Symbol('down'),
>  LEFT: Symbol('left'),
>  RIGHT: Symbol('right')
>});
>
>const myDirection = Direction.UP;
>
>if(myDirection === Direction.UP) {
>  console.log('You are going UP');
>}

## 4. 심벌과 프로퍼티 키

객체의 프로퍼티 키는 빈 문자열을 포함하는 모든 문자열 또는 심벌 값으로 만들 수 있으며, 동적으로 생성할 수 있다. 

심벌 값을 프로퍼티 키로 사용하려면 프로퍼티 키로 사용할 심벌 값에 대괄호를 사용해야 한다. 

```js
const obj = {
  //심벌 값으로 프로퍼티 키를 생성
  [Symbol.for('mySymbol')]: 1
};

obj[Symbol.for('mySymbol')]; //1
```

__심벌 값은 유일무이한 값이므로 심벌 값으로 프로퍼티 키를 만들면 다른 프로퍼티 키와 절대 충돌하지 않는다.__
기존 프로퍼티  키와 충돌하지 않는 것은 물론, 미래에 추가될 어떤 프로퍼티 키와도 충돌한 위험이 없다. 

## 5. 심벌과 프로퍼티 은닉

심벌 값을 프로퍼티 키로 사용해 생성한 프로퍼티는 `for ... in`문이나 `Object.keys`, `Object.getOwnPropertyNames` 메서드를 찾을 수 없다. 
이처럼 심벌 값을 프로퍼티 키로 사용해 프로퍼티를 생성하면 외부에 노출할 필요가 없는 프로퍼티를 은닉할 수 있다.

```js
const obj= {
  //심벌 값으로 프로퍼티 키를 생성
  [Symbol('mySymbol')]: 1
};

for(const key in obj){
  console.log(key); //아무것도 출력되지 않는다. 
}

console.log(Object.keys(obj)); //[]
console.log(Object.getOwnPrepertyNames(obj)); //[]
```

하지만 __프로퍼티를 완전히 숨길 수 있는 건 아니다!__
`Object.getOwnPropertySymbols` 메서드 사용 시 심벌 값을 프로퍼티 키로 사용해 생성한 프로퍼티를 찾을 수 있다. 

```js
const obj = {
  //심벌 값으로 프로퍼티 생성
  [Symbol('mySymbol')]: 1
};

//getOwnPropertySymbols 메서드는 인수로 전달한 객체의 심벌 프로퍼티 키를 배열로 반환한다.
console.log(Object.getOwnPropertySymbols(obj)); //[Symbol(mySymbol)]

//getOwnPropertySymbols 메서드로 심벌 값도 찾을 수도 있다.
const symbolKey1 = Object.getOwnPropertySymbols(obj)[0];
console.log(obj[SymbolKey1]); //1
```


## 6. 심벌과 표준 빌트인 객체 확장

원래 표준 빌트인 객체에 사용자 정의 메서드를 직접 추가해 확장하는 것은 권장되지 않는다. 표준 빌트인 객체는 읽기 전용으로 사용하는 것이 좋다. 

그 이유는 개발자가 직접 추가한 메서드와 미래에 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문이다. 예를 들어 `Array.prototype.find` 메서드가 ES6에서 도입되기 전에 `Array.prototype`에 사용자 정의 find 메서드를 직접 추가했다면 새롭게 도입된 ES6의 `Array.prototype.find` 메서드와 이름이 중복되어 ES6의 `Array.prototype.find` 메서드를 이전에 추가했던 사용자 정의 find 메서드가 덮어쓴다. 표준 빌트인 메서드를 사용자 정의 메서드가 덮어쓴다면 문제가 된다.

하지만 중복될 가능성이 없는 심벌 값으로 프로퍼티 키를 생성하여 표준 빌트인 객체를 확장하면 표준 빌트인 객체의 기존 프로퍼티 키와 충돌하지 않는 것은 물론, 표준 사양의 버전이 올라감에 따라 추가될지 모르는 어떤 프로퍼티 키와도 충돌할 위험이 없어 안전하게 표준 빌트인 객체를 확장할 수 있다.

```js
//심벌 값으로 프로퍼티 키를 동적 생성하면 다른 프로퍼티 키와 절대 충돌하지 않아 안전하다.
Array.prototype[Symbol.for('sum')] = function () {
  return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2][Symbol.for('sum')](); //3
```

## 7. Well-known Symbol

JS가 기본 제공하는 빌트인 심벌 값을 ESMAScript 사양에서는 _Well-knwon Symbol_ 이라 부른다. 이는 JS 엔진의 내부 알고리즘에 사용된다. 
