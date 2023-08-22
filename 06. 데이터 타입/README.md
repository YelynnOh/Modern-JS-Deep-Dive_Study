## 6. 데이터 타입

자바스크립트(ES6)은 7개의 데이터 타입을 제공한다. 크게 __원시 타입(primitive type)__ 과 __객체 타입(object/reference type)__ 으로 나뉜다.

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style1">
<tbody>
<tr>
<td style="width: 33.3333%; text-align: center;">구분</td>
<td style="width: 33.3333%; text-align: center;">데이터 타입</td>
<td style="width: 33.3333%; text-align: center;">설명</td>
</tr>
<tr>
<td style="width: 33.3333%; text-align: center;" rowspan="6">원시 타입</td>
<td style="width: 33.3333%; text-align: center;">숫자(number)</td>
<td style="width: 33.3333%; text-align: center;">숫자. 정수/실수 구분 없이 하나의 숫자 타입만 존재</td>
</tr>
<tr>
<td style="width: 33.3333%; text-align: center;">문자열(string)</td>
<td style="width: 33.3333%; text-align: center;">문자열</td>
</tr>
<tr>
<td style="width: 33.3333%; text-align: center;">불리언(boolean)</td>
<td style="width: 33.3333%; text-align: center;">true or false</td>
</tr>
<tr>
<td style="width: 33.3333%; text-align: center;">undefined</td>
<td style="width: 33.3333%; text-align: center;">var 키워드로 선언된 변수에 암묵적으로 할당되는 값</td>
</tr>
<tr>
<td style="width: 33.3333%; text-align: center;">null</td>
<td style="width: 33.3333%; text-align: center;">값이 없다는 것을 의도적으로 명시할 때 사용하는 값</td>
</tr>
<tr>
<td style="width: 33.3333%; text-align: center;">심벌(symbol)</td>
<td style="width: 33.3333%; text-align: center;">ES6에 추가된 새로운 타입</td>
</tr>
<tr>
<td style="width: 66.6666%; text-align: center;" colspan="2">객체 타입</td>
<td style="width: 33.3333%; text-align: center;">객체, 함수, 배열 등</td>
</tr>
</tbody>
</table>

### 1) 숫자 타입

_자바스크립트는 독특하게 하나의 숫자 타입 `number`만 존재한다. 모든 수를 64비트 부동소수점 형식, 즉 실수로 처리한다._

때문에 `console.log(1 === 1.0)`의 결과값은 `true`이다. 

_👀 숫자 타입에서 추가적으로 표현할 수 있는 세 가지 특별한 값_
- Infinity: 양의 무한대 
- -Inifinity: 음의 무한대
- NaN: 산술 연산 불가(not-a-number)


### 2) 문자열 타입

_0개 이상의 16비트 유니코드 문자(UTF-16)의 집합으로 거의 전 세계 대부분의 문자를 표현한다_

👀 문자열 타입을 나타내는 방식은 작은따옴표(' '), 큰따옴표(" "), 벡틱(``)이 있다. 벡틱은 ES6부터 추가가 되었다고 한다.

어떤 방식으로든 문자열을 따옴표로 감싸는 이유는 키워드나 식별자와 같은 토큰과 구분을 해주기 위해서이다. 

JS에서의 문자열은 원시 타입으로, 변경 불가능한 값(immutable value)이다. <br>

--- 

📌 __템플릿 리터럴(template literal)__
- ES6에 추가된 방식으로, __벡틱__ 을 활용하는 방식이다.
- _멀티라인 문자열(multi-line string), 표현식 삽입(expression interpolation), 태그드 템플릿(tagged template)_ 과 같은 문자열 처리 기능을 제공한다.
- 런타임에 일반 문자열로 변환되어 처리된다.

1️⃣ 멀티라인 문자열

일반 문자열 내에서는 줄바꿈(개행)이 허용되지 않는다. 만약 줄바꿈을 하고 싶다면 백슬래쉬(\ )를 포함한 _이스케이프 시퀀스_ 를 활용해야 한다. 그래서 작은따옴표나 큰따옴표를 활용하는 경우, 번거로움이 있었다. 하지만 벡틱을 사용한다면 모든 공백이 있는 그대로 적용이 되고 이스케이프 시퀀스를 활용하지 않아도 된다!

```javascript
var template = '<ul>\n\t<li>HI</li>\n</ul>';

var template = `<ul>
	<li>HI</li>
</ul>`

/*
콘솔을 찍어보면 둘 다
<ul>
	<li>HI</li>
</ul> 가 나온다. 벡틱이 훨씬 직관적이고 수월한 걸 볼 수 있다!
*/
```

2️⃣ 표현식 삽입

문자열은 문자열 연산자 '+'를 사용해 연결할 수 있다. '+' 연산자의 경우 피연산자 중 하나 이상이 문자열이 경우 문자열 연결 연산자로 동작하고, 그 외의 경우 덧셈 연산자로 동작한다. 

템플릿 리터럴 내에서는 표현식 삽입을 통해 간단히 문자열을 삽입할 수 있는데, 이는 문자열 연산자보다 가독성이 좋고 간편하다. `${}`꼴로 표현식을 감싸서 삽입해주면 된다.

```javascript
var first = 'Lynn';
var last = 'Oh';

//ES6 표현식 삽입
console.log(`My Name is ${first} ${last}.`); //My Name is Lynn Oh
```

표현식 삽입에서 _1) 표현식의 평가 결과가 문자열이 아니더라도 문자열로 타입이 강제로 변환되어 삽입된다._ 또한 이는 _2) 반드시 템플릿 리터럴 내에서 사용해야 한다._ 그렇지 않으면 `${}`가 그저 문자열로 출력되게 된다.


### 3) 불리언 타입

논리적 참, 거짓을 나타내는 true와 false가 있으며, 참/거짓의 조건으로 프로그램의 흐름을 제어하는 조건문에서 자주 사용한다.

### 4) undefined

```javascript
var foo;
console.log(foo);

//undefined
```
_`undefined`는 개발자가 의도적으로 할당하는 값이 아니라 자바스크립트 엔진이 변수를 초기화할 때 사용이 되는 값_ 이다. 즉, 변수가 선언까지만 되었고 값이 할당되지 않은 상태일 때만 적용되는 값인 것이다. 

만약 변수에 값이 없다는 것을 명시하고 싶을 때는 `null`을 할당하면 된다. 

### 5) null 타입

_`null`을 할당하는 것은 변수가 이전에 참조하던 값을 더 이상 참조하지 않겠다_ 는 의미이다. 이는 이전에 할당되어 있던 값에 대한 참조를 명시적으로 제거하는 것을 의미하며, 자바스크립트 엔진은 누구도 참조하지 않는 메모리 공간에 대해 _가비지 콜렉션_ 을 수행한다. 

`null`을 선언한다는 것은 변수의 스코프를 줄인다는 말과 동일하다. 

단, 함수의 경우 유효한 값을 반환할 수 없을 때 명시적으로 `null`을 반환하기도 한다.


### 6) 심벌 타입(ES6 추가)

___변경 불가능한 원시 타입의 값, 다른 값과 중복되지 않는 유일무이한 값___

따라서 주로 이름이 충돌할 위험이 없는 객체의 유일한 프로퍼티 키를 만들 때 사용한다. 심벌 값을 생성할 때는 _Symbol_ 함수를 호출한다.

```javascript
//심벌 값 생성
var key = Symbol('key');
console.log(typeof key); //symbol

//객체 생성
var obj = {};

//이름이 충돌할 위험이 없는 유일무이한 값인 심벌을 프로퍼티 키로 사용한다.
obj[key] = 'value';
console.log(obj[key]); //value
```

### 7) 데이터 타입의 필요성
- 값을 저장할 때 확보하는 메모리 공간의 크기를 결정하기 위해 필요하다.
- 값을 참조할 때 한번에 읽어들여야 할 메모리 공간의 크기를 결정하기 위함이다.
- 메모리에서 읽어들인 2진수를 해석할 방법을 결정하기 때문에 필요하다. 

### 8) 동적 타이핑

📌 _동적 타입 언어(dynamic/weak type) vs 정적 타입 언어(static/strong type)_

__정적 타입 언어__ 는 변수 선언 시 변수의 데이터 타입을 사전에 선언해야 한다.(명시적 타입 선언, explicit type declaration) `char c`, `int num`처럼. 이는 _1) 변수의 타입을 변경할 수 없으며 2) 변수에 선언한 타입에 맞는 값만 할당할 수 있다._ 실제로 컴파일 시 타입 체크를 통해 타입에 맞는 값이 할당되었는지 체크하고, 아닐 시 에러를 발생시킨다.

이는 타입의 일관성을 강제함으로서 안정적인 코드 구현을 통한 런타임 에러를 줄일 수 있다. 대표적으로 C언어, Java, 코틀린, 고, 하스켈, 러스트, 스칼라가 있다.

이에 반해 JS와 같은 __동적 타입 언어__ 에서 변수를 선언할 때는 타입을 선언하지 않는다. 다만 var, let, const 키워드를 사용해 변수를 선언할 뿐이다. 자바스크립트의 변수는 정적 타입 언어와 같이 미리 선언한 데이터 타입의 값만 할당할 수 있는 것이 아니라 어떠한 데이터 타입의 값이라도 자유롭게 할당할 수 있다.

즉, 자바스크립트에서는 _값을 할당하는 시점_ 에 변수 타입이 동적으로 결정되고 변수 타입을 언제든지 자유롭게 변경할 수 있다.

👉 ___따라서 JS의 변수는 선언이 아니라 '할당'에 의해 타입이 결정, 즉, 타입 추론이 된다. 그리고 재할당에 의해 변수 타입은 언제든지 동적으로 변한다(동적 타이핑, dynamic typing)___

대표적으로 JS, 파이썬, PHP, 루비, 리스프, 펄이 있다.

동적 타입 언어는 <br>
👍 유연성은 높지만 <br>
👎 그만큼 신뢰성은 낮다.

때문에 동적 타입 언어를 사용할 때 주의할 사항은

- 변수는 꼭 필요한 경우 제한적으로 사용하기
- 변수의 유효 범위(스코프)는 최대한 좁게 만들어 혹시 모를 부작용 억제하기
- 전역 변수 자제하기. 프로그램의 복잡성을 증가시키고 처리 흐름을 추적하기 어렵게 만든다.
- 변수보다는 상수를 사용해 값의 변경을 억제한다. `const` 사용하기!
- 변수 네이밍은 가독성이 좋게 명명하자.
