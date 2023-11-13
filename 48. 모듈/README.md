48. 모듈

## 1. 모듈의 일반적 의미

__모듈이란 앱을 구성하는 개별적 요소로 재사용 가능한 코드 조각을 의미. 보통 기능을 기준으로 파일 단위로 분리함__

- 모듈은 자신만의 _파일 스코프(모듈 스코프)_ 를 가질 수 있어야 함
- 모듈의 자산(모듈에 포함되어 있는 변수, 함수, 객체 등)은 캡슐화되어 기본적으로 비공개이기 때문에 다른 모듈에서 접근할 수 없음. __즉, 모듈은 개별적 존재로서 애플리케이션과 분리되어 존재함__
- 하지만 이는 의미가 없으므로 __모듈은 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개가 가능하다. 👉 EXPORT__
- __모듈 사용자는 모듈이 공개(export)한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용할 수 있다. 👉 IMPORT__

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/162c7614-9ce4-4b6c-9b5b-ef6b57d8579f)


따라서 모듈은 앱과 분리되어 개별적으로 존재하다가 다른 모듈에 의해 재사용된다. 코드의 단위를 명확히 분리해 앱을 구성할 수 있기에 __재사용성이 좋고 개발 효율성과 유지보수성을 높일 수 있다.__

## 2. 자바스크립트와 모듈

원래 JS는 웹페이지의 단순한 보조 기능을 처리하기 위함이라는 제한적인 용도를 목적으로 태어남. 즉, JS 모듈이 성립하기에 필요한 __파일 스코프와 import, export를 지원하지 않음__

다른 언어와 다르게 JS 파일을 여러 개의 파일로 분리해 `script` 태그로 로드를 해도 분리된 JS 파일들은 결국 하나의 JS 파일 내에 있는 것처럼 동작하기 때문에, 즉, 모든 JS 파일은 하나의 전역을 공유하기 때문에 모듈 구현이 불가능하다.

_클라이언트 사이드, 브라우저 환경에 국한되지 않고 범용적으로 사용하려는 의도로 CommonJS와 AMD(Asynchronous Module Definition)이 제안되었다._

이후 Node.js의 경우 ECMAScript 표준 사양은 아니지만 모듈 시스템을 지원하며, Node.js 환경에서는 파일별로 독립적인 파일 스코프(모듈 스코프)를 갖는다. 

## 3. ES6 모듈(ESM)

ES6에서 클라이언트 사이드 JS에서도 작동하는 모듈 기능이 추가되었다. 

ES6 모듈(ESM)을 사용하려면 `script` 태그에 `type="module"` 어트리뷰트를 추가하면 로드된 JS 파일이 모듈로서 작동을 한다. 

일반 JS 파일과 구별하기 위해 ESM 파일 확장자는 `mjs`를 사용할 것을 권장한다.

```js
<script type="module" src="app.mjs"></script>
```

### 모듈 스코프

ESM은 독자적인 모듈 스코프를 가진다. 

모듈 내에서 `var` 키워드로 선언한 변수는 더는 전역 변수가 아니며 window 객체 프로퍼티도 아니게 된다.

```js
// foo.mjs
// x 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.
var x = 'foo';
console.log(x); // foo
console.log(window.x); // undefined
```

```js
// bar.mjs
// x 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.
// foo.mjs에서 선언한 x 변수와 스코프가 다른 변수다.
var x = 'bar';
console.log(x); // bar
console.log(window.x); // undefined
```

```js
<!DOCTYPE html>
<html>
<body>
  <script type="module" src="foo.mjs"></script>
  <script type="module" src="bar.mjs"></script>
</body>
</html>
```

모듈 내에서 선언한 식별자는 모듈 외부에서 참조 불가능하다. 그 이유는 모듈 스코프가 다르기 때문이다. 

### export 키워드

모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있게 하려면 `export` 키워드를 사용한다.

`export` 키워드는 선언문 앞에 사용을 하여 변수, 함수, 클래스 등 모든 식별자를 export 할 수 있다. 

```js
// lib.mjs
// 변수의 공개
export const pi = Math.PI;

// 함수의 공개
export function square(x) {
  return x * x;
}

// 클래스의 공개
export class Person {
  constructor(name) {
    this.name = name;
  }
}
```

선언문 앞에 매번 `export` 키워드를 붙이는 게 번거롭다면 export할 대상을 하나의 객체로 구성하여 한 번에 export 할 수도 있다. 

```js
// lib.mjs
const pi = Math.PI;

function square(x) {
  return x * x;
}

class Person {
  constructor(name) {
    this.name = name;
  }
}

// 변수, 함수 클래스를 하나의 객체로 구성하여 공개
export { pi, square, Person };
```

### import 키워드

다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드하기 위해서는 `import` 키워드를 사용한다. 다른 모듈이 export한 식별자 이름으로 import해야 하며 ESM의 경우 파일 확장자를 생략할 수 없다!

```js
// app.mjs
// 같은 폴더 내의 lib.mjs 모듈이 export한 식별자 이름으로 import한다.
// ESM의 경우 파일 확장자를 생략할 수 없다.
import { pi, square, Person } from './lib.mjs';

console.log(pi);         // 3.141592653589793
console.log(square(10)); // 100
console.log(new Person('Lee')); // Person { name: 'Lee' }
```

```js
<!DOCTYPE html>
<html>
<body>
  <script type="module" src="app.mjs"></script>
</body>
</html>
```

export한 식별자 이름을 일일이 지정하지 않고 하나의 이름으로 한 번에 import할 수도 있다. 이때 import되는 식별자는 `as` 뒤에 지정한 이름의 객체에 프로퍼티로 할당된다.

```js
//app.mjs
//lib.mjs 모듈이 export한 모든 식별자를 lib 객체의 프로퍼티로 모아 import
import * as lib from './lib.mjs';

console.log(lib.pi); //3.141592
console.log(lib.square(10)); //100
console.log(new lib.Person('Lee')); // Person { name: 'Lee' }
```

모듈이 export한 식별자 이름을 변경하여 import할 수도 있다.

```js
import { pi as PI, square as sq, Person as P} from './lib.mjs'; 
...
```

모듈에서 하나의 값만 export 할 경우 `default`를 사용할 수 있다. 해당 키워드를 사용하는 경우 기본적으로 이름 없이 하나의 값을 export한다.

`default` 키워드는 `var, let, const`와 같이 사용될 수 없다. 또한 `default` 키워드와 함께 export한 모듈은 `{}` 없이 임의의 이름으로 import한다. 

```js
//app.mjs
import square from  '/lib.mjs';

console.log(square(3)); //9
```
