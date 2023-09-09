# 20. strict mode

## 1. strict mode 란?

__strict mode란 JS 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 JS 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시키는 모드이다.__

비슷한 기능은 Eslint도 한다.

```js
function foo() {
  x = 10;
}
foo();

console.log(x); // 10
```

위 예제를 실행하면 결과는 10이 출력된다. `x = 10` 코드를 만나면 자바스크립트 엔진은 `foo` 함수 스코프에서 x 식별자를 검색한다.
foo 함수 스코프에 x 선언이 없이 때문에 스코프 체인을 통해 상위 스코프에서 x 변수를 검색한다. 
전역 스코프에도 x 변수의 선언은 없기 때문에 ReferenceError를 발생시킬 것 같지만 JS 엔진은 암묵적으로 전역 객체에 x 프로퍼티를 동적 생성한다.

이처럼 전역 객체의 x 프로퍼티는 마치 전역 변수처럼 활동을 하는데, 이를 __암묵적 전역(implicit global)__ 이라고 한다. 

이런 현상은 개발자의 의도와 다르게 동작할 우려가 있어 좋지 않다.
따라서 반드시 var, let, const키워드를 사용하여 변수를 선언한 다음 사용해야 하지만, 애초에 개발 환경에서부터 제한을 주기 위해 나타난 것이 strict mode이다.

## 2. strict mode의 적용

_전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가한다._

전역 선두에 추가 시 스크립트 전체에 strict mode가 적용이 된다.
함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 strict mode가 적용된다.

```js
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```
```js
function foo() {
  'use strict';

  x = 10; // ReferenceError: x is not defined
}
foo();
```

## 3. 전역에 strict mode를 적용하는 것은 피하자

strict mode는 스크립트 단위로 적용된다. 때문에 여러 strict mode 스크립트와 non-strict mode 스크립트가 혼용이 될 수도 있는데, 이는 오류의 원인이 된다.

외부 서드파티 라이브러리를 사용할 경우 라이브러리가 non-strict mode인 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않다. 이런 경우 즉시 실행 함수로 스크립트 전체를 감싸 슼코프를 구분하고 즉시 실행 함수 선두에 모드를 적용한다.

```js
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  'use strict';

  // Do something...
}());
```

## 4. 함수 단위로 strict mode를 적용하는 것도 피하자

- 역시 strict mode와 아닌 것을 혼용하는 것은 바람직하지 않다
- 모든 함수에 일일이 모드를 지정하는 것은 번거롭다
- strict mode가 적용된 함수가 참조할 함수 외부 컨텍스트에 strict mode가 적용되지 않는다면 에러 발생 가능하다.

역시 즉시 실행 함수로 감싼 스크립트 단위로 적용하자.

## 5. strict mode가 발생시키는 에러

### 1) 암묵적 전역

선언하지 않은 변수 참조시 ReferenceError 발생

### 2) 변수, 함수, 매개변수 삭제

delete 연산자로 변수, 함수, 매개변수 삭제 시 SyntaxError 발생한다.

```js
(function () {
  'use strict';

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
}());
```

### 3) 매개변수 이름 중복

중복된 매개변수 이름 사용 시 SyntaxError 발생한다.

### 4) with 문의 사용

with 문 사용 시 SyntaxError 발생한다.

with문은 전달된 객체를 스코프 체인에 추가하는데, 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어 코드가 간단해진다. 하지만 그만큼 성능과 가독성은 나빠지기에 사용을 지양하자.

```js
(function () {
  'use strict';

  // SyntaxError: Strict mode code may not include a with statement
  with({ x: 1 }) {
    console.log(x);
  }
}());
```

## 6. strict mode 적용에 의한 변화

### 1) 일반 함수의 this

strict mode에서 함수를 일반 함수로 호출 시 this에 undefined가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다. 이때 에러가 발생하지는 않는다.

```js
(function () {
  'use strict';

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
}());
```

strict mode가 아닐 경우 window 객체가 바인딩된다.

### 2) arguments 객체

strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```js
(function (a) {
  'use strict';
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
}(1));
```
