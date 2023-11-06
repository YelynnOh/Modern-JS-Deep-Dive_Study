# 41. 타이머


## 1. 호출 스케줄링

**호출 스케쥴링이란?**

함수를 명시적으로 호출하지 않고 일정 시간이 경과된 이후에 호출되록 함수 호출을 예약하기 위해 타이머 함수를 활용하는 것

JS에서는 타이머를 생성하거나 제거할 수 있는 함수로 `setTimeOut`, `setInterval`, `clearTimeout`, `clearInterval` 함수를 제공한다.

타이머 함수는 호스트 객체다. 

`setTimeout` 함수가 생성한 타이머는 단 __한 번만 동작__ 하고, `setInterval` 함수가 생성한 타이머는 __반복 동작__ 한다.

JS 엔진은 하나의 실행 컨텍스트를 갖기 때문에 두 가지 이상의 태스크를 동시에 실행할 수 없다. 즉, JS 엔진은 __싱글 스레드__ 로 동작한다. 때문에 타이머 함수 `setTimeout`과 `setInterval`은 _비동기 처리 방식_ 으로 동작한다.

## 2. 타이머 함수

### `setTimeout`, `clearTimeout`

1) `setTimeout`

`const timeoutId = setTimeout(func|code[, delay, param1, param2, ...]);` 

| 매개변수 | 설명 |
| --- | --- |
| func | 타이머가 만료된 뒤 호출될 콜백 함수   \* 콜백 함수 대신 코드를 문자열로 전달 가능. 이때 코드 문자열은 타이머가 만료된 뒤 해석되고 실행됨. 이는 \`eval\` 함수와 유사하지만 권장 X |
| delay | 타이머 만료 시간(ms). 생략이 가능하며, 생략 시 디폴트는 0으로 생성됨   \* delay 시간이 설정된 타이머가 만료되면 콜백 함수가 즉시 호출되는 것이 보장 X. delay 시간은 태스크 큐에 콜백 함수를 등록하는 시간을 지연할 뿐이다.   \* delay가 4ms 이하인 경우 최소 지연 시간 4ms가 지정됨 |
| param1, param2, ... | 호출 스케줄링된 콜백 함수에 전달해야 할 인수가 존재하는 경우 세 번째 이후의 인수로 전달 가능 |

```js
// 1초(1000ms) 후 타이머가 만료되면 콜백 함수가 호출된다.
setTimeout(() => console.log('Hi!'), 1000);

// 1초(1000ms) 후 타이머가 만료되면 콜백 함수가 호출된다.
// 이때 콜백 함수에 'Lee'가 인수로 전달된다.
setTimeout(name => console.log(`Hi! ${name}.`), 1000, 'Lee');

// 두 번째 인수(delay)를 생략하면 기본값 0이 지정된다.
setTimeout(() => console.log('Hello!'));

```

2) `clearTimeout`

`setTimeout`의 return 값 (반환값)은 __고유한 타이머 ID__ 이다. 해당 타이머 ID는 `clearTimeout`의 인수로 전달하여 타이머를 취소할 수 있다.

```js
//setTimeout 함수가 반환한 타이머 id를 clearTimeout 함수의 인수로 전달해 타이머 취소
//타이머가 취소될 경우 setTimeout 함수의 콜백 함수가 실행되지 않음
clearTimeout(timerId);
```

### `setInterval`, `clearInterval`

`setInterval`도 `setTimeout`과 마찬가지 코드 형태로 생성할 수 있다. 다만 위에서와의 차이는 delay마다 반복적으로 실행이 된다는 점이다. 또, 타이머가 취소되면 `setInterval` 함수의 콜백 함수는 실행되지 않는다.

```js
let count = 1;

// 1초(1000ms) 후 타이머가 만료될 때마다 콜백 함수가 호출된다.
// setInterval 함수는 생성된 타이머를 식별할 수 있는 고유한 타이머 id를 반환한다.
const timeoutId = setInterval(() => {
  console.log(count); // 1 2 3 4 5
  // count가 5이면 setInterval 함수가 반환한 타이머 id를 clearInterval 함수의
  // 인수로 전달하여 타이머를 취소한다. 타이머가 취소되면 setInterval 함수의 콜백 함수가
  // 실행되지 않는다.
  if (count++ === 5) clearInterval(timeoutId);
}, 1000);

```

## 3. 디바운스와 스로틀

이벤트에 바인딩한 이벤트 핸들러는 과도하게 호출되어 성능에 문제를 일으킬 수 있다. (scroll, resize,input, mousemove 등과 같은 이벤트) <br><br>__디바운스와 스로틀은 짧은 시간 간격으로 연속해서 발생하는 이벤트를그룹화해서 과도한 이벤트 핸들러의 호출을 방지하는 프로그래밍 기법이다.__

### 디바운스

_디바운스는 짧은 시간 간격으로 이벤트가 연속해서 발생하면 이벤트 핸들러를 호출하지 않다가 일정 시간이 경과한 이후에 이벤트 핸들러가 __한 번만 호출되도록 한다.___

```html

<!DOCTYPE html>
<html>
<body>
  <input type="text">
  <div class="msg"></div>
  <script>
    const $input = document.querySelector('input');
    const $msg = document.querySelector('.msg');

    const debounce = (callback, delay) => {
      let timerId;
      // debounce 함수는 timerId를 기억하는 클로저를 반환한다.
      return (...args) => {
        // delay가 경과하기 이전에 이벤트가 발생하면 이전 타이머를 취소하고
        // 새로운 타이머를 재설정한다.
        // 따라서 delay보다 짧은 간격으로 이벤트가 발생하면 callback은 호출되지 않는다.
        if (timerId) clearTimeout(timerId);
        timerId = setTimeout(callback, delay, ...args);
      };
    };

    // debounce 함수가 반환하는 클로저가 이벤트 핸들러로 등록된다.
    // 300ms보다 짧은 간격으로 input 이벤트가 발생하면 debounce 함수의 콜백 함수는
    // 호출되지 않다가 300ms 동안 input 이벤트가 더 이상 발생하면 한 번만 호출된다.
    $input.oninput = debounce(e => {
      $msg.textContent = e.target.value;
    }, 300);
  </script>
</body>
</html>
```

여기서 `input` 이벤트는 사용자가 텍스트 입력 필드에 값을 입력할 때마다 연속해서 발생한다. 만약 `input` 이벤트 핸들러에서 사용자가 입력 필드에 입력한 값으로 매번 무거운 요청 (ex. Ajax 요청)을 해야 한다면 _불필요한 작업이 지속될 것이고 서버에도 부담이 될 것이다._

__여기서 디바운스 함수는 `두번째 인수로 전달된 시간(delay) > 이벤트발생 간격` 이면 이전 타이머를 취소하고 새로운 타이머를 재설정한다. 즉, 타이머를 갱신한다.__

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/54da9df2-3ace-428b-bb17-d2d137ac35e2)

디바운스는 _resize 이벤트 처리, input 요소에 입력된 값으로 ajax 요청하는 입력 필드 자동완성 UI 구현, 버튼 중복 클릭 방지 처리_ 에 유용하게 활용된다!

Underscore의 `debounce` 함수나 Lodash의 `debounce` 함수를 사용하는 것을 권장한다.

### 스로틀

_스로틀은 짧은 시간 간격으로 __이벤트가 연속해서 발생하더라도 일정 시간 간격으로 이벤트 핸들러가 최대 한 번만 호출되도록 한다.___

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    .container {
      width: 300px;
      height: 300px;
      background-color: rebeccapurple;
      overflow: scroll;
    }

    .content {
      width: 300px;
      height: 1000vh;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="content"></div>
  </div>
  <div>
    일반 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
    <span class="normal-count">0</span>
  </div>
  <div>
    스로틀 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
    <span class="throttle-count">0</span>
  </div>

  <script>
    const $container = document.querySelector('.container');
    const $normalCount = document.querySelector('.normal-count');
    const $throttleCount = document.querySelector('.throttle-count');

    const throttle = (callback, delay) => {
      let timerId;
      // throttle 함수는 timerId를 기억하는 클로저를 반환한다.
      return (...args) => {
        // delay가 경과하기 이전에 이벤트가 발생하면 아무것도 하지 않다가
        // delay가 경과했을 때 이벤트가 발생하면 새로운 타이머를 재설정한다.
        // 따라서 delay 간격으로 callback이 호출된다.
        if (timerId) return;
        timerId = setTimeout(() => {
          callback(...args);
          timerId = null;
        }, delay);
      };
    };

    let normalCount = 0;
    $container.addEventListener('scroll', () => {
      $normalCount.textContent = ++normalCount;
    });

    let throttleCount = 0;
    // throttle 함수가 반환하는 클로저가 이벤트 핸들러로 등록된다.
    $container.addEventListener('scroll', throttle(() => {
      $throttleCount.textContent = ++throttleCount;
    }, 100));
  </script>
</body>
</html>

```

여기서 scroll 이벤트는 짧은 시간 간격으로 연속해서 발생하는 이벤트이다. _과도한 이벤트 핸들러의 호출을 방지하기 위해 `throttle` 함수는 이벤트를 그룹화해서 일정 시간 단위로 이벤트 핸들러가 호출되도록 호출주기를 만든다._

__`throttle` 함수에 두 번째 인수로 전달한 시간(delay) 경과 전에 이벤트 발생 시 아무것도 하지 않다가 delay 경과 후 콜백 함수를 호출하고 새로운 타이머를 재설정한다. __즉, 이벤트가 발생함에 따라 delay가 갱신되지는 않는다.___

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/a976cab9-1fd2-488c-927c-911296941051)


scroll 이벤트 처리나 무한 스크롤 UI 구현 등에 유용하게 사용된다.

Underscore의 `throttle` 함수나 Lodash의 `throttle` 함수 사용이 권장된다.
