## 5. 표현식과 문 

### 1) 값

_식(표현식, expression)이 평가되어 생성된 결과이다._

`10 + 20`은 평가되어 숫자 값 `30`을 생성한다. 모든 값은 데이터 타입을 가진다. 값은 다양한 방법으로 생성할 수 있지만 그 중 가장 기본적인 방법은 리터럴을 사용하는 것이다.

### 2) 리터럴✨

_사람이 이해할 수 있는 문자 또는 약속된 기호를 사용해 값을 생성하는 표기법을 말한다._

JS에서 자바스크립트 엔진은 코드가 실행되는 시점인 런타임에 리터럴을 평가해 값을 생성한다. 즉, 리터럴은 값을 생성하기 위해 미리 약속한 표기법이라고 할 수 있다.

리터럴의 예로 정수 리터럴, 부동소수점 리터럴, 2진수 리터럴, 불리언 리터럴 등이 있다. 

### 3) 표현식

_값으로 평가될 수 있는 문(statement)이다. 즉, 표현식이 평가되면 새로운 값을 생성하거나 기존 값을 참조한다._

```javascript
// 리터럴 표현식
10
'Hello'

// 식별자 표현식(선언이 이미 존재한다고 가정)
sum
person.name
arr[1]

// 연산자 표현식
10 + 20
sum = 10
sum !== 10

// 함수/메서드 호출 표현식(선언이 이미 존재한다고 가정)
square()
person.getName()
```


### 4) 문

_프로그램을 구성하는 기본 단위이자 최소 실행 단위_

문은 여러 토큰(token)으로 구성된다.

>📌 토큰이란? <br> 문법적인 의미를 가지며, 문법적으로 더 이상 나눌 수 없는 코드의 기본 요소. `var sum = 1 + 2;`는 `var`부터 `;`까지 총 7개의 토큰으로 구성된다.

대표적으로 흔히 아는 조건문, 함수 선언문, 반복문부터 할당문, 변수 선언문 모두 문의 예시이다. 

### 5) 표현식인 문과 표현식이 아닌 문

_👀 표현식은 문의 일부일 수도 있고 그 자체로 문이 될 수도 있다. 그럼 표현식을 보고 어떻게 문인지 알 수 있을까?_

👉 표현식인 문과 표현식이 아닌 문을 구별하는 방법은 __변수에 할당해보는 것__ 이다.

표현식인 문은 값으로 평가되기 때문에 변수 할당이 가능하지만, 표현식이 아닌 문은 값으로 평가가 안되기 때문에 변수 할당 시 에러가 발생한다.


```javascript
var x; //변수 선언문
//이 친구의 경우 var foo = var x;(X) 이런식으로 할 수 없기 때문에 표현식이 아닌 문임

x = 100; //할당문
//할당문은 그 자체가 표현식이지만 이 자체로 완전한 문임. 따라서 표현식인 문.
// var foo = x = 100;, console.log(foo);도 올바른 코드!
```
