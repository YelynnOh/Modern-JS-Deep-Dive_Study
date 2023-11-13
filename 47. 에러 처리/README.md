# 47. 에러 처리

## 1. 에러 처리의 필요성

__우리는 언제나 에러나 예외적인 상호아이 발생할 수 있다는 것을 전제하고 이에 대응하는 코드를 작성하는 것이 중요하다.__

## 2. try catch finally 문

📌 __에러 처리를 구현하는 두 가지 방식__ <br>
    
  1. 예외적인 상황이 발생하면 반환하는 값(null 또는 -1)을 if 문이나 단축 평가 혹은 옵셔널 체이닝 연산자를 통해 확인해서 처리하는 방법 (ex. querySelector, Array#find) 
  2. 에러 처리 코드를 미리 등록하고 에러 발생 시 에러 처리 코드로 점프하는 방법 (ex. try..catch..finally 문)

- try catch finally 문에서는 finally 문은 불필요하다면 생략이 가능하며, catch도 생략이 되지만 생략 시 의미가 없으므로 생략하지 않음

```js
try{
  // 실행할 코드(에러가 발생할 가능성이 있는 코드)
} catch (err) {
  // try 코드 블록에서 에러가 발생하면 이 코드 블록의 코드가 실행된다.
  // err에는 try 코드 블록에서 발생한 Error 객체가 전달된다.
} finally {
  // 에러 발생과 상관없이 반드시 한 번 실행된다.  
}

```

- 코드의 흐름
1. try 코드 블록 실행
2. try 코드 블록에 포함된 문 중 에러 발생 시 발생한 에러는 catch문의 err 변수에 전달되고 catch 코드 블록 실행
※ catch 문에 err 변수는 try 코드 블록에 포함된 문 중 에러 발생 시 생성 + catch 코드 블록에서만 유효
3. finally 코드 블록은 에러 발생과 상관없이 반드시 한 번 실행

try catch finally 활용 시 프로그램이 강제 종료되지 않음

## 3. Error 객체

- Error 생성자 함수로 생성하며, 에러를 상세히 설명하는 에러 메시지를 인수로 전달 가능

```js
const error = new Error('invalid');
```

- 생성된 에러 객체는 `message` 프로퍼티와 `stack` 프로퍼티를 가짐
    - `message` 프로퍼티: Error 생성자 함수에 인수로 전달한 에러 메시지
    - `stack` 프로퍼티 값: 에러를 발생시킨 콜 스택의 호출 정보를 나타내는 문자열이며 디버깅 목적으로 사용

- Error 생성자 함수 뿐만 아니라 추가적으로 7가지의 에러 객체 생성 Error 생성자 함수가 제공됨 (ex. ``Error, SyntaxError, ReferenceError, TypeError, RangeError`` ....)
- 해당 생성자 함수가 생성한 에러 객체의 프로토타입은 모두 `Error.prototype`을 상속받음

## 4. throw 문

__에러 객체 생성과 에러 발생은 의미가 다르다.__

Error 생성자 함수로 에러 객체를 생성한다고 해서 에러가 발생하는 것은 아님. _에러를 발생시키려면 try 코드 블록에서 throw문으로 에러 객체를 던져야 함!_

```js
throw 표현식;
```

- 표현식에는 어떤 값도 상관없지만 보통 에러 객체를 지정함. 에러를 던지면 catch 문의 에러 변수가 생성되고 에러 객체가 할당된다. 그리고 catch 코드 블록이 실행됨. 

```js
try {
  // 에러 객체를 던지면 catch 코드 블록이 실행되기 시작한다.
  throw new Error('something wrong');
} catch (error) {
  console.log(error); // Error: something wrong
}
```

## 5. 에러의 전파

에러는 호출자 방향으로 전파된다. __즉, 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파된다.__

```js
const foo = () => {
  throw Error('foo에서 발생한 에러'); // (4)
}

const bar = () => {
  foo(); // (3)
}

const baz = () => {
  bar(); //(2)
}

try {
  baz() // (1)
} catch (err) {
  console.error(err);
}
```
👉 (1)에서 `baz` 함수 호출 시 (2)에서 `bar` 함수 호출되고 (3)의 `foo` 함수 호출되고 `foo` 함수는 (4)에서 에러를 `throw`함

👉 `throw` 된 에러는 호출자에게 전파되어 전역에서 캐치됨

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/a7aa2787-bd43-4e71-8aca-3db3a94a9b07)


`throw` 된 에러를 캐치하여 적절히 대응하면 프로그램을 강제 종료시키지 않고 코드의 실행 흐름을 복구할 수 있다. 하지만 `throw` 된 에러를 어디에서도 캐치하지 않으면 프로그램은 강제 종료된다!
