# REST API

## 1. REST API의 구성

REST API는 자원(resource), 행위(verb), 표현(representation) 3가지 요소로 구성된다. 

| 구성 요소 | 내용 | 표현 방법 |
| --- | --- | --- |
| 자원 | 자원 | URI(엔드포인트) |
| 행위 | 자원에 대한 행위 | HTTP 요청 메서드 |
| 표현 | 자원에 대한 행위의 구체적 내용 | 페이로드 |

## 2. REST API 설계 원칙 

__URI는 리소스를 표현하는 데 집중하고, 행위에 대한 정의는 HTTP 요청 메서드를 통해 하는 것이 RESTful API를 설계하는 중심 규칙__

### 1. URI는 리소스를 표현

리소스를 식별할 수 있는 이름은 _동사보다 명사 사용_

```js
//bad
GET /getTodos/1
GET /todos/show/1

//good
GET /todos/1
```


### 2. 리소스에 대한 행위는 HTTP 요청 메서드로 표현

HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적(리소스에 대한 행위)을 알리는 방법

주로 5가지 요청 메서드(_GET, POST, PUT, PATCH, DELETE 등_)을 사용해 CRUD를 구현!

| HTTP 요청 메서드 | 종류 | 목적 | 페이로드 |
| --- | --- | --- | --- |
| GET | index/retrieve | 모든/특정 리소스 취득 | X |
| POST | create |  리소스 생성 | O |
| PUT | replace | 리소스의 전체 교체 | O |
| PATCH | modify | 리소스의 일부 수정 | O |
| DELETE | delete | 모든/특정 리소스 삭제 | X |

## 3. JSON Server를 이용한 REST API 실습

HTTP 요청을 전송하고 응답을 받기 위해서는 서버가 필요하다. JSON server를 사용해 가상 REST API 서버를 구축하여 HTTP 요청을 전송하고 응답을 받는 실습을 해볼 수 있는데, 과정은 다음과 같다.

### 1. JSON Server 설치

- npm을 통해서 JSON Server를 설치할 수 있음

> 📌 npm이란? <br> npm(node package manager)은 자바스크립트 패키지 매니저이다. __Node.js에서 사용할 수 있는 모듈들을 패키지화하여 모아둔 저장소 역할과 패키지 설치 및 관리를 위한 CLI를 제공__ 한다. 자신이 __작성한 패키지를 공개__ 할 수도 있고, __필요한 패키지를 검색하여 재사용__ 할 수도 있다. 

```bash
$ mkdir json-server-exam && cd json-server-exam
$ npm init -y
$ npm install json-server --save-dev
```

### 2. db.json 파일 생성

db.json 파일은 리소스를 제공하는 데이터베이스 역할을 함

### 3. JSON 서버 실행

터미널에서 JSON 서버를 실행시킬 수 있다. JSON 서버가 db.json (데이터베이스) 파일의 변경을 감지하게 하기 위해서는 `watch` 옵션을 추가함

```bash
### 기본 포트(3000) 사용 & watch 옵션 적용
$ json-server --watch db.json

### 포트 바꾸고 싶다면 port 옵션을 적용할 수 있음
$ json-server --watch db.json --port 5000
```

근데 매번 명령어를 입력하는 것도 번거로우니 `package.json` 파일의 `scripts`를 수정해 JSON 서버를 실행할 수 있음! 

```json
{
  "name": "json-server-exam",
  "version": "1.0.0",
  "scripts": {
    "start": "json-server --watch db.json"
  },
  "devDependencies": {
    "json-server": "^0.16.1"
  }
}
```

이후 `npm start`를 입력해서 JSON 서버를 실행할 수 있음!

### 4. 요청 보내기

#### GET 요청

todo 리소스 (db.json)에 있는 __모든 todo를 취득(index) 함__

todos 리소스에서 __id를 사용하여 특정 todo를 선택적으로 취득(retrieve)할 수 있음__

#### POST 요청

todos 리소스에 새로운 todo를 생성함. POST 요청 시에는 `setRequestHeader` 메서드를 이용해 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 함!

```html

<script>
  ...

  //요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입 지정
  xhr.setRequestHeader('content-type', 'application/json');

  //HTTP 요청 전송
  //새로운 todo를 생성하기 위해 페이로드를 서버에 전송해야 함
  xhr.send(JSON.stringify({id: 4, content: 'Angular', completed: false}));
  ...
</script>
```

#### PUT 요청

PUT은 __특정 리소스 전체를 교체할 때 사용함__

PUT 요청 시에는 POST와 마찬가지로 `setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 함

#### PATCH 요청

PATCH는 __특정 리소스의 일부를 수정할 때 사용함__

PATCH 요청 시에는 마찬가지로 `setRequestHeader` 메서드를 사용해 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정함. 

📌 __PUT vs PATCH__

PUT의 경우 리소스 모든 내용을 수정/변경한다. 예를 들어, 회원 정보(이름, 성별, 나이)가 적힌 표를 수정하기 위해 PUT 요청을 보낼때 이름만 넣어서 보낸다고 해보자. 그렇다면 해당 이름이 수정되고, 나머지 값(성별, 나이)는 `null` 처리가 되어 빈 값으로 수정이 된다. <br>
하지만 PATCH의 경우 일부 내용을 수정/변경한다. 따라서 같은 예제를 보았을 때 PATCH 요청을 보내면 그 전에 이미 있던 성별, 나이 정보는 유지된 채 이름만 변경이 된다.

#### DELETE 요청

todos 리소스에서 id를 사용해 todo를 삭제하는 역할을 함
