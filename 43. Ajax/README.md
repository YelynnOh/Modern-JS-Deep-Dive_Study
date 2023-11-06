# Ajax

## 1. Ajax란?

Ajax(Asynchronous Javascript and XML)란 JS를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신해 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말한다. 

### Ajax 등장 배경

예전 웹페이지는 html 태그로 시작해서 html 태그로 끝나는 완전한 HTML을 서버로부터 전송받아 웹페이지 전체를 _처음부터 다시 렌더링하는 방식으로 동작했다._ 때문에 아래와 같은 단점이 존재했다.

1. 변경할 필요가 없는 부분까지 포함된 완전한 HTML을 서버로부터 매번 다시 전송받기 때문에 불필요한 데이터 통신 발생
2. 변경할 필요가 없는 부분까지 처음부터 다시 렌더링! 이로 인해 화면 전환 시 화면이 순간적으로 깜박이는 현상 발생
3. 클라-서버 간 통신이 동기 방식으로 작동하기 때문에 서버로부터 응답있을 때까지 다음 처리 블로킹

하지만 Ajax가 등장하고부터는 __서버로부터 웹페이지의 변경에 필요한 데이터만 비동기 방식으로 전송받아 웹페이지를 변경할 필요가 없는 부분은 다시 렌더링하지 않고, 변경할 필요가 있는 부분만 한정적으로 렌더링하는 방식이 가능해졌다.__ 때문에 아래와 같은 장점이 있다.

1. 변경할 부분을 갱신하는 데 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신이 발생하지 않음
2. 변경할 필요가 없는 부분은 다시 렌더링하지 않음. 따라서 화면이 순간적으로 깜박이는 현상이 발생하지 않음.
3. 클라-서버 간 통신이 비동기 방식으로 동작하기 때문에 서버에게 요청을 보낸 후 블로킹 발생 X

## 2. JSON

JSON(Javascript)은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다. 

### JSON 표기 방식

```json
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```

_키와 문자열은 반드시 큰따옴표로 묶기!_

### JSON.stringify (for 직렬화)

__객체 > JSON 포맷의 문자열로 변환해준다.__ 

클라이언트가 서버로 객체를 전송하려면 객체를 _문자열화_ 해야 하는 직렬화 과정이 필요한데, 이때 사용이 된다.

```js
const obj = {
    name: 'Lee',
    age: 20,
    alive: true,
    hobby: ['traveling', 'tennis']
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);
console.log(typeof json, json);
// string {"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}

// 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기 한다.
const prettyJson = JSON.stringify(obj, null, 2);
console.log(typeof prettyJson, prettyJson);
/*
string {
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}
*/

// replacer 함수. 값의 타입이 Number이면 필터링되어 반환되지 않는다.
function filter(key, value) {
    // undefined: 반환하지 않음
    return typeof value === 'number' ? undefined : value;
}

// JSON.stringify 메서드에 두 번째 인수로 replacer 함수를 전달한다.
const strFilteredObject = JSON.stringify(obj, filter, 2);
console.log(typeof strFilteredObject, strFilteredObject);
/*
string {
  "name": "Lee",
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}
*/
```

객체 뿐만 아니라 배열도 JSON 포맷의 문자열로 변환한다.

```js

const todos = [
    { id: 1, content: 'HTML', completed: false },
    { id: 2, content: 'CSS', completed: true },
    { id: 3, content: 'Javascript', completed: false }
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos, null, 2);
console.log(typeof json, json);
/*
string [
  {
    "id": 1,
    "content": "HTML",
    "completed": false
  },
  {
    "id": 2,
    "content": "CSS",
    "completed": true
  },
  {
    "id": 3,
    "content": "Javascript",
    "completed": false
  }
]
*/
```

### JSON.parse (for 역직렬화 - deserializing)

__JSON 포맷의 문자열 > 객체로 변환__

서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열인데, 이 문자열을 객체로서 사용하려면 JSON 포맷의 문자열을 객체화하는 _역직렬화 과정을 거친다._

```js
const obj = {
    name: 'Lee',
    age: 20,
    alive: true,
    hobby: ['traveling', 'tennis']
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);

// JSON 포맷의 문자열을 객체로 변환한다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
// object {name: "Lee", age: 20, alive: true, hobby: ["traveling", "tennis"]}
```

배열의 요소가 객체인 경우 배열의 요소까지 객체로 변환한다.

```js
const todos = [
    { id: 1, content: 'HTML', completed: false },
    { id: 2, content: 'CSS', completed: true },
    { id: 3, content: 'Javascript', completed: false }
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos);

// JSON 포맷의 문자열을 배열로 변환한다. 배열의 요소까지 객체로 변환된다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
/*
 object [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'Javascript', completed: false }
]
*/
```

## 3. XMLHttpRequest

### XMLHttpRequest 객체 생성

생성자 함수를 호출해 생성할 수 있다. 해당 객체는 브라우저에서 제공하는 Web API이므로 브라우저 환경에서만 정상적으로 실행된다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();
```

### XMLHttpRequest 객체의 프로퍼티와 메서드

### HTTP 요청 전송

📌 __HTTP 요청 순서__
1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청 초기화
2. 필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값 설정
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청 전송

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('GET', '/users');

// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send();
```

#### `XMLHttpRequest.prototype.open` 

이는 서버에 전송할 HTTP 요청을 초기화한다. 

```js
xhr.open(method, url[, async])
```

- method : HTTP 요청 메서드("GET", "POST", "PUT", "DELETE")
- url : HTTP 요청을 전송할 URL
- async : 비동기 요청 여부. 옵션으로 기본값은 true이며, 비동기 방식으로 동작한다.

HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(리소스에 대한 행위)을 알리는 방법이다. 주로 5가지 요청 메서드(GET, POST, PUT, PATCH, DELETE 등)를 사용해 CRUD를 구현한다.

#### `XMLHttpRequest.prototype.send`

send 메서드는 open 메서드로 초기화된 HTTP 요청을 서버에 전송한다. 기본적으로 서버로 전송하는 데이터는 GET, POST 요청 메서드에 따라 전송 방식에 차이가 있다.

- GET : 데이터를 URL의 일부분인 쿼리 문자열로 서버에 전송한다.
- POST : 데이터(페이로드)를 요청 몸체(request body)에 담아 전송한다.

send 메서드에는 요청 몸체에 담아 전송할 데이터(페이로드)를 인수로 전달할 수 있다. 페이로드가 객체인 경우 반드시 `JSON.stringfy `메서드를 사용하여 직렬화한 다음 전달해야 한다.

```js
xhr.send(JSON.stringify({ id: 1, content: 'HTML', completed: false }));
```

#### `XMLHttpRequest.prototype.setRequestHeader`

특정 HTTP 요청의 헤더 값을 설정한다. setRequestHeader 메서드는 반드시 open 메서드를 호출한 이후에 호출해야 한다.

`Content-type`은 요청 몸체에 담아 전송할 데이터의 MIME 타입의 정보를 표현한다. 

### HTTP 응답 처리

서버가 전송한 응답을 처리하기 위해서는 `XMLHttpRequest` 객체가 발생시키는 이벤트를 캐치해야 한다. `XMLHttpRequest` 객체는 `onreadystatechange, onload, onerror`와 같은 이벤트 핸들러 프로퍼티를 갖는다.

이 중 `readyState` 프로퍼티 값이 변경된 경우 발생하는 `readystatechange` 이벤트를 캐치해 HTTP 응답을 처리할 수 있다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// https://jsonplaceholder.typicode.com은 Fake REST API를 제공하는 서비스다.
xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

// HTTP 요청 전송
xhr.send();

// readystatechange 이벤트는 HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티가
// 변경될 때마다 발생한다.
xhr.onreadystatechange = () => {
    // readyState 프로퍼티는 HTTP 요청의 현재 상태를 나타낸다.
    // readyState 프로퍼티 값이 4(XMLHttpRequest.DONE)가 아니면 서버 응답이 완료되지 상태다.
    // 만약 서버 응답이 아직 완료되지 않았다면 아무런 처리를 하지 않는다.
    if (xhr.readyState !== XMLHttpRequest.DONE) return;

    // status 프로퍼티는 응답 상태 코드를 나타낸다.
    // status 프로퍼티 값이 200이면 정상적으로 응답된 상태이고
    // status 프로퍼티 값이 200이 아니면 에러가 발생한 상태다.
    // 정상적으로 응답된 상태라면 response 프로퍼티에 서버의 응답 결과가 담겨 있다.
    if (xhr.status === 200) {
        console.log(JSON.parse(xhr.response));
        // {userId: 1, id: 1, title: "delectus aut autem", completed: false}
    } else {
        console.error('Error', xhr.status, xhr.statusText);
    }
};
```
send 메서드를 통해 HTTP 요청을 서버에 전송하면 서버는 응답을 반환한다. 하지만 언제 응답이 클라이언트에 도달할지는 알 수 없다. 따라서 `readystatechange` 이벤트를 통해 HTTP 요청의 현재 상태를 확인해야 한다. `readystatechange` 이벤트는 HTTP 요청의 현재 상태를 나타내는 `readyState` 프로퍼티가 변경될 때마다 발생한다.

`onreadystatechange` 이벤트 핸들러 프로퍼티에 할당한 이벤트 핸들러는 HTTP 요청의 현재 상태를 나타내는 `xhr.readyState` 가 `XMLHttpRequest.DONE` 인지 확인하여 서버의 응답이 완료되었는지 확인한다.

서버의 응답이 완료되면 HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는` xhr.status`가 200인지 확인하여 정상 처리와 에러 처리를 구분한다. HTTP 요청에 대한 응답이 정상적으로 도착했다면 요청에 대한 응답 몸체를 나타내는 `xhr.response`에서 서버가 전송한 데이터를 취득한다. 만약 `xhr.status` 가 200이 아니면 에러가 발생한 상태이므로 필요한 에러 처리를 한다.

`readystatechange` 이벤트 대신 load 이벤트를 캐치해도 좋다. load 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생한다. 따라서 load 이벤트를 캐치하는 경우 `xhr.readyState`가 `XMLHttpRequest.DONE`인지 확인할 필요가 없다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
// https://jsonplaceholder.typicode.com은 Fake REST API를 제공하는 서비스다.
xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

// HTTP 요청 전송
xhr.send();

// load 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생한다.
xhr.onload = () => {
    // status 프로퍼티는 응답 상태 코드를 나타낸다.
    // status 프로퍼티 값이 200이면 정상적으로 응답된 상태이고
    // status 프로퍼티 값이 200이 아니면 에러가 발생한 상태다.
    // 정상적으로 응답된 상태라면 response 프로퍼티에 서버의 응답 결과가 담겨 있다.
    if (xhr.status === 200) {
        console.log(JSON.parse(xhr.response));
        // {userId: 1, id: 1, title: "delectus aut autem", completed: false}
    } else {
        console.error('Error', xhr.status, xhr.statusText);
    }
};
```
