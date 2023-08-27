## 9. 타입 변환과 단축 평가

### 1) 타입 변환이란?

JS의 모든 값은 타입이 있다. 값의 타입은 개발자의 의도에 따라 다른 타입으로 변환할 수 있다. 타입 변환은 크게 2가지로 나뉜다. <br><br>
개발자가 의도적으로 값의 타입을 변환하는 __명시적 타입 변환(타입 캐스팅)__ 과 표현식을 평가하는 도중 JS 엔진에 의해 암묵적으로 타입이 자동 변환되는 __암묵적 타입 변환(타입 강제 변환)__ 이 있다.

```js
var x = 10;

//명시적 타입 변환: 숫자를 문자열로 타입 캐스팅!
var str = x.toString();
console.log(typeof str, str); //string 10
console.log(typeof x, x); //number 10. x 변수의 값이 변경된 건 아니다!

//암묵적 타입 변환: 문자열 연결 연산자는 숫자 타입 x의 값을 바탕으로 새로운 문자열을 생성!
var str = x + '';
console.log(typeof str, str); //string 10
console.log(typeof x, x); //number 10. 역시 x 변수 값이 변경된 건 아니다. 
```

> 📌 __타입 변환은 변경 불가능한 값인 원시값을 직접 변경하지 않는다. _타입 변환은 기존 원시 값을 사용해 다른 타입의 새로운 원시 값을 생성한다!___ <br>
자바스크립트 엔진은 표현식을 에러 없이 평가하기 위해 피연산자의 값을 암묵적 타입 변환해 새로운 타입의 값을 만들어 단 한 번만 사용하고 버린다.

### 2) 암묵적 타입 변환

개발자의 의도와는 상관없이 코드의 문맥을 고려해 암묵적으로 데이터 타입을 강제 변환하는 것을 말한다. 
문자열, 숫자, 불리언과 같은 원시 타입 중 하나로 타입을 자동 변환한다. 

#### 문자열 타입으로 변환
- 자바스크립트 엔진은 문자열 연결 연산자 표현식을 평가하기 위해서 문자열 연결 연산자의 피연산자 중 문자열 타입이 아닌 피연산자를 문자열 타입으로 암묵적 타입 변환한다.
- 이때, 연산자 표현식의 피연산자만 암묵적 타입 변환 대상이 되는 건 아니고 표현식을 평가할 때 코드 문맥에 부합하도록 암묵적 타입 변환을 한다.
    - `1 + 1 = ${1 + 1} //"1 + 1 = 2"` <br><br>
      
  ```js
  // 숫자 타입 > 문자열 타입
  0 + '' // "0"
  -0 + '' //  "0"
  1 + '' // "1"
  -1 + '' // "-1"
  NaN + '' // "NaN"
  Infinity + '' //"Infinity"
  -Infinity + '' //"-Infinity"
  
  // 불리언 타입 > 문자열 타입
  true + '' //"true"
  false + '' //"false"
  
  // null 타입 > 문자열 타입
  null + '' //"null"
  
  // undefined 타입 > 문자열 타입
  undefined + '' //"undefined"
  
  // 심벌 타입 > 문자열 타입
  (Symbol()) + '' // TypeError: Cannot convert a Symbol value to a string
  
  // 객체 타입> 문자열 타입
  ({}) + '' // "[object Object]"
  Math + '' // "[object Math]"
  [] + '' // ""
  [10, 20] + '' //"10,20"
  (function(){}) + '' // "function(){}"
  Array + '' // "function Array() { [native code] }"
  ```

#### 숫자 타입으로 변환

- 자바스크립트 엔진은 _산술 연산자 표현식_ 과 _비교 연산자 표현식_ 을 평가하기 위해 중에서 숫자 타입이 아닌 피연산자를 숫자 타입으로 암묵적 타입 변환한다.
- `+` 단항 연산자도 피연산자가 숫자 타입의 값이 아니면 숫자 타입의 값으로 암묵적 타입 변환을 한다. 
- 이때 숫자로 타입 변환을 할 수 없는 표현식의 평가 결과는 `NaN`이 된다.
  
  ```js
  //산술 연산자
  1 - '1' //0
  1 * '10' //10
  1 / 'one' //NaN

  ----
  
  //비교 연산자
  '1' > 0 //true

  ----

  //'+' 단항 연산자
  // 문자열 타입 
  +'' // 0
  +'1' // 1
  +'string' // NaN
  
  // 불리언 타입
  +true //1
  +false // 0
  
  // null 타입
  +null //0
  
  // undefined 타입
  +undefined // NaN
  
  // 심벌 타입
  +Symbol() // TypeError: Cannot convert a Symbol value to a number
  
  // 객체 타입
  +{} // NaN
  +[] // 0
  +[10, 20] // NaN
  +(function(){}) // NaN
  ```

#### 불리언 타입으로 변환

- 자바스크립트 엔진은 `if`문이나 `for`문에서 조건식의 평가 결과를 불리언 타입으로 암묵적 타입 변환한다.
- __자바스크립트 엔진은 불리언 타입이 아닌 값을 Truthy 값(참으로 평가되는 값) 혹은 Falsy값(거짓으로 평가되는 값)으로 구분한다.__ Falsy 값이 아닌 모든 값들은 모두 true로 평가되는 Truthy 값이다.
  
  - Falsy 값들 예시
    
    - false
    - undefined
    - null
    - 0, -0
    - NaN
    - ''(빈 문자열)

  👀 _Truthy/Falsy 판별하는 함수_
  
  ```js
  //전달받은 인수가 Falsy면 true, Truthy 값이면 false 리턴
  function isFalsy(v){
    return !v;
  }
  
  //전달받은 인수가 Falsy면 false, Truthy 값이면 true 리턴
  function isTruthy(v){
    return !!v;
  }
  
  //모두 true 반환
  isFalsy(false);
  isFalsy(undefined);
  isFalsy(null);
  isFalsy(0);
  isFalsy(NaN);
  isFalsy('');
  
  //모두 true 반환
  isTruthy(true);
  isTruthy('0'); //빈 문자열이 아닌 문자열은 Truthy한 값!
  isTruthy([]);
  isTruthy({});
  ```

### 3) 명시적 타입 변환

명시적으로 타입 변환을 하는 방법은 다양하다. (1) 표준 빌트인 생성자 함수(`String, Number, Boolean`)을 new 연산자 없이 호출하는 방법 (2) 빌트인 메서드를 사용하는 방법 (3) 암묵적 타입 변환을 이용하는 방법이 있다. 

#### 문자열 타입으로 변환

문자열 타입이 아닌 값을 문자열 타입으로 변환하는 방법
- String 생성자 함수를 new 연산자 없이 호출하기
- `Object.prototype.toString` 메서드 사용하기
- 문자열 연결 연산자를 이용하기

```js
//1. String 생성자 함수를 new 연산자 없이 호출하기
//숫자 > 문자열
String(1); //'1'
String(NaN); //'NaN'
//불리언 > 문자열
String(true); //'true'

//2. `Object.prototype.toString` 메서드 사용하기
//숫자 > 문자열
(1).toString(); //'1'
(NaN).toString(); //'NaN'
//불리언 > 문자열
(true).toString(); //'true'

//3. 문자열 연결 연산자를 이용하기
//숫자 > 문자열
1 + ''; //'1'
//불리언 > 문자열
true + ''; //'true'
```
#### 숫자 타입으로 변환

숫자 타입이 아닌 값을 숫자 타입으로 변환하는 방법
- Number 생성자 함수를 new 연산자 없이 호출하기
- parseInt, parseFloat 함수를 사용하는 방법(*문자열만 변환된다)
- `+`단항 산술 연산자를 이용하는 방법
- `*` 산술 연산자를 이용하는 방법


```js
//1. Number 생성자 함수를 new 연산자 없이 호출하기
//문자열 > 숫자
Number('0') //0
Number('-1') // -1
//불리언 > 숫자
Number(true); //1

//2. parseInt, parseFloat 함수를 사용하는 방법(*문자열만 변환된다)
//문자열 > 숫자
parseInt('0'); //0
parseFloat('10.53'); //10.53. parseInt로 하니깐 그냥 10이 나옴

//3.`+`단항 산술 연산자를 이용하는 방법
//문자열 > 숫자
+'0'; //0
//불리언 > 숫자
+true; //1

//4. `*` 산술 연산자를 이용하는 방법
//문자열 > 숫자
'0' * 1; //0
//불리언 > 숫자
true * 1 //1
```

#### 불리언 타입으로 변환

불리언 타입이 아닌 값을 불리언 타입으로 변환하는 방법
- Boolean 생성자 함수를 new 연산자 없이 호출하기
- `!` 부정 논리 연산자를 두 번 사용하기

```js
// 1. Boolean 생성자 함수를 new 연산자 없이 호출하는 방법
// 문자열 타입 => 불리언 타입
Boolean('x');       // -> true
Boolean('');        // -> false
Boolean('false');   // -> true
// 숫자 타입 => 불리언 타입
Boolean(0);         // -> false
Boolean(1);         // -> true
Boolean(NaN);       // -> false
Boolean(Infinity);  // -> true
// null 타입 => 불리언 타입
Boolean(null);      // -> false
// undefined 타입 => 불리언 타입
Boolean(undefined); // -> false
// 객체 타입 => 불리언 타입
Boolean({});        // -> true
Boolean([]);        // -> true

// 2. ! 부정 논리 연산자를 두번 사용하는 방법
// 문자열 타입 => 불리언 타입
!!'x';       // -> true
!!'';        // -> false
!!'false';   // -> true
// 숫자 타입 => 불리언 타입
!!0;         // -> false
!!1;         // -> true
!!NaN;       // -> false
!!Infinity;  // -> true
// null 타입 => 불리언 타입
!!null;      // -> false
// undefined 타입 => 불리언 타입
!!undefined; // -> false
// 객체 타입 => 불리언 타입
!!{};        // -> true
!![];        // -> true

```

### 4) 단축 평가

__표현식을 평가하는 도중에 평가 결과가 확정된 경우 나머지 평가 과정을 생략하는 것을 의미한다.__
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/bf181a67-479c-4b76-83cd-aa73f3d10e81)


#### 논리 연산자를 사용한 단축 평가

- 논리합(`||`)연산자 혹은 논리곱(`&&`) 연산자 표현식은 언제나 2개의 피연산자 중 하나로 평가된다. 논리 연산의 결과를 결정하는 피연산자를 타입 변환하지 않고 그대로 반환한다.

  ```js
  // 논리합(||) 연산자: 논리 연산의 결과를 결정한 첫 번째 피연산자를 반환
  'Cat' || 'Dog'  // -> "Cat"
  false || 'Dog'  // -> "Dog"
  'Cat' || false  // -> "Cat"
  
  // 논리곱(&&) 연산자: 논리 연산의 결과를 결정하는 두 번째 피연산자를 반환
  'Cat' && 'Dog'  // -> "Dog"
  false && 'Dog'  // -> false
  'Cat' && false  // -> false
  ```
- 단축 평가를 사용하면 `if`문을 대체할 수 있다.
  
  ```js
  //어떤 조건이 Truthy일 때는 논리곱(&&) 연산자 활용
  var done = true;
  var message = '';
  
  // 주어진 조건이 true일 때
  if (done) message = '완료';
  
  // if 문은 단축 평가로 대체 가능하다.
  // done이 true라면 message에 '완료'를 할당
  message = done && '완료';
  console.log(message); // 완료

  //어떤 조건이 Falsy할 때는 논리곱(||) 연산자 활용
  var done = false;
  var message = '';
  
  // 주어진 조건이 false일 때
  if (!done) message = '미완료';
  
  // if 문은 단축 평가로 대체 가능하다.
  // done이 false라면 message에 '미완료'를 할당
  message = done || '미완료';
  console.log(message); // 미완료
  ```

  📌 __단축 평가가 유용하게 쓰이는 2가지 상황__

  1️⃣  __객체가 가리키기를 기대하는 변수가 `null` 혹은 `undefined`가 아닌지 확인하고 프로퍼티 참조할 때__
  - 객체를 가리키기를 기대하는 변수가 null또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때 단축 평가를 사용하면 에러없이 사용할 수 있다.
    
  ```js
  // 단순 참조
  var elem = null;
  var value = elem.value; // TypeError: Cannot read property 'value' of null
  
  
  // 단축 평가 사용
  var elem = null;
  // elem이 null이나 undefined와 같은 Falsy 값이면 elem으로 평가되고
  // elem이 Truthy 값이면 elem.value로 평가된다.
  var value = elem && elem.value; // -> null
  ```

  2️⃣  __함수 매개변수에 기본값 설정할 떄__
  - 함수를 호출할 때 인수를 전달하지 않으면 매개변수에는 undefined가 할당되는데, 단축 평가를 사용하면 이를 방지할 수 있다.
    
  ```js
  // 단축 평가를 사용한 매개변수의 기본값 설정
  function getStringLength(str) {
    str = str || '';  // 단축 평가
    return str.length;
  }
  
  getStringLength();     // -> 0
  getStringLength('hi'); // -> 2
  
  
  // ES6의 매개변수의 기본값 설정
  function getStringLength(str = '') {
    return str.length;
  }
  
  getStringLength();     // -> 0
  getStringLength('hi'); // -> 2
  ```

#### 옵셔널 체이닝 연산자(`?.`)

- 옵셔널 체이닝 연산자 `?.`는 좌항의 피연산자가 `null` 또는 `undefined`인 경우 `undefined`를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.
  ```js
  var elem = null;
  
  // elem이 null 또는 undefined이면 undefined를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.
  var value = elem?.value;
  console.log(value); // undefined

  //원래는 논리 연산자 &&를 이용한 단축 평가를 통해 변수가 null 혹은 undefined인지 확인했다.
  var value = elem && elem.value;
  console.log(value); //undefined
  ```
- 논리 연산자 &&는 좌항 피연산자가 false로 평가되는 Falsy 값이면 좌항 피연산자를 그대로 반환하지만 0이나 ''은 객체로 평가될 때도 있다. 하지만 옵셔널 체이닝 연산자 ?.는 좌항 피연산자가 Falsy 값이라도 null 또는 undefined가 아니면 우항의 프로퍼티 참조를 이어간다.

  ```js
  var str = '';

  //문자열 길이 참조
  var length = str && str.length;
  //문자열의 길이를 참조하지 못함. 
  console.log(length); // ''

  //문자열의 길이 참조. 이때, 좌항 피연산자가 Falsy 값이라도 null 또는 undefined가 아니면 우항의 프로퍼티 참조를 이어간다.
  var length = str?.length;
  console.log(length); // 0
  ```

#### null 병합 연산자(`??`)

- null 병합 연산자 `??`는 좌항의 피연산자가 `null` 또는 `undefined`인 경우 우항 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.
- 변수에 기본값을 설정할 때 유용하다.

  ```js
  // 좌항의 피연산자가 null 또는 undefined이면 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.
  var foo = null ?? 'default string';
  console.log(foo); // "default string"
  ```
- 만약 Falsy 값인 0 이나 ''도 기본값으로써 유효하다면 예기치 않은 동작이 발생할 수 있다. 하지만 null 병합 연산자 `??`는 좌항의 피연산자가 Falsy 값이라도 null 또는 undefined가 아니면 좌항의 피연산자를 그대로 반환한다.

  ```js
  // Falsy 값인 0이나 ''도 기본값으로서 유효하다면 예기치 않은 동작이 발생할 수 있다.
  var foo = '' || 'default string';
  console.log(foo); // "default string"
  
  
  // 좌항의 피연산자가 Falsy 값이라도 null 또는 undefined이 아니면 좌항의 피연산자를 반환한다.
  var foo = '' ?? 'default string';
  console.log(foo); // ""
  ```
  






