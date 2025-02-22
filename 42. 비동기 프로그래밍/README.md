# 42. 비동기 프로그래밍

## 1. 동기 처리와 비동기 처리

보통 함수의 실행 순서는

1) 함수 호출
2) 함수 코드 평가
3) 함수 실행 컨텍스트 생성
4) 생성된 함수 실행 컨텍스트는 실행 컨텍스트 스택(콜 스택)에 푸시
5) 함수 코드 실행
6) 실행 종료 시 함수 실행 컨텍스트가 스택에서 팝되어 제거 

__JS 엔진은 단 하나의 실행 컨텍스트 스택을 가진다. 이는 즉 동기에 2개 이상의 함수를 동시에 실행할 수 없다는 것을 의미한다.__ 

### 동기 처리

싱글 스레드 방식으로 동작하는 JS 엔진은 하나의 태스크만 실행할 수 있기 때문에 처리에 시간이 걸리는 태스크를 실행할 경우 __블로킹(blocking, 작업 중단)__ 현상이 발생한다. 아래 예시를 보자!

```js
// sleep 함수는 일정 시간(delay)이 경과한 이후에 콜백 함수(func)를 호출한다.
function sleep(func, delay) {
    // Date.now()는 현재 시간을 숫자(ms)로 반환한다.("30.2.1. Date.now" 참고)
    const delayUntil = Date.now() + delay;
  
    // 현재 시간(Date.now())에 delay를 더한 delayUntil이 현재 시간보다 작으면 계속 반복한다.
    while (Date.now() < delayUntil);
    // 일정 시간(delay)이 경과한 이후에 콜백 함수(func)를 호출한다.
    func();
}
  
function foo() {
    console.log('foo');
}
  
function bar() {
    console.log('bar');
}
  
// sleep 함수는 3초 이상 실행된다..
sleep(foo, 3 * 1000);
// bar 함수는 sleep 함수의 실행이 종료된 이후에 호출되므로 3초 이상 블로킹된다.
bar();
// (3초 경과 후) foo 호출 -> bar 호출

```

여기서 `sleep` 함수는 3초 뒤에 `foo` 함수를 호출한다. 이때 `bar` 함수는 `sleep` 함수 실행이 종료된 후 호출이 되므로 3초 이상(`foo`함수 실행 시간 + 3초) 호출되지 못하고 블로킹(작업 중단)된다.

`sleep` > `foo` > `bar` 순으로 실행

이처럼 __현재 실행 중인 태스크가 종료할 때까지 다음에 실행될 태스크가 대기하는 방식을 동기 처리라고 한다.__ 이는 _실행 순서가 보장된다는 장점_ 이 있지만 앞선 태스크가 종료될 때까지 이후 _태스크들이 블로킹되는 단점이 있다._


### 비동기 처리 

__현재 실행 중인 태스크가 종료되지 않은 상태라 해도 다음 태스크를 곧바로 실행하는 방식을 비동기 처리__ 라고 한다.

```js
function foo() {
    console.log('foo');
}

function bar() {
    console.log('bar');
}

// 타이머 함수 setTimeout은 일정 시간이 경과한 이후에 콜백 함수 foo를 호출한다.
// 타이머 함수 setTimeout은 bar 함수를 블로킹하지 않는다.
setTimeout(foo, 3 * 1000);
bar();
// bar 호출 -> (3초 경과 후) foo 호출
```

`setTimeout` 함수는 `sleep` 함수와 유사하게 일정 시간이 경과된 후에 콜백 함수를 호출하지만 `setTimeout` 함수 이후의 태스크를 블로킹하지 않고 곧바로 실행한다.

`setTimeout` > 바로 `bar` > 3초 있다가 `foo` 실행

_현재 실행 중인 태스크가 종료되지 않은 상태가 해도 다음 태스크를 곧바로 실행하므로 블로킹이 발생하지 않는다는 장점_ 이 있지만 _태스크 실행 순서가 보장되지 않는다._

비동기 처리를 수행하는 비동기 함수는 __콜백 패턴__ 을 사용한다. 이는 _콜백 헬(callback hell)을 유발해 가독성을 떨어트리고, 비동기 처리 중 발생한 에러의 예외 처리가 곤란하며, 여러 개의 비동기 처리를 한 번에 처리하는 데 한계가 있다._ 나중에 이와 관련해서 __프로미스__ 가 나온다고 한다! 

## 2. 이벤트 루프와 태스크 큐

우리는 브라우저가 동작하는 것을 보면 많은 태스크가 동시에 처리되는 것처럼 느껴진다. (ex. HTML 요소가 애니메이션 효과를 통해 움직이면서 이벤트 처리할때 / HTTP 요청을 통해 서버로부터 데이터를 가지고 오면서 렌더링할 때..)

이처럼 __JS의 동시성(concurrency)를 지원하는 것이 바로 이벤트 루프(event loop)이다.__

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/df83d8e3-76e7-4e69-8a3d-3ffc6ad426f8)



📌 __이벤트 루프와 브라우저 환경__

구글의 V8 JS 엔진을 비롯한 대부분의 JS 엔진은 크게 2개의 영역으로 구분한다.

- _콜 스택(call stack)_
    - 실행 컨텍스트가 추가되고  제거되는 스택 자료구조
    - 함수를 호출하면 함수 실행 컨텍스트가 순차적으로 콜 스택에 푸시되어 실행됨
    - JS 엔진은 하나의 콜 스택이 존재하므로 최상위 실행 컨텍스트(실행 중인 실행 컨텍스트)가 종료되어 콜 스택에서 제거되기 전까지 다른 어떤 태스크도 실행 X

- _힙(heap)_
    - 객체가 저장되는 메모리 공간
    - 콜 스태긔 요소인 실행 컨텍스트는 힙에 저장된 객체 참조
    - 객체를 저장할 때는 메모리 공간의 크기를 런타임에 결정(동적 할당)함. 따라서 힙은 구조화 되어 있지 않음. 

---


__비동기 처리에서 소스코드의 평가와 실행을 제외한 모든 처리는 JS 엔진을 구동하는 환경인 브라우저 OR Node.js가 담당한다.__

ex) `setTimeout`의 콜백 함수의 평가/실행은 JS 엔진이 담당하지만 호출 스케줄링을 위한 타이머 설정과 콜백 함수의 등록은 브라우저나 Node.js가 담당

---

📌 __브라우저 환경의 구성__

- _태스크 큐(task queue)_
    - `setTimeout` 아니면 `setInterval`과 같은 비동기 함수의 콜백 함수/이벤트 핸들러가 일시적으로 보관되는 영역
    - 태스크 큐와 별도로 프로미스 후속 처리 메서드의 콜백 함수가 일시적으로 보관되는 _마이크로태스크 큐_ 도 존재함
- _이벤트 루프(event loop)_
    - 콜 스택에 현재 실행 중인 실행 컨텍스트가 있는지, 태스크 큐에 대기 중에 함수(콜백 함수, 이벤트 핸들러)가 있는지 반복해서 확인
    - 콜 스택이 비어 있고 태스크 큐에 대기 중인 함수가 있으면 이벤트 루프는 _순차적으로(FIFO) 태스크 큐에 대기 중인 함수를 콜 스택으로 이동시킴_
    - 콜 스택으로 이동한 함수는 실행됨
    - 태스크 큐에 일시 보관한 함수들은 비동기 처리 방식으로 동작


---

```js
function foo() {
    console.log('foo');
}

function bar() {
    console.log('bar');
}

setTimeout(foo, 0); // 0초(실제는 4ms) 후에 foo 함수가 호출된다.
bar();

```
코드 실행 순서

1. 전역 코드가 평가되어 전역 실행 컨텍스트 생성 / 콜 스택에 푸시
2. 전역 코드가 실행되기 시작하여 `setTimeout` 함수 호출됨! 이때 `setTimeout` 함수의 함수 실행 컨텍스트가 생성되고 콜 스택에 푸시되어 현재 실행 중인 실행 컨텍스트가 됨. 브라우저이 Web API(호스트 객체)인 타이머 함수도 함수이므로 함수 실행 컨텍스트 생성.
3. `setTimeout` 함수가 실행되면 콜백 함수를 호출 스케줄링하고 종료되어 콜 스택에서 팝됨. 이때 호출 스케줄링, 즉, 타이머 설정과 타이머가 만료되면 콜백 함수를 태스크 큐에 푸시하는 것은 브라우저의 역할임
4. 아래 두 단계는 병행 처리됨
    - (__브라우저 실행__) 브라우저는 타이머를 설정하고 타이머의 만료 대기. 이후 타이머 만료 시 `foo`가 태스크 에 푸시됨. 여기서는 4ms 후에 `foo`가 태스크 큐에 푸시되어 대기함.
    (_`setTimeout` 함수로 호출 스케줄링한 콜백 함수는 정확히 지연 시간 후에 호출된다는 보장이 없다. 지연 시간 이후에 콜백 함수가 태스크 큐에 푸시되어 대기하지만 콜 스택이 비어야 호출되므로 약간의 시간차가 발생할 수 있기 때문!_)

    - `bar` 함수 호출되어 해당 함수의 함수 실행 컨텍스트 생성, 콜 스택에 푸시되어 현재 실행 중인 실행 컨텍스트가 됨. 이후 `bar` 함수 종료 시 콜 스택에서 팝됨. 이때 브라우저가 타이머를 설정한 후 4ms 경과했다면 `foo` 함수는 아직 태스크 큐에서 대기 중.

5. 전역 코드 실행 종료되고 전역 실행 컨텍스트가 콜 스택에서 팝됨. 콜 스택에는 아무런 실행 컨텍스트도 존재하지 않게 됨.
6. 이벤트 루프에 의해 콜 스택이 비어 있음이 감지되고 태스크 큐에서 대기 중인 콜백 함수 `foo`가 이벤트 루프에 의해 콜 스택에 푸시됨. 즉, 콜백 함수 `foo`의 함수 실행 컨텍스트가 생성되고 콜 스택에 푸시되어 현재 실행 중인 실행 컨텍스트가 됨. 이후 `foo` 함수가 종료되어 콜 스택에서 팝됨. 

👉 __비동기 함수인 `setTimeout`의 콜백 함수는 태스크 큐에 푸시되어 대기하다가 콜 스택이 비면 (전역 코드 및 명시적으로 호출된 함수가 모두 종료하면) 콜 스택에 푸시되어 실행됨__

결론적으로 __JS 엔진은 싱글 스레드로 동작하지만 브라우저는 멀티 스레드로 동작함!__
