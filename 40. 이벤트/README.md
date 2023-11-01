# 40. 이벤트

## 1. 이벤트 드리븐 프로그래밍

애플리케이션이 특정 타입의 이벤트에 대해 반응하여 어떤 일을 하고 싶다면 해당하는 타입의 이벤트가 발생했을 때 호출될 함수를 브라우저에게 알려 호출을 위임한다. 
이 때 이벤트가 발생했을 때 _호출된 함수를_ **이벤트 핸들러** 라고 하며, _이벤트 핸들러의 호출을 위입하는 것을_ **이벤트 핸들러 등록** 이라고 한다.

프로그램의 흐름을 이벤트 중심으로 제어하는 프로그래밍 방식을 **이벤트 드리븐 프로그래밍** 이라 한다. 

## 3. 이벤트 핸들러 등록

이벤트가 발생하면 브라우저에 의해 호출될 함수가 이벤트 핸들러다.
이벤트 핸들러를 등록하는 방법은 3가지다.

### 1) 이벤트 핸들러 어트리뷰트 방식

이벤트핸들러 어트리뷰트의 이름은 `onclick`과 같이 `on` 접두사와 이벤트의 종류를 나타내는 이벤트 타입으로 이뤄져 있다. 이벤트 핸들러 어트리뷰 값으로 함수 호출문 등의 `문(statement)`을 할당하면 이벤트 핸들러가 등록된다. 

```js
<!DOCTYPE html>
<html>
<body>
  <button onclick="sayHi('Lee')">Click me!</button>
  <script>
    function sayHi(name) {
      console.log(`Hi! ${name}.`);
    }
  </script>
</body>
</html>
```
위 예제에서는 이벤트 핸들러 어트리뷰트 값으로 함수 호출문을 할당했다. 
**이벤트 핸들러 어트리뷰트 값은 사실 암묵적으로 생성될 이벤트 핸들러의 함수 몸체를 의미한다.**

즉, `onclick="sayHi('Lee')"`는 파싱되어 다음과 같으 함수를 암묵적으로 생성하고 이벤트 핸들러 어트리뷰트 이름과 동일한 키 `onclick` 이벤트 핸들러 프로퍼티에 할당한다. 

```js
function onclick(event) {
  sayHi('Lee');
}
```

보통은 이 방식을 많이 사용하지는 않지만, CBD(Component Based Development) 방식의 앵귤러/리액트/Svelte/뷰.js 같은 프레임워크/라이브러리에서는 이벤트 핸들러 어트리뷰트 방식으로 이벤트를 처리한다. 

### 2) 이벤트 핸들러 프로퍼티 방식

`window` 객체와 `Document, HTMLElement` 타입의 DOM 노드 객체는 이벤트에 대응하는 이벤트 핸들러 프로퍼티를 가지고 있있다. 이벤트 핸들러 프로퍼티의 키는 이벤트 핸들러 어트리뷰트와 마찬가지로 `onclick`과 같이 `on` 접두사와 이벤트의 종류를 나타내는 이벤트 타입으로 이뤄져 있다.
이벤트 핸들러 프로퍼티에 함수를 바인딩하면 이벤트 핸들러가 등록된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    // 이벤트 핸들러 프로퍼티에 이벤트 핸들러를 바인딩
    $button.onclick = function () {
      console.log('button click');
    };
  </script>
</body>
</html>
```

여기서 이벤트 핸들러를 등록하기 위해서는 아래를 지정해줘야 한다.
- 이벤트 타깃 : 이벤트를 발생시킬 객체
- 이벤트 타입 : 이벤트의 종류를 나타내는 문자열
- 이벤트 핸들러

위의 코드에서의 경우로 예시를 들면 다음과 같이 이벤트 타깃, 이벤트 타입, 이벤트 핸들러를 표시할 수 있다. 

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/f4222599-0f4b-4882-9874-fa127fd76c3f)

이벤트 핸들러는 대부분 이벤트를 발생시킬 이벤트 타깃에 바인딩하지만 반드시 그렇지는 않다. 이벤트 핸들러는 이벤트 타깃 or 전파된 이벤트를 캐치할 DOM 노드 객체에 바인딩한다.

이벤트 핸들러 어트리뷰트 방식도 결국 DOM 노드 객체의 이벤트 핸들러 프로퍼티로 변환되기 때문에 이벤트 핸들러 프로퍼티 방식과 동일하다고 할 수 있다. 허나 이벤트 핸들러 프로퍼티 방식은 HTML과 JS가 뒤섞이는 문제를 해결할 수 있다. 그치만 이벤트 핸들러 프로퍼티에 하나의 이벤트 핸들러만 바인딩할 수 있다는 단점이 있기는 하다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    // 이벤트 핸들러 프로퍼티 방식은 하나의 이벤트에 하나의 이벤트 핸들러만을 바인딩할 수 있다.
    // 첫 번째로 바인딩된 이벤트 핸들러는 두 번째 바인딩된 이벤트 핸들러에 의해 재할당되어
    // 실행되지 않는다.
    $button.onclick = function () {
      console.log('Button clicked 1');
    };

    // 두 번째로 바인딩된 이벤트 핸들러
    $button.onclick = function () {
      console.log('Button clicked 2');
    };
  </script>
</body>
</html>
```

### 3) `addEventListener` 메서드 방식

DOM Level 2에서 도입된 `EventTarget.prototype.addEventListener` 메서드를 사용하여 이벤트 핸들러를 등록할 수 있다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/30f89895-1a63-4661-a6a5-6a3f8fa96a0c)

`addEventListener` 메서드 설명

- 첫번째 매개변수 > 이벤트 타입
  - 이벤트 종류를 나타낸다!
  - 이벤트 핸들러 프로퍼티 방식과는 다르게 `on` 접두사를 붙이지 않는다.
- 두번째 매개변수 > 이벤트 핸들러 전달
  - 이벤트 핸들러를 전달한다.
- 세번째 매개변수 > 이벤트 캐치할 이벤트 전파 단계 지정
  - 생략하거나 false로 지정 : 버블링 단계에서 이벤트를 캐치한다.
  - true 지정 : 캡처단계에서 이벤트를 캐치한다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    // 이벤트 핸들러 프로퍼티 방식
    // $button.onclick = function () {
    //   console.log('button click');
    // };

    // addEventListener 메서드 방식
    $button.addEventListener('click', function () {
      console.log('button click');
    });
  </script>
</body>
</html>
```

`addEventListener` 메서드는 이벤트 핸들러를 인수로 전달한다. 동일한 HTML 요소에서 발생한 동일한 이벤트에 대해 이벤트 핸들러 프로퍼티 방식과 `addEventListener`메서드 방식을 모두 사용하여 이벤트 핸들러를 등록하면 다음과 같다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    // 이벤트 핸들러 프로퍼티 방식
    $button.onclick = function () {
      console.log('[이벤트 핸들러 프로퍼티 방식]button click');
    };

    // addEventListener 메서드 방식
    $button.addEventListener('click', function () {
      console.log('[addEventListener 메서드 방식]button click');
    });
  </script>
</body>
</html>
```
addEventListener 메서드 방식은 이벤트 핸들러 프로퍼티에 바인딩된 이벤트 핸들러에 아무런 영향을 주지 않는다. 따라서 버튼 요소에서 클릭 이벤트가 발생하면 2개의 이벤트 핸들러가 모두 호출된다.

동일한 HTML 요소에서 발생한 동일한 이벤트에 대해 이벤트 핸들러 프로퍼티 방식은 하나 이상의 이벤트 핸들러를 등록할 수 없지만 `addEventListener` 메서드는 하나 이상의 이벤트 핸들러를 등록할 수 있다. 이때 이벤트 핸들러는 등록된 순서대로 호출된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    // addEventListener 메서드는 동일한 요소에서 발생한 동일한 이벤트에 대해
    // 하나 이상의 이벤트 핸들러를 등록할 수 있다.
    $button.addEventListener('click', function () {
      console.log('[1]button click');
    });

    $button.addEventListener('click', function () {
      console.log('[2]button click');
    });
  </script>
</body>
</html>
```

이때 `addEventListener` 메서드를 통해 참조가 동일한 이벤트 핸들러를 중복 등록하면 하나의 이벤트 핸들러만 등록이 된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    const handleClick = () => console.log('button click');

    // 참조가 동일한 이벤트 핸들러를 중복 등록하면 하나의 핸들러만 등록된다.
    $button.addEventListener('click', handleClick);
    $button.addEventListener('click', handleClick);
  </script>
</body>
</html>
```

## 4. 이벤트 핸들러 제거

`addEventListener` 메서드로 등록한 이벤트 핸들러를 제거하려면 `Event.prototype.removeEventListener` 메서드를 사용한다. 해당 메서드에 전달할 인수는 `addEventListener` 메서드와 동일하며, `addEventListener` 메서드에 전달한 인수와 일치해야지 제거가 된다.

또, 무명 함수를 이벤트 핸들러로 등록한 경우 제거가 불가능하며, 이벤트 핸들러의 참조를 변수나 자료구조에 저장하고 있어야 한한다. 기명 이벤트 핸들러 내부에서 메서드 호출 시 이벤트 핸들러를 제거하는 것이 가능하다. 이때, 이벤트 핸들러는 단 한 번만 호출된다. 

```js
// 기명 함수를 이벤트 핸들러로 등록
$button.addEventListener('click', function foo() {
  console.log('button click');
  // 이벤트 핸들러를 제거한다. 따라서 이벤트 핸들러는 단 한 번만 호출된다.
  $button.removeEventListener('click', foo);
});
```

이벤트 핸들러 프로퍼티 방식으로 등록한 이벤트 핸들러는 `removeEventListener` 메서드로 제거할 수 없다. 이를 제거하려면 이벤트 핸들러 프로퍼티에 `null`을 할당해야 한다. 

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');

    const handleClick = () => console.log('button click');

    // 이벤트 핸들러 프로퍼티 방식으로 이벤트 핸들러 등록
    $button.onclick = handleClick;

    // removeEventListener 메서드로 이벤트 핸들러를 제거할 수 없다.
    $button.removeEventListener('click', handleClick);

    // 이벤트 핸들러 프로퍼티에 null을 할당하여 이벤트 핸들러를 제거한다.
    $button.onclick = null;
  </script>
</body>
</html>
```

## 5. 이벤트 객체

**이벤트가 발생하면 이벤트에 관련한 다양한 정보를 담고 있는 이벤트 객체가 동적으로 생성된다. 생성된 이벤트 객체는 이벤트 핸들러의 첫 번째 인수로 전달된다.**

```js
<!DOCTYPE html>
<html>
<body>
  <p>클릭하세요. 클릭한 곳의 좌표가 표시됩니다.</p>
  <em class="message"></em>
  <script>
    const $msg = document.querySelector('.message');

    // 클릭 이벤트에 의해 생성된 이벤트 객체는 이벤트 핸들러의 첫 번째 인수로 전달된다.
    function showCoords(e) {
      $msg.textContent = `clientX: ${e.clientX}, clientY: ${e.clientY}`;
    }

    document.onclick = showCoords;
  </script>
</body>
</html>
```

클릭 이벤트에 의해 생성된 이벤트 객체는 이벤트 핸들러의 첫 번째 인수로 전달되어 매개변수 e에 암묵적으로 할당된다. 이는 브라우저가 이벤트 핸들러를 호출할 때 이벤트 객체를 인수로 전달하기 때문이다. 따라서 이벤트 객체를 전달받으려면 이벤트 핸들러를 정의할 때 이벤트 객체를 전달받을 매개변수를 명시적으로 선언해야 한다. 위 예제에서 e라는 이름으로 매개변수를 선언했으나 다른 이름을 사용하여도 상관없다.

이벤트 핸들러 어트리뷰트 방식으로 이벤트 핸들러를 등록했다면 다음과 같이 event를 통해 이벤트 객체를 전달받을 수 있다.

```js
<!DOCTYPE html>
<html>
<head>
  <style>
    html, body { height: 100%; }
  </style>
</head>
<!-- 이벤트 핸들러 어트리뷰트 방식의 경우 event가 아닌 다른 이름으로는 이벤트 객체를
전달받지 못한다. -->
<body onclick="showCoords(event)">
  <p>클릭하세요. 클릭한 곳의 좌표가 표시됩니다.</p>
  <em class="message"></em>
  <script>
    const $msg = document.querySelector('.message');

    // 클릭 이벤트에 의해 생성된 이벤트 객체는 이벤트 핸들러의 첫 번째 인수로 전달된다.
    function showCoords(e) {
      $msg.textContent = `clientX: ${e.clientX}, clientY: ${e.clientY}`;
    }
  </script>
</body>
</html>

```
### 이벤트 객체의 상속 구조

이벤트 객체 생성 시 _생성자 함수를 호출해 이벤트 객체를 생성할 수 있다._

```js
<!DOCTYPE html>
<html>
<body>
  <script>
    // Event 생성자 함수를 호출하여 foo 이벤트 타입의 Event 객체를 생성한다.
    let e = new Event('foo');
    console.log(e);
    // Event {isTrusted: false, type: "foo", target: null, ...}
    console.log(e.type); // "foo"
    console.log(e instanceof Event); // true
    console.log(e instanceof Object); // true

    // FocusEvent 생성자 함수를 호출하여 focus 이벤트 타입의 FocusEvent 객체를 생성한다.
    e = new FocusEvent('focus');
    console.log(e);
    // FocusEvent {isTrusted: false, relatedTarget: null, view: null, ...}

    // MouseEvent 생성자 함수를 호출하여 click 이벤트 타입의 MouseEvent 객체를 생성한다.
    e = new MouseEvent('click');
    console.log(e);
    // MouseEvent {isTrusted: false, screenX: 0, screenY: 0, clientX: 0, ... }

    // KeyboardEvent 생성자 함수를 호출하여 keyup 이벤트 타입의 KeyboardEvent 객체를
    // 생성한다.
    e = new KeyboardEvent('keyup');
    console.log(e);
    // KeyboardEvent {isTrusted: false, key: "", code: "", ctrlKey: false, ...}

    // InputEvent 생성자 함수를 호출하여 change 이벤트 타입의 InputEvent 객체를 생성한다.
    e = new InputEvent('change');
    console.log(e);
    // InputEvent {isTrusted: false, data: null, inputType: "", ...}
  </script>
</body>
</html>
```

`Event` 인터페이스는 DOM내에서 발생한 이벤트에 의해 생성되는 이벤트 객체를 나타낸다. Event 인터페이스에는 모든 이벤트 객체의 공통 프로퍼티가 정의되어 있고, `FocusEvent, MouseEvent, KeyboardEvent, WheelEvent` 같은 하위 인터페이스에는 이벤트 타입에 따라 고유한 프로퍼티가 정의되어 있다. 즉, 다음 예제와 같이 이벤트 객체의 프로퍼티는 발생한 이벤트의 타입에 따라 달라진다.

```js
<!DOCTYPE html>
<html>
<body>
  <input type="text">
  <input type="checkbox">
  <button>Click me!</button>
  <script>
    const $input = document.querySelector('input[type=text]');
    const $checkbox = document.querySelector('input[type=checkbox]');
    const $button = document.querySelector('button');

    // load 이벤트가 발생하면 Event 타입의 이벤트 객체가 생성된다.
    window.onload = console.log;

    // change 이벤트가 발생하면 Event 타입의 이벤트 객체가 생성된다.
    $checkbox.onchange = console.log;

    // focus 이벤트가 발생하면 FocusEvent 타입의 이벤트 객체가 생성된다.
    $input.onfocus = console.log;

    // input 이벤트가 발생하면 InputEvent 타입의 이벤트 객체가 생성된다.
    $input.oninput = console.log;

    // keyup 이벤트가 발생하면 KeyboardEvent 타입의 이벤트 객체가 생성된다.
    $input.onkeyup = console.log;

    // click 이벤트가 발생하면 MouseEvent 타입의 이벤트 객체가 생성된다.
    $button.onclick = console.log;
  </script>
</body>
</html>
```

### 이벤트 객체의 공통 프로퍼티

`Event` 인터페이스, 즉 `Event.prototype`에 정의되어 있는 `UIEvent, CustomEvent, MouseEvent` 등 모든 파생 이벤트 객체에 상속된다. 즉, Event 인터페이스의 이벤트 관련 프로퍼티는 모든 이벤트 객체가 상속받는 공통 프로퍼티다. 이벤트 객체의 공통 프로퍼티는 다음과 같다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/9a4ff829-7496-4e2f-8e0a-dd193b5937fe)


### 마우스 정보 취득

`click, dblclick, mousedown, mouseup, mouseenter, mouseleave` 이벤트가 발생하면 생성되는 MouseEvent 타입의 이벤트 객체는 다음과 같은 고유의 프로퍼티를 갖는다.

- 마우스 포인터 좌표 정보를 나타내는 프로퍼티: `screenX/screenY, clientX/clientY, pageX/pageY, offsetX/offsetY`
- 버튼 정보를 나타내는 프로퍼티: `altKey, ctrlKey, shiftKey, button`

### 키보드 정보 취득

`keydown, keyup, keypress` 이벤트가 발생하면 생성되는 KeyboardEvent 타입의 이벤트 객체는 `altKey, ctrlKey, shiftKey, metaKey, key, keyCode` 같은 고유의 프로퍼티를 갖는다.
찾아보니 `keypress`는 deprecated되었다고 하여 안쓰인다고 한다..!


## 6. 이벤트 전파

**DOM 트리 상에 존재하는 DOM 요소 노드에서 발생한 이벤트는 DOM 트리를 통해 전파된다. 이를 이벤트 전파(event propagation)이라 한다.**

```js
<!DOCTYPE html>
<html>
<body>
  <ul id="fruits">
    <li id="apple">Apple</li>
    <li id="banana">Banana</li>
    <li id="orange">Orange</li>
  </ul>
</body>
</html>
```

여기서 `ul`의 두번째 자식 요소 `li` 요소를 클릭하면 클릭 이벤트가 발생한다. 이때 _생성된 이벤트 객체는 이벤트를 발생시킨 DOM 요소인 이벤트 타깃(event target)을 중심으로 DOM 트리를 통해 전파된다._

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/47c67c91-781f-4971-9396-27e012345f56)

1. 캡처링 단계: 이벤트가 상위 요소에서 항위 요소 방향으로 전파
2. 타깃 단계: 이벤트가 이벤트 타깃에 도달
3. 버블링 단계: 이벤트가 하위 요소에서 상위 요소 방향으로 전파

```js

<!DOCTYPE html>
<html>
<body>
  <ul id="fruits">
    <li id="apple">Apple</li>
    <li id="banana">Banana</li>
    <li id="orange">Orange</li>
  </ul>
  <script>
    const $fruits = document.getElementById('fruits');

    // #fruits 요소의 하위 요소인 li 요소를 클릭한 경우
    $fruits.addEventListener('click', e => {
      console.log(`이벤트 단계: ${e.eventPhase}`); // 3: 버블링 단계
      console.log(`이벤트 타깃: ${e.target}`); // [object HTMLLIElement]
      console.log(`커런트 타깃: ${e.currentTarget}`); // [object HTMLUListElement]
    });
  </script>
</body>
</html>
```

_여기서는 `ul` 요소에 이벤트 핸들러를 바인딩하고 `ul` 요소의 하위 요소인 `li` 요소를 클릭해 이벤트를 발생시킨다. 이때 이벤트 타깃은 `li` 요소이고 커런트 타깃은 `ul`요소이다!_
1. `li` 요소 클릭시 클릭 이벤트 발생 > 클릭 이벤트 객체 생성 되고 클릭된 `li` 요소가 이벤트 타깃이 됨
2. 이때 클릭 이벤트 객체는 `window`에서 시작해 이벤트 타깃 방향으로 전파됨(**이게 캡처링 단계!**)
3. 이후 이벤트 객체는 이벤트를 발생시킨 이벤트 타깃에 도달한다. (**이게 타깃 단계!**)
4. 이벤트 객체는 이벤트 타깃에서 시작해서 `window` 방향으로 전파된다. (**이게 버블링 단계!**)

이벤트 핸들러 어트리뷰트/프로퍼티 방식으로 등록한 이벤트 핸들러는 타깃, 버블링 단계의 이벤트만 캐치할 수 있다. `addEventListenr` 메서드 방식으로 등록한 이벤트 핸들러는 타깃, 버블링, 캡처링 단계 이벤트도 선별적으로 캐치할 수 있다. 캡처링 단계의 이벤트를 캐치하려면 `addEventListener` 메서드의 3번째 인수로 `true`를 전달해야 한다. 

**이벤트는 이벤트를 발생시킨 이벤트 타깃은 물론 상위 DOM 요소에서도 캐치할 수 있다.**
즉, DOM 트리를 통해 전파되는 이벤트는 이벤트 패스(이벤트가 통과하는 DOM 트리 상의 경로)에 위치한 모든 DOM 요소에서 캐치할 수 있다. 

아래 이벤트는 버블링을 통해 전파되지 않는다. 따라서 캡처링 단계의 이벤트를 캐치해야 한다. 혹은 이들을 대체할 수 있는 이벤트가 존재한다.
- 포커스 이벤트 : focus/blur > focusin/focusout으로 대체
- 리소스 이벤트 : load/unload/abort/error
- 마우스 이벤트 : mouseenter/mouseleave > mouseover/mouseout으로 대체

_다음 예제는 캡처링 단계의 이벤트와 버블링 단계의 이벤트를 캐치하는 이벤트 핸들러가 혼용되는 경우다._

```js
<!DOCTYPE html>
<html>
<head>
  <style>
    html, body { height: 100%; }
  </style>
<body>
  <p>버블링과 캡처링 이벤트 <button>버튼</button></p>
  <script>
    // 버블링 단계의 이벤트를 캐치
    document.body.addEventListener('click', () => {
      console.log('Handler for body.');
    });

    // 캡처링 단계의 이벤트를 캐치
    document.querySelector('p').addEventListener('click', () => {
      console.log('Handler for paragraph.');
    }, true);

    // 타깃 단계의 이벤트를 캐치
    document.querySelector('button').addEventListener('click', () => {
      console.log('Handler for button.');
    });
  </script>
</body>
</html>
```

위 예제의 경우 body 요소는 버블링 단계의 이벤트만을 캐치하고 `p` 요소는 캡처링 단계의 이벤트만 캐치한다. 이벤트는 캡처링 - 타깃 - 버블링 단계로 전파되므로 만약 `button` 요소에서 클릭 이벤트가 발생하면 먼저 캡처링 단계를 캐치하는 `p` 요소의 이벤트 핸들러가 호출되고, 그후 버블링 단계의 이벤트를 캐치하는 body 요소의 이벤트 핸들러가 순차적으로 호출된다.

## 7. 이벤트 위임

사용자가 내비게이션 아이템(li 요소)을 클릭하여 선택하면 현재 선택된 내비게이션 아이템에 `active` 클래스를 추가하고 그 외의 모든 내비게이션 아이템의 active 클래스는 제거하는 다음 예제를 살펴보자!

```js
<!DOCTYPE html>
<html>
<head>
  <style>
    #fruits {
      display: flex;
      list-style-type: none;
      padding: 0;
    }

    #fruits li {
      width: 100px;
      cursor: pointer;
    }

    #fruits .active {
      color: red;
      text-decoration: underline;
    }
  </style>
</head>
<body>
  <nav>
    <ul id="fruits">
      <li id="apple" class="active">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
  </nav>
  <div>선택된 내비게이션 아이템: <em class="msg">apple</em></div>
  <script>
    const $fruits = document.getElementById('fruits');
    const $msg = document.querySelector('.msg');

    // 사용자 클릭에 의해 선택된 내비게이션 아이템(li 요소)에 active 클래스를 추가하고
    // 그 외의 모든 내비게이션 아이템의 active 클래스를 제거한다.
    function activate({ target }) {
      [...$fruits.children].forEach($fruit => {
        $fruit.classList.toggle('active', $fruit === target);
        $msg.textContent = target.id;
      });
    }

    // 모든 내비게이션 아이템(li 요소)에 이벤트 핸들러를 등록한다.
    document.getElementById('apple').onclick = activate;
    document.getElementById('banana').onclick = activate;
    document.getElementById('orange').onclick = activate;
  </script>
</body>
</html>
```

위 예제를 살펴보면 모든 내비게이션 아이템(li 요소)이 클릭 이벤트에 반응하도록 모든 내비게이션 아이템에 이벤트 핸들러인 activate를 등록했다. 만일 내비게이션 아이템이 100개라면 100개의 이벤트 핸들러를 등록해야 한다. 이 경우 많은 DOM 요소에 이벤트 핸들러를 등록하므로 성능 저하의 원인이 될 뿐더러 유지보수에도 부적합한 코드를 생산하게 한다.

**이벤트 위임: 여러 개의 하위 DOM 요소에 각각 이벤트 핸들러를 등록하는 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법을 말한다.**

이벤트 위임을 통해 상위 DOM 요소에 이벤트 핸들러를 등록하면 여러 개의 하위 DOM 요소에 이벤트 핸들러를 등록할 필요가 없다. 또한, 동적으로 하위 DOM 요소를 추가하더라도 일일이 추가된 DOM 요소에 이벤트 핸들러를 등록할 필요가 없다.

```js
//수정본!

<!DOCTYPE html>
<html>
<head>
  <style>
    #fruits {
      display: flex;
      list-style-type: none;
      padding: 0;
    }

    #fruits li {
      width: 100px;
      cursor: pointer;
    }

    #fruits .active {
      color: red;
      text-decoration: underline;
    }
  </style>
</head>
<body>
  <nav>
    <ul id="fruits">
      <li id="apple" class="active">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
  </nav>
  <div>선택된 내비게이션 아이템: <em class="msg">apple</em></div>
  <script>
    const $fruits = document.getElementById('fruits');
    const $msg = document.querySelector('.msg');

    // 사용자 클릭에 의해 선택된 내비게이션 아이템(li 요소)에 active 클래스를 추가하고
    // 그 외의 모든 내비게이션 아이템의 active 클래스를 제거한다.
    function activate({ target }) {
      // 이벤트를 발생시킨 요소(target)가 ul#fruits의 자식 요소가 아니라면 무시한다.
      if (!target.matches('#fruits > li')) return;

      [...$fruits.children].forEach($fruit => {
        $fruit.classList.toggle('active', $fruit === target);
        $msg.textContent = target.id;
      });
    }

    // 이벤트 위임: 상위 요소(ul#fruits)는 하위 요소의 이벤트를 캐치할 수 있다.
    $fruits.onclick = activate;
  </script>
</body>
</html>
```

이벤트 위임을 통해 하위 DOM 요소에서 발생한 이벤트를 처리할 때 주의할 점은 상위 요소에 이벤트 핸들러를 등록하기 때문에 이벤트 타깃, 즉 이벤트를 실제로 발생시킨 DOM 요소가 개발자가 기대한 DOM 요소가 아닐 수도 있다는 것이다. 위 예제의 경우 ul#fruits 요소에 바인딩된 이벤트 핸들러는 자기 자신은 물론 ul#fruits 요소의 하위 요소 중에서 클릭 이벤트를 발생시킨 모든 DOM 요소에 반응한다. 따라서 이벤트에 반응이 필요한 DOM 요소(위 예제의 경우` #fruits > li` 선택자에 의해 선택되는 DOM 요소)에 한정하여 이벤트 핸들러가 실행되도록 이벤트 타깃을 검사할 필요가 있다.

## 8. DOM 요소의 기본 동작 조작

### DOM 요소의 기본 동작 중단

DOM 요소는 저마다 기본 동작이 있다. 이벤트 객체의 `preventDefault` 메서드는 이러한 DOM 요소의 기본 동작을 중단시킨다. 

```js
<!DOCTYPE html>
<html>
<body>
  <a href="https://www.google.com">go</a>
  <input type="checkbox">
  <script>
    document.querySelector('a').onclick = e => {
      // a 요소의 기본 동작을 중단한다.
      e.preventDefault();
    };

    document.querySelector('input[type=checkbox]').onclick = e => {
      // checkbox 요소의 기본 동작을 중단한다.
      e.preventDefault();
    };
  </script>
</body>
</html>
```

### 이벤트 전파 방지

이벤트 객체의 `stopPropagation` 메서드는 이벤트 전파를 중지시킨다.

```js
<!DOCTYPE html>
<html>
<body>
  <div class="container">
    <button class="btn1">Button 1</button>
    <button class="btn2">Button 2</button>
    <button class="btn3">Button 3</button>
  </div>
  <script>
    // 이벤트 위임. 클릭된 하위 버튼 요소의 color를 변경한다.
    document.querySelector('.container').onclick = ({ target }) => {
      if (!target.matches('.container > button')) return;
      target.style.color = 'red';
    };

    // .btn2 요소는 이벤트를 전파하지 않으므로 상위 요소에서 이벤트를 캐치할 수 없다.
    document.querySelector('.btn2').onclick = e => {
      e.stopPropagation(); // 이벤트 전파 중단
      e.target.style.color = 'blue';
    };
  </script>
</body>
</html>
```

1. 여기서 상위 DOM 요소인 `container` 요소에 이벤트를 위임함
2. 따라서 하위 DOM 요소에서 발생한 클릭 이벤트를 상위 DOM 요소인 `container` 요소가 캐치하여 이벤트를 처리함
3. 하지만 하위 요소 중 `btn2` 요소는 자체적으로 이벤트 처리함
4. 이때 `btn2` 요소는 자신이 발생시킨 이벤트가 전파되는 것을 중단해 자신에게 바인딩된 이벤트 핸들러만 실행되도록 함

**이처럼 `stopPropagation` 메서드는 하위 DOM 요소의 이벤트를 개별적으로 처리하기 위해 이벤트의 전파를 중단시킨다.**


## 9. 이벤트 함수 내부의 this

### 이벤트 핸들러 어트리뷰트 방식

다음 예제의 `handleClick` 함수 내부의 `this` 는 전역 객체 `window`를 가리킨다.

```js
<!DOCTYPE html>
<html>
<body>
  <button onclick="handleClick()">Click me</button>
  <script>
    function handleClick() {
      console.log(this); // window
    }
  </script>
</body>
</html>
```

이벤트 핸들러 어트리뷰트 값으로 지정한 문자열은 사실 암묵적으로 생성되는 이벤트 핸들러의 문이라고 했다. 따라서 `handleClick` 함수는 이벤트 핸들러에 의해 일반 함수로 호출된다. 일반 함수로서 호출되는 함수 내부의 `this`는 전역 객체를 가리킨다.

단, 이벤트 핸들러를 호출할 때 인수로 전달한 `this`는 이벤트를 바인딩한 DOM 요소를 가리킨다.

### 이벤트 핸들러 프로퍼티 방식과 `addEventListener` 메서드 방식

이벤트 핸들러 프로퍼티 방식과 `addEventListener` 메서드 방식 모두 이벤트 핸들러 내부의 `this`는 이벤트를 바인딩한 DOM 요소를 가리킨다. 즉, 이벤트 핸들러 내부의 this는 이벤트 객체의 `currentTarget` 프로퍼티와 같다.

```js
<!DOCTYPE html>
<html>
<body>
  <button class="btn1">0</button>
  <button class="btn2">0</button>
  <script>
    const $button1 = document.querySelector('.btn1');
    const $button2 = document.querySelector('.btn2');

    // 이벤트 핸들러 프로퍼티 방식
    $button1.onclick = function (e) {
      // this는 이벤트를 바인딩한 DOM 요소를 가리킨다.
      console.log(this); // $button1
      console.log(e.currentTarget); // $button1
      console.log(this === e.currentTarget); // true

      // $button1의 textContent를 1 증가시킨다.
      ++this.textContent;
    };

    // addEventListener 메서드 방식
    $button2.addEventListener('click', function (e) {
      // this는 이벤트를 바인딩한 DOM 요소를 가리킨다.
      console.log(this); // $button2
      console.log(e.currentTarget); // $button2
      console.log(this === e.currentTarget); // true

      // $button2의 textContent를 1 증가시킨다.
      ++this.textContent;
    });
  </script>
</body>
</html>
```

화살표 함수로 정의한 이벤트 핸들러 내부의 `this`는 상위 스코프의 `this`를 가리킨다. 화살표 함수는 함수 자체의 `this` 바인딩을 갖지 않는다.

```js
<!DOCTYPE html>
<html>
<body>
  <button class="btn1">0</button>
  <button class="btn2">0</button>
  <script>
    const $button1 = document.querySelector('.btn1');
    const $button2 = document.querySelector('.btn2');

    // 이벤트 핸들러 프로퍼티 방식
    $button1.onclick = e => {
      // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
      console.log(this); // window
      console.log(e.currentTarget); // $button1
      console.log(this === e.currentTarget); // false

      // this는 window를 가리키므로 window.textContent에 NaN(undefined + 1)을 할당한다.
      ++this.textContent;
    };

    // addEventListener 메서드 방식
    $button2.addEventListener('click', e => {
      // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
      console.log(this); // window
      console.log(e.currentTarget); // $button2
      console.log(this === e.currentTarget); // false

      // this는 window를 가리키므로 window.textContent에 NaN(undefined + 1)을 할당한다.
      ++this.textContent;
    });
  </script>
</body>
</html>
```

클래스에서 이벤트 핸들러를 바인딩하는 경우 `this`에 주의해야 한다.

다음 예제는 이벤트 핸들로 프로퍼티 방식을 사용하고 있으나 `addEventListener` 메서드 방식을 사용하는 경우와 동일하다.

```js
<!DOCTYPE html>
<html>
<body>
  <button class="btn">0</button>
  <script>
    class App {
      constructor() {
        this.$button = document.querySelector('.btn');
        this.count = 0;

        // increase 메서드를 이벤트 핸들러로 등록
        this.$button.onclick = this.increase;
      }

      increase() {
        // 이벤트 핸들러 increase 내부의 this는 DOM 요소(this.$button)를 가리킨다.
        // 따라서 this.$button은 this.$button.$button과 같다.
        this.$button.textContent = ++this.count;
        // -> TypeError: Cannot set property 'textContent' of undefined
      }
    }

    new App();
  </script>
</body>
</html>
```

위 에제의 `increase` 메서드 내부의 `this`는 클래스가 생성할 인스턴스를 가리키지 않는다. 이벤트 핸들러 내부의 this는 이벤트를 바인딩한 DOM 요소를 가리키기 때문에 `increase` 메서드 내부의 `this`는 `this.$button`을 가리킨다. 따라서 `increase` 메서드를 이벤트 핸들러로 바인딩할 때 bind 메서드를 사용해 `this`를 전달하여 `increase` 메서드 내부의 `this`가 생성할 인스턴스를 가리키도록 해야 한다.

또는 클래스 필드에 할당한 화살표 함수를 이벤트 핸들러로 등록하여 이벤트 핸들러 내부의 `this`가 인스턴스를 가리키도록 할 수도 있다. 다만 이때 이벤트 핸들러 `increase`는 프로토타입 메서드가 아닌 인스턴스 메서드가 된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button class="btn">0</button>
  <script>
    class App {
      constructor() {
        this.$button = document.querySelector('.btn');
        this.count = 0;

        // 화살표 함수인 increase를 이벤트 핸들러로 등록
        this.$button.onclick = this.increase;
      }

      // 클래스 필드 정의
      // increase는 인스턴스 메서드이며 내부의 this는 인스턴스를 가리킨다.
      increase = () => this.$button.textContent = ++this.count;
    }
    new App();
  </script>
</body>
</html>
```
## 10. 이벤트 핸들러에 인수 전달

함수에 인수를 전달하려면 함수를 호출할 때 전달해야 한다. 이벤트 핸들러 어트리뷰트 방식은 함수 호출문을 사용할 수 있기 때문에 인수를 전달할 수 있지만 이벤트 핸들러 프로퍼티 방식과 `addEventListener` 메서드 방식의 경우 이벤트 핸들러를 브라우저가 호출하기 때문에 함수 호출문이 아닌 함수 자체를 등록해야 한다. 따라서 인수를 전달할 수 없다. 그러나 인수를 전달할 방법이 전혀 없는 것은 아니다. 다음 예제와 같이 이벤트 핸들러 내부에서 함수를 호출하면서 인수를 전달할 수 있다.

```js
<!DOCTYPE html>
<html>
<body>
  <label>User name <input type='text'></label>
  <em class="message"></em>
  <script>
    const MIN_USER_NAME_LENGTH = 5; // 이름 최소 길이
    const $input = document.querySelector('input[type=text]');
    const $msg = document.querySelector('.message');

    const checkUserNameLength = min => {
      $msg.textContent
        = $input.value.length < min ? `이름은 ${min}자 이상 입력해 주세요` : '';
    };

    // 이벤트 핸들러 내부에서 함수를 호출하면서 인수를 전달한다.
    $input.onblur = () => {
      checkUserNameLength(MIN_USER_NAME_LENGTH);
    };
  </script>
</body>
</html>
```

또한, 이벤트 핸들러를 반환하는 함수를 호출하면서 인수를 전달할 수도 있다.

```js
<!DOCTYPE html>
<html>
<body>
  <label>User name <input type='text'></label>
  <em class="message"></em>
  <script>
    const MIN_USER_NAME_LENGTH = 5; // 이름 최소 길이
    const $input = document.querySelector('input[type=text]');
    const $msg = document.querySelector('.message');

    // 이벤트 핸들러를 반환하는 함수
    const checkUserNameLength = min => e => {
      $msg.textContent
        = $input.value.length < min ? `이름은 ${min}자 이상 입력해 주세요` : '';
    };

    // 이벤트 핸들러를 반환하는 함수를 호출하면서 인수를 전달한다.
    $input.onblur = checkUserNameLength(MIN_USER_NAME_LENGTH);
  </script>
</body>
</html>
```

## 11. 커스텀 이벤트

### 커스텀 이벤트 생성

개발자의 의도로 생성된 이벤트를 커스텀 이벤트라고 한다.

이벤트 생성자 함수는 첫번째 인수로 이벤트 타입을 나타내는 문자열을 전달받는다. 이때, 문자열은 기존 이벤트 타입이 아니라 임의의 문자열을 사용하여 새로운 이벤트 타입을 지정할 수 있다. 이 경우 `CustomEvent` 이벤트 생성자 함수를 사용한다.
이 객체는 버블링되지 않고, `preventDefault` 메서드로 취소할 수도 없다. 커스텀 이벤트 객체의 `bubbles`와 `cancelable` 프로퍼티 값을 `true`로 설정하려면 이벤트 생성자 함수의 두 번째 인수로 `bubbles` 또는 `cancelable` 프로퍼티를 갖는 객체를 전달한다. 

```js
// KeyboardEvent 생성자 함수로 keyup 이벤트 타입의 커스텀 이벤트 객체를 생성
const keyboardEvent = new KeyboardEvent('keyup');
console.log(keyboardEvent.type); // keyup

// CustomEvent 생성자 함수로 foo 이벤트 타입의 커스텀 이벤트 객체를 생성
const customEvent = new CustomEvent('foo');
console.log(customEvent.type); // foo


// MouseEvent 생성자 함수로 click 이벤트 타입의 커스텀 이벤트 객체를 생성
const customEvent = new MouseEvent('click', {
  bubbles: true,
  cancelable: true
});

console.log(customEvent.bubbles); // true
console.log(customEvent.cancelable); // true
```

커스텀 이벤트 객체에는 `bubbles` 또는 `cancelable` 프로퍼티 뿐만 아니라 이벤트 타입에 따라 가지는 이벤트 고유의 프로퍼티 값을 지정할 수 있다. 예를 들어, `MouseEvent` 생성자 함수로 생성한 마우스 이벤트 객체는 마우스 포인터의 좌표 정보를 나타내는 마우스 이벤트 객체 고유의 프로퍼티 `screenX/screenY, clientX/clientY, offsetX/offsetY`와 버튼 정보를 나타내는 프로퍼티 `altKey, ctrlKey, shiftKey button`을 갖는다. 이러한 이벤트 객체 고유의 프로퍼티 값을 지정하려면 다음과 같이 이벤트 생성자 함수의 두 번째 인수로 프로퍼티를 전달한다.

```js
// MouseEvent 생성자 함수로 click 이벤트 타입의 커스텀 이벤트 객체를 생성
const mouseEvent = new MouseEvent('click', {
  bubbles: true,
  cancelable: true,
  clientX: 50,
  clientY: 100
});

console.log(mouseEvent.clientX); // 50
console.log(mouseEvent.clientY); // 100

// KeyboardEvent 생성자 함수로 keyup 이벤트 타입의 커스텀 이벤트 객체를 생성
const keyboardEvent = new KeyboardEvent('keyup', { key: 'Enter' });

console.log(keyboardEvent.key); // Enter
```

이벤트 생성자 함수로 생성한 커스텀 이벤트는 `isTrusted` 프로퍼티의 값이 언제나 `false`다. 커스텀 이벤트가 아닌 사용자의 행위에 의해 발생한 이벤트에 의해 생성된 이벤트 객체의 `isTrusted` 프로퍼티 값은 언제나 `true`다.

```js
// InputEvent 생성자 함수로 foo 이벤트 타입의 커스텀 이벤트 객체를 생성
const customEvent = new InputEvent('foo');
console.log(customEvent.isTrusted); // false
```

### 커스텀 이벤트 디스패치

생성된 커스텀 이벤트는 `dispatchEvent` 메서드로 디스패치(이벤트를 발생시키는 행위)할 수 있다. `dispatchEvent` 메서드에 이벤트 객체를 인수로 전달하면서 호출하면 인수로 전달한 이벤트 타입의 이벤트가 발생한다.

```js
<!DOCTYPE html>
<html>
<body>
  <button class="btn">Click me</button>
  <script>
    const $button = document.querySelector('.btn');

    // 버튼 요소에 click 커스텀 이벤트 핸들러를 등록
    // 커스텀 이벤트를 디스패치하기 이전에 이벤트 핸들러를 등록해야 한다.
    $button.addEventListener('click', e => {
      console.log(e); // MouseEvent {isTrusted: false, screenX: 0, ...}
      alert(`${e} Clicked!`);
    });

    // 커스텀 이벤트 생성
    const customEvent = new MouseEvent('click');

    // 커스텀 이벤트 디스패치(동기 처리). click 이벤트가 발생한다.
    $button.dispatchEvent(customEvent);
  </script>
</body>
</html>
```

일반적으로 이벤트 핸들러는 비동기(asynchronous) 처리 방식으로 동작하지만 `dispatchEvent` 메서드는 이벤트 핸들러를 동기(synchronous) 처리 방식으로 호출한다. 다시 말해, `dispatchEvent` 메서드를 호출하면 커스텀 이벤트에 바인딩된 이벤트 핸들러를 직접 호출하는 것과 같다. 따라서 `dispatchEvent` 메서드로 이벤트를 디스패치하기 이전에 커스텀 이벤트를 처리할 이벤트 핸들러를 등록해야 한다.

기존 이벤트 타입이 아닌 임의의 이벤트 타입을 지정하여 이벤트 객체를 생성하는 경우 일반적으로 `CustomEvent` 이벤트 생성자 함수를 사용한다.

```js
// CustomEvent 생성자 함수로 foo 이벤트 타입의 커스텀 이벤트 객체를 생성
const customEvent = new CustomEvent('foo');
console.log(customEvent.type); // foo
```

이때 `CustomEvent` 이벤트 생성자 함수에는 두 번째 인수로 이벤트와 함께 전달하고 싶은 정보를 담은 `detail` 프로퍼티를 포함하는 객체를 전달할 수 있다. 이 정보는 이벤트 객체의 `detail` 프로퍼티(e.detail)에 담겨 전달된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button class="btn">Click me</button>
  <script>
    const $button = document.querySelector('.btn');

    // 버튼 요소에 foo 커스텀 이벤트 핸들러를 등록
    // 커스텀 이벤트를 디스패치하기 이전에 이벤트 핸들러를 등록해야 한다.
    $button.addEventListener('foo', e => {
      // e.detail에는 CustomEvent 함수의 두 번째 인수로 전달한 정보가 담겨 있다.
      alert(e.detail.message);
    });

    // CustomEvent 생성자 함수로 foo 이벤트 타입의 커스텀 이벤트 객체를 생성
    const customEvent = new CustomEvent('foo', {
      detail: { message: 'Hello' } // 이벤트와 함께 전달하고 싶은 정보
    });

    // 커스텀 이벤트 디스패치
    $button.dispatchEvent(customEvent);
  </script>
</body>
</html>
```

기존 이벤트 타입이 아닌 임의의 이벤트 타입을 지정하여 커스텀 이벤트 객체를 생성한 경우 반드시 `addEventListener` 메서드 방식으로 이벤트 핸들러를 등록해야 한다. 이벤트 핸들러 어트리뷰트/프로퍼티 방식을 사용할 수 없는 이유는 'on + 이벤트 타입'으로 이루어진 이벤트 핸들러 어트리뷰트/프로퍼티가 요소 노드에 존재하지 않기 때문이다. 예를 들어 `foo`라는 임의의 이벤트 타입으로 커스텀 이벤트를 생성한 경우 `onfoo` 라는 핸들러 어트리뷰트/프로퍼티가 요소 노드에 존재하지 않기 때문에 이벤트 핸들러 어트리뷰트/프로퍼티 방식으로는 이벤트 핸들러를 등록할 수 없다.
