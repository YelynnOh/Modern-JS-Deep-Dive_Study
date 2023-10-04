# 39. DOM

__DOM(Document Object Model)은 HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조다.__

## 1. 노드

### HTML 요소와 노드 객체

HTML 요소는 HTML 문서를 구성하는 개별적인 요소를 의미한다.

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/a232c270-5a31-4a44-b79a-32f51e82a02d)

HTML 요소 간에는 중첩 관계에 의해 계층적인 부자(parent-child) 관계가 형성된다. 이러한 HTML 요소 간의 부자 관계를 반영하여 HTML 문서의 구성 요소인 HTML 요소를 객체화한 모든 노드 객체들을 트리 자료 구조로 구성한다.

__노드 객체들로 구성된 트리 자료구조를 DOM이라고 한다.__

### 노드 객체의 타입

DOM은 노드 객체의 계층적인 구조로 구성된다. 노드 객체는 총 12개의 종류가 있다. 중요한 노드 타입은 아래 4개이다.

1) 문서 노드(document node)
  - DOM 트리 최상위에 존재하는 루트 노드로 `document` 객체를 가리킴
  - 문서 노드는 `window.document` 나 `document`로 참조할 수 있다.
  - _HTML 문서당 `document` 객체는 유일하다._
2) 요소 노드(element node)
  - HTML 요소를 가리키는 객체
3) 어트리뷰트 노드(attribute node)
  - HTML 요소의 어트리뷰트를 가리키는 객체
  - 어트리뷰트 노드는 부모 노드가 없어 요소 노드의 형제 노드는 아니며, 어트리뷰트 노드에 접근해 어트리뷰트를 참조하거나 변경하려면 먼저 요소 노드에 접근해야 한다.
4) 텍스트 노드(text node)
  - HTML 요소의 텍스트를 가리키는 객체
  - DOM 트리의 최종단이며, 텍스트 노드 접근시 먼저 부모 노드인 요소 노드에 접근해야 한다.

### 노드 객체의 상속 구조

__DOM을 구성하는 노드 객체는 자신의 구조와 정보를 제어할 수 있는 DOM API를 사용할 수 있다.__

DOM을 구성하는 노드 객체는 ECMAScript 사양에 정의된 표준 빌트인 객체가 아니라 브라우저 환경에서 추가적으로 제공하는 호스트 객체다.
하지만 노드 객체도 자바스크립트 객체이므로 프로토타입에 의한 상속 구조를 갖는다. 

![image](https://github.com/YelynnOh/Modern-JS-Deep-Dive_Study/assets/110076475/06f00e93-3415-401b-b7b5-ab2e9bf78d3f)

노드 객체에는 노드 객체의 종류, 즉 노드 타입에 상관없이 모든 노드 객체가 공통으로 갖는 기능도 있고, 노드 타입에 따라 공유한 기능도 있다.

_공통점_
- 모든 노드 객체는 공통적으로 이벤트를 발생시킬 수 있다.
- 트리 탐색 기능(`Node.parentNode, Node.childNodes, Node.previousSibling` 등)이나 노드 정보 제공 기능(`Node.nodeType, Node.nodeName` 등)이 필요하다.
- HTML 요소가 객체화된 요소 노드 객체는 HTML 요소가 갖는 공통적인 기능이 있다. (`input` 요소 노드 객체랑 `div` 요소 노드 객체는 모두 `style` 프로퍼티가 있다.)

_차이점_
- 요소 노드 객체는 HTML 요소에 따라 고유한 기능도 있다. (`input`에는 `value` 프로퍼티가 필요하지만 `div` 요소 노드 객체는 `value` 프로퍼티가 필요하지 않다.)

노드 객체는 공통된 기능일수록 프로토타입 체인의 상위에, 개별적인 고유 기능일수록 프로토타입 체인의 하위에 프로토타입 체인을 구축하여 노드 객체에 필요한 기능, 즉 프로퍼티와 메서드를 제공하는 상속 구조를 갖는다.

_DOM은 HTML 문서의 계층적 구조와 정보를 표현하는 것은 물론 노드 객체의 종류, 즉 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API로 제공한다. 이 DOM API를 통해 HTML의 구조나 내용 또는 스타일 등을 동적으로 조작할 수 있다._

## 2. 요소 노드 취득

HTML 구조나 내용 또는 스타일을 동적 조작하려고하면 요소 취득이 선행되어야 한다.
DOM은 요소 노드를 취득할 수 있는 다양한 메서드를 제공한다.

### id를 이용한 요소 노드 취득

`Document.prototype.getElementById`

해당 메서드는 인수로 전달된 id 값을 갖는 첫 번째 요소 노드만 반환한다. 즉, 이는 언제나 단 하나의 요소 노드만을 반환한다.

HTML 요소가 존재하지 않는 경우 `null`이 반환된다.

HTML 요소에 id 어트리뷰트를 부여할 경우 id 값과 동일한 이름의 전역 변수가 암묵적으로 선언되고 해당 노드 객체가 할당되는 부수 효과가 있다.
단, id 값과 동일한 이름의 전역 변수가 이미 선언되어있다면 이 전역 변수에 노드 객체가 재할당되지 않는다.

### 태그 이름을 이용한 요소 노드 취득

`Document.prototype.getElementByTagName`, `Element.prototype.getElementsByTagName`

인수로 전달한 태그 이름을 갖는 모든 요소 노드들을 탐색해 반환한다.

여러 개의 요소 노드 객체를 갖는 DOM 컬렉션 객체인 HTMLCollection 객체를 반환한다.

`getElemenetsByTagName` 메서드가 반환하는 DOM 컬렉션 객체인 HTMLCollection 객체는 유사 배열 객체이면서 이터러블이다. 

### class를 이용한 요소 노드 취득

`Document.prototype.getElementsByClassName`, `ELement.prototype.getElementsByClassName` 메서드는 인수로 전달한 class 어트리뷰트 값을 갖는 모든 요소 노드들을 탐색해 반환한다. 

인수로 전달할 class 값은 공백으로 구분하여 여러 개의 class를 지정할 수 있다. 

HTML 객체가 반환된다. 

### HTMLCollection과 NodeList

DOM 컬렉션 객체인 HTMLCollection과 NodeList는 DOM API가 여러 개의 결과값을 반환하기 위한
DOM 컬렉션 객체이다. HTMLCollection과 NodeList는 모두 유사 배열 객체이면서 이터러블이다.
따라서 `for...of` 문으로 순회할 수 있으며 스프레드 문법을 사용하여 간단히 배열로 변환할 수 있다.

_HTMLCollection과 NodeList의 중요한 특징은 노드 객체의 상태 변화를 실시간으로 반영하여 살아 있는 객체라는 것이다._

__HTMLCollection__

- `getElementsByTagName, getElementsByClassName`
- 노드 객체의 상태 변화를 실시간으로 반영하는 live DOM 컬렉션 객체
- for문으로 순회를 하며 노드 객체의 상태를 변경할 때는 유사 배열 객체이면서 이터러블인 HTMLCollection 객체를 배열로 변환해 순회하는 것을 권장한다.

__NodeList__

- `querySelectorAll`
- DOM 컬렉션 객체인 nodeList 객체를 반환한다. 이는 실시간으로 노드 객체의 상태 변경을 반영하지 않는 객체이다.
- 하지만 childNodes 프로퍼티가 반환하는 NodeList 객체는 live 객체로 동작한다!!
- 노드 객체의 상태 변경과 상관없이 DOM 컬렉션을 이용할 경우 모두 배열로 변환해 사용을 권장한다. 이는 스프레드 문법이나 `Array.from` 메서드 이용시 가능하다. 혹은 배열의 유용한 고차 함수를 사용한다.

## 3. 노드 탐색

요소 노드를 취득한 다음, 취득한 요소 노드를 기점으로 DOM 트리의 노드를 옮겨 다니며 부모, 형제, 자식 노드 등을 탐색해야할 때가 있다.

### 공백 텍스트 노드

HTML 요소 사이의 스페이스, 탭, 줄빠굼 등의 공백(white space) 문자는 텍스트 노드를 생성한다. 이를 공백 텍스트 노드라고 한다.

노드 탐색 시 공백 문자가 생성한 공백 텍스트 노드를 주의해야 한다.

### 자식 노드 탐색

다음과 같은 노드 탐색 프로퍼티를 이용해 자식 노드를 탐색한다.

| 프로퍼티 | 설명 |
| --- | --- |
| Node.prototype.childNodes | 자식노드를 모두 탐색해 DOM 컬렉션 객체인 NodeList에 담아 반환. childNodes 프로퍼티가 반환한 NodeList에는 요소 노드 뿐만 아니라 텍스트 노드도 포함되어 있을 수 있음 |
| Element.prototype.children | 자식 노드 중에서 요소 노드만 모두 탐색해 DOM 컬렉션 객체인 HTMLCollection에 담아 반환한다. children 프로퍼티가 반환한 HTMLCollection에는 텍스트 노드 포함 X |
| Node.prototype.firstChild | 첫 번째 자식 노드를 반환함. firstChild 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드 |
| Node.prototype.lastChild | 마지막 자식 노드를 반환. lastChild 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드 |
| Element.prototype.firstElementChild | 첫 번째 자식 요소 노드 반환. firstElementChild 프로퍼티는 요소 노드만 반환 |
| Element.prototype.lastElementChild | 마지막 자식 요소 노드 반환. lastElementChild 프로퍼티는 요소 노드만 반환 |

### 자식 노드 존재 확인

`Node.prototype.hasChildNodes` 메서드를 이용해 존재시 `true`를 반환한다.

이때, 이는 childNodes 프로퍼티와 마찬가지로 텍스트 노드를 포함해 자식 노드의 존재를 확인한다.
만약 텍스트 노드가 아닌 요소 노드가 존재하는지 확인하고 싶다면 `hasChildNodes` 메서드 대신 `children.length` 또는 Element 인터페이스의 `childElementCount` 프로퍼티를 사용한다.

### 요소 노드의 텍스트 노드 탐색

요소 노드의 텍스트 노드는 요소 노드의 자식노드이므로 요소 노드의 텍스트 노드는 `firstChild` 프로퍼티로 접근한다.

### 부모 노드 탐색

부모 노드를 탐색하려면 `Node.prototype.parentNode` 프로퍼티를 사용한다.

### 형제 노드 탐색

`Node.prototype.previousSibling, Node.prototype.NextSibling, Element.prototype.previousElementSibling, Element.prototype.nextElementSibling` 메서드가 있다.

## 4. 노드 정보 취득

노드 객체에 대한 정보를 취득하고 싶다면 다음과 같은 노드 정보 프로퍼티를 사용한다.

- `Node.prototype.nodeType`
  - 노드 객체의 종류. 즉, 노드 타입을 나타내는 상수를 반환한다. 노드 타입 상수는 Node에 정의되어 있다.
    - Node.ELEMENT_NODE: 요소 노드 타입을 나타내는 상수 1 반환
    - Node.TEXT_NODE: 텍스트 노드 타입을 나타내는 상수 3 반환
    - Node.DOCUMENT_NODE: 문서 노드 타입을 나타내는 상수 9 반환
- `Node.prototype.nodeName`
  - 노드 이름을 문자열로 반환한다.
    - 요소 노드: 대문자 문자열로 태그 이름('UL', 'LI' 등) 반환
    - 텍스트 노드: 문자열 '#text' 반환
    - 문서 노드: 문자열 '#document' 반환

## 5. 요소 노드의 텍스트 조작

### nodeValue

- `Node.prototype.nodeValue` 프로퍼티를 활용한다.
  - setter, getter 모두 존재하는 접근자 프로퍼티이며, 노드 객체의 값을 반환한다.(텍스트 노드의 텍스트)
  - 텍스트 노드가 아닌 노드 (문서 노드 or 요소 노드)의 nodeValue 프로퍼티를 참조할 경우 `null`이 반환된다.

### textContent

- `Node.prototype.textContent` 프로퍼티
  - setter, getter 모두 존재하는 접근자 프로퍼티이며, 요소 노드의 텍스트와 모든 자손 노드의 텍스트를 모두 취득하거나 변경한다.
  - 요소 노드의 textContent 프로퍼티를 참조할 경우 요소 노드의 콘텐츠 영역 내의 텍스트를 모두 반환한다.
  - 요소 노드의 textContent 프로퍼티에 문자열을 할당할 경우 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가된다.

## 6. DOM 조작

DOM 조작은 새로운 노드를 생성해 DOM에 추가하거나 기존 노드를 삭제 또는 교체하는 것이다. DOM 조작에 의해 DOM에 새로운 노드가 추가되거나 삭제되면 리플로우와 리페인트가 발생하므로 성능에 영향을 준다.
그러니 조심하자!

### innerHTML

`Element.prototype.innerHTML` 프로퍼티를 쓴다.
- setter, getter 모두 존재하는 접근자 프로퍼티
- 요소 노드의 HTML 마크업을 취득하거나 변경한다.
- HTML 마크업이 포함된 문자열을 그대로 반환한다.

__단점__
- 사용자로부터 입력받은 데이터를 그대로 innerHTML 프로퍼티에 할당하는 것은 크로스 사이트 스크립팅 공격(XSS)에 취약하므로 위험한다. HTML 마크업 내에 JS 악성 코드가 포함되어 있다면 파싱 과정에서 그대로 실행되어버린다.
  👉 때문에 HTML Sanitization 방식을 활용하는 게 좋다.
- 요소 노드의 innerHTML 프로퍼티에 HTML 마크업 문자열을 할당하는 경우 요소 노드의 모든 자식 노드를 제거하고 할당한 HTML 마크업 문자열을 파싱하여 DOM을 변경한다는 단점도 있다. (비효율적)
- 새로운 요소를 삽입할 때 삽입될 위치를 지정할 수 없다. 

### insertAdjacentHTML 메서드

`Element.prototype.insertAdjacentHTML (position, HTML markup string)` 메서드

- 기존의 요소를 제거하지 않고 위치를 지정해 새로운 요소를 삽입할 수 있다.
👉 position(문자열): 'beforebegin', 'afterbegin', 'beforeend', 'afterend'

__장점__

- innerHTML 프로퍼티보다 효율적이고 빠르다. 기존 요소에는 영향 없이 새롭게 삽입될 요소만을 파싱해 자식 요소로 추가하기 때문이다.
  - innerHTML은 기존 자식 노드를 모두 제거하고 다시 처음부터 새롭게 자식 노드를 생성해 자식 요소로 추가한다.
 
__단점__

- HTML 마크업 문자열을 파싱하므로 크로스 사이트 스크립팅 공격에는 여전히 취약하다.

### 노드 생성과 추가

__요소 노드 생성__

> `document.prototype.createElement(tagName)`

__텍스트 노드 생성__

> `document.prototype.createTextNode(text)`

__요소노드를 DOM에 추가__

> `Node.prototype.appendChild(childNode)`

### 복수의 노드 생성과 추가

DOM 을 변경하는 것은 높은 비용이 드므로 요소 노드를 반복하여 추가하는 것은 비효율적이다. 때문에 추가해야할 요소를 컨테이너 `DocumentFragment` 요소의 자식 노드로 추가하고, 해당 컨테이너 요소를 실제 돔에 넣어주는 방식을 사용한다.

`document.createDocumentFragment()`
- 부모 노드가 없어 기존 DOM 과는 별개로 존재한다.
- 컨테이너 요소와 같이 별도의 서브 DOM 을 구성하여 기존 DOM 에 추가하기 위한 용도로 사용한다.
- 실제 DOM 변경은 한번 뿐이며 리플로우와 리페인트도 한 번만 실행된다.
- DocumentFragment 노드를 DOM에 추가하면 자신은 제거되고 자신의 자식 노드만 DOM에 추가된다.

