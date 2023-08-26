## 7. 연산자

연산자는 하나 이상의 표현식을 대상으로 산술, 할당, 비교, 논리, 타입, 지수 연산 등을 수행해 하나의 값을 만든다.

피연산자 및 피연산자와 연산자의 조합으로 이뤄진 연산자 표현식도 값으로 평가될 수 있다. 

### 1) 산술 연산자

피연산자를 대상으로 수학적 계산을 수행해 _새로운 숫자_ 값을 만든다. 연산이 불가능한 경우 `NaN`을 반환한다!

__이항 산술 연산자__ (`+, -, *, /, %`)

- 2개의 피연산자를 산술 연산하여 숫자 값을 만든다.
- 피연산자의 값을 변경하는 부수 효과(side effect)이 없다. 즉, _어떤 산술 연산을 해도 피연산자의 값이 바뀌는 경우는 없고 언제나 새로운 값을 만든다._

❓ 부수 효과 발생시 메모리 상에서는 어떤 일 발생?

__단항 산술 연산자__ (`++, --, +, -`)

- 1개의 피연산자를 산술 연산하여 숫자 값을 만든다.

- __`++`, `--` 단항 연산자__
  - __증가/감소 연산자는 증감 연산을 하며, 피연산자의 값을 변경하는 부수 효과가 있다.__
    
    ```javascript
    var x = 5, result;

    //선할당 후증가(postfix increment operator)
    result = x++;
    console.log(result, x); //5 6

    //선증가 후할당(prefix increment operator)
    result = ++x;
    console.log(result, x); //7 7

    //선할당 후감소(postfix decrement operator)
    result = x--;
    console.log(result, x); //7 6

    //선감소 후할당(prefix decrement operator)
    result = --x;
    console.log(result, x); //5 5

    ```
    
- __`+`, `-` 단항 연산자__
  - __둘 다 부수 효과는 없다. 피연산자를 변경하는 연산을 하지 않고__ ___숫자 타입으로 변환___ __한 값을 새로 생성해 반환한다.__
  - `-`의 경우 피연산자의 부호를 반전한 값을 반환, `+`는 피연산자에 어떠한 효과도 주지 않는다.
    
      ```javascript
    var x = '1'; //현재 문자열인 상황

    console.log(+x); //1 출력. 문자열을 숫자로 타입 변환
      console.log(-x); //-1
      console.log(x); // "1". 부수 효과 없음

    x = true;
    console.log(+x); // 1
      console.log(-x); //-1
      console.log(x); //true. 부수 효과 없음

      x = false;
      console.log(+x); //0
      console.log(x); //false. 부수 효과 없음

      x = 'Hello';
      console.log(+x); //NaN. 문자열은 숫자로 타입 변환할 수 없으므로 NaN 반환. 
      console.log(x); //부수 효과 없음.

    ```

__문자열 연결 연산자__ (`+`)
- _`+` 연산자는 피연산자 중 하나 이상이 문자열인 경우 문자열 연결 연산자로 동작해 문자열을 연결하는 연산을 한다._
  
  ```javascript
  //문자열 연결 연산자
  '1' + 2; // '12'
  1 + '2'; //'12'

  //산술 연산자
  1 + 2; //3

  //true는 1로 타입 변환, false는 0으로 타입 변환
  1 + true; //2
  1 + false; //1

  //null은 0으로 타입 변환
  1 + null; // 1

  //undefined는 숫자로 타입 변환 안됨
  +undefined; //NaN
  1 + undefined; //NaN  
  ```

 📌 __암묵적 타입 변환(implicit coercion), 타입 강제 변환(type coercion)__
  - 개발자의 의도와 상관없이 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환되는 것.
  - `+, -` 단항 연산자와 `문자열 연결 연산자`가 포함됨
  

### 2) 할당 연산자(`=, +=, =+, *=, /+, %=`)

- 우항에 있는 피연산자의 평가 결과를 좌항에 있는 변수에 할당한다.
- __좌항의 변수에 값을 할당하므로 변수 값이 변하는 부수 효과가 있다.__
- _할당문은 값으로 평가되는 표현식인 문으로서 할당된 값으로 평가되기 때문에 다른 변수에 할당하는 연쇄 할당이 가능하다._

  ```javascript
  var a, b, c;

  //연쇄 할당. 오른쪽에서 왼쪽으로 진행.
  //1) c = 0 : 0으로 평가
  //2) b = 0 : 0으로 평가
  //3) a = 0 : 0으로 평가
  a = b = c = 0;

  console.log(a, b, c); //0 0 0
  ```

### 3) 비교 연산자

좌항과 우항의 피연산자를 비교한 다음 그 결과를 __불리언 값__ 으로 반환한다. 이는 if문이나 for문과 같은 제어문의 조건식에서 많이 활용된다.

__동등/일치 비교 연산자__ (`==, ===, !=, !==`)

- 좌항과 우항의 피연산자가 같은 값으로 평가되는지 비교해 불리언 값을 반환한다.
- 동등 비교 연산자는 _느슨한 비교_ 를 하지만 일치 비교 연산자는 _타입까지 고려하는 엄격한 비교를 한다._

- _1. 동등 비교 연산자_ (`==, !=`)
  - 좌항과 우항의 피연산자를 비교할 때 먼저 __암묵적 타입 변환__ 을 통해 타입을 일치시킨 후 같은 값인지 비교한다.
    
    ```javascript
    //동등 비교
    5 == 5; //true
  
    //타입은 다르지만 암묵적 타입 변환을 통해 타입을 일치시키면 동등함
    5 == '5'; //true
    ```
  
- _2. 일치 비교 연산자_ (`===, !==`)
  - 좌항과 우항의 피연산자가 타입도 같고 값도 같은 경우에 동등하다고 판단한다.
    
     ```javascript
    //동등 비교
    5 === 5; //true
  
    //값은 같지만 타입이 다르므로 false 반환
    5 === '5'; //false
    ```
   
  - `NaN`은 자신과 일치하지 않는 유일한 값이다. 특정 숫자가 NaN이 조사하기 위해서는 빌트인 함수 `Number.isNaN`을 사용한다.
    
    ```javascript
    NaN === NaN; //false

    //Number.isNaN 함수는 지정한 값이 NaN인지 확인하고 그 결과를 불리언 값으로 반환한다.
    Number.isNaN(NaN); //true
    Number.isNaN(10); //false
    Number.isNaN(1 + undefined); //true
    ```
  - 숫자 0은 양의 0(`+0`)과 음의 0(`-0`)이 있는데 이를 비교하면 true를 반환한다.
  
  📌 __Object.is 메서드__
  - ES6에 도입된 메서드로, 양의 0과 음의 0을 다른 값이라고 판단하고, NaN들은 서로 같은 값이라고 판단한다.
  - 이 외에는 일반적인 일치 비교 연산자와 동일하게 작동한다.
    
     ```javascript
    -0 === +0; //true
    Object.is(-0, +0); //false
   
    NaN === NaN; //false
    Object.is(NaN, NaN); //true
    ```

__대소 관계 비교 연산자__ (`<, >, >=, <=`)

피연산자의 크기를 비교하여 불리언 값을 반환한다. 부수 효과는 없다!
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/f847b9c7-bb15-476d-a843-8c8071cb5563)


### 4) 삼항 조건 연산자

조건식의 평가 결과에 따라 반환할 값을 결정한다. JS의 유일한 삼항 연산자이며, 부수 효과는 없다.

> __조건식 ? 조건식이 true일 때 반환할 값 : 조건식이 false일 때 반환할 값__

조건식은 불리언 타입의 값으로 평가될 표현식이기 때문에 평가 결과가 불리언 값이 아니더라도 불리언 값으로 암묵적 타입 변환이 된다.
삼항 조건 연산자의 첫 번째 피연산자는 조건식이므로 삼항 조건 연산자 표현식은 __값으로 평가할 수 있는 표현식인 조건문이다.__ 이때, 이는 `if ... else`문과 유사하게 처리되는데, 이는 값으로 처리될 수 없다. 

### 5) 논리 연산자(`||, &&, !`)

우항과 좌항의 피연산자(부정 논리 연산자의 경우 우항의 피연산자)를 논리 연산한다. 부수 효과는 없다. 

```javascript
// 논리합(||) 연산자. true는 1, false는 0
true || true; // -> true
true || false; // -> true
false || true; // -> true
false || false; // -> false

// 논리곱(&&) 연산자
true && true; // -> true
true && false; // -> false
false && true; // -> false
false && false; // -> false

// 논리 부정(!) 연산자 
!true; // -> false
!false; // -> true
```

- 논리 부정(`!`) 연산자는 언제나 불리언 값을 반환한다. 이떄, 피연산자가 불리언 값이 아니면 암묵적 타입 변환이 된다.
  
   ```javascript
   //암묵적 타입 변환  
   !0; //true
   !'Hello'; //false
   ```
- 논리합(`||`), 논리곱(`&&`) 연산자 표현식의 평가 결과는 불리언 값이 아닐 수 있다. 이 두 표현식은 언제나 2개의 피연산자 중 한 쪽으로 평가된다.
  
   ```javascript
   //단축 평가
   'Cat' && 'Dog'; //Dog
   ```

### 6) 쉼표 연산자(`,`)

왼쪽 피연산자부터 차례대로 피연산자를 평가하고 마지막 피연산자의 평가가 끝나면 마지막 피연산자의 평가 결과를 반환한다.

```javascript
var x, y, z;

x = 1, y = 2, z = 3; //3
```

### 7) 그룹 연산자(`()`)

자신의 피연산자인 표현식을 가장 먼저 평가한다. 그룹 연산자를 사용할 경우 연산자의 우선순위를 가장 높게 설정할 수 있다.

```javascript
10 * 2 + 3; //23

10 * (2 + 3); //50. 그룹 연산자 안에 값들을 먼저 평가. 
```

### 8) typeof 연산자

피연산자의 데이터 타입을 문자열로 반환한다. 7가지 __문자열__ `string, number, boolean, undefined, symbol, object, function` 중에 하나의 값을 리턴하게 된다.

```javascript
typeof ''; // -> "string"
typeof 1; // -> "number"
typeof NaN; // -> "number"
typeof true; // -> "boolean"
typeof undefined; // -> "undefined"
typeof Symbol(); // -> "symbol"
typeof null; // -> "object". JS의 첫번째 버그!
typeof []; // -> "object"
typeof {}; // -> "object"
typeof new Date(); // -> "object"
typeof /test/gi; // -> "object"
typeof function () {}; // -> "function"

//null인지 아닌지 확인하고 싶다면 일치 연산자(===)를 사용한다. 이때 리턴되는 값들은 불리언임을 알고 있자. 
var foo = null;

typeof foo === null; //false
foo === null; //true

// 선언하지 않은 식별자를 typeof로 연산해보면 ReferenceError가 발생하지 않고 undefined가 나온다.
typeof undeclared; // -> undefined
```

### 9) 지수 연산자

ES7에서 도입된 지수 연산자는 좌항의 피연산자를 밑으로, 우항의 피연산자를 지수로 거듭 제곱하여 숫자 값을 반환한다.
이전에는 `Math.pow` 메서드를 활용했지만, 이제는 가독성이 더 좋은 지수 연산자를 사용할 수 있다. 

```javascript
//지수 연산자
2 ** 2; //4
2 ** 2.5; //5.65685424...
2 ** 0; //1
2 ** -2; //0.25

//Math.pow
Math.pow(2, 2); //4
Math.pow(2, 0); //1
Math.pow(2, -2); //0.25

//음수의 거듭제곱도 가능하다. 단, 반드시 괄호로 묶어줘야 한다.
-5 ** 2;
// SyntaxError: Unary operator used immediately before exponentiation expression.
// Parenthesis must be used to disambiguate operator precedence

(-5) ** 2; // -> 25
```

### 10) 그 외 연산자
![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/f4fdf203-d02b-4a0a-b418-ed9cf5345410)

### 11) 연산자의 부수 효과 (`=, ++, --, delete`)

다른 코드에 영향을 주는 부수 효과가 있는 연산자들이 있다. 할당 연산자, 증감 연산자, delete 연산자가 있다. 

```javascript
var x;

// 할당연산자 - 변수 값이 변함
x = 1;
console.log(x); // 1

// 증가/감소 연산자 - 피연산자의 값이 재할당되어 변경
x++;
console.log(x); // 2

// delete 연산자 - 객체의 프로퍼티를 삭제! 객체 o를 사용하는 다른 코드에 영향을 준다.
var o = {a : 1};
delete o.a;
console.log(o); // {}
```
### 12) 연산자 우선 순위

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/314aed9b-5092-49cc-82c0-4f9ba162eac2)






