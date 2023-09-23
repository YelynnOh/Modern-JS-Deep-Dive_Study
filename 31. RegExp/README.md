# 31. RegExp

## 1. 정규 표현식이란?

정규 표현식(Regular Expression)은 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 형식 언어(foraml language)이다. 정규 표현식은 JS의 고유 문법은 아니다.

__정규 표현식은 문자열을 대상으로 패턴 매칭 기능을 제공한다.__ 이는 특정 패턴과 일치하는 문자열을 검색하거나 추출 또는 치환할 수 있는 기능을 말한다.

```js
//사용자로부터 입력받은 휴대폰 전화번호
const tel = `010-1234-123엥`;

//정규 표현식 리터럴로 휴대폰 전화번호 패턴 정의: 숫자3개 - 숫자4개 - 숫자4개
const RegExp = /^\d{3}-\d{4}-\d{4}$/;

//tel이 전화번호 패턴에 매칭하는지 테스트함
regExp.test(tel); //false
```

_반복문과 조건문 없이 패턴을 정의하고 테스트하는 것으로 간단하게 체크할 수 있다는 장점_

_하지만 정규표현식은 주석이나 공백을 허용하지 않고 여러 기호를 혼합해 사용하기 떄문에 가독성이 좋지 않다._

## 2. 정규 표현식 생성

정규 표현식 객체(RegExp 객체)를 생성하기 위해 정규 표현식 리터럴과 RegExp 생성자 함수를 사용할 수 있다. 일반적으로는 정규 표현식 리터럴을 사용한다.

> `/regexp/i`
> `/`은 시작과 종료 기호, `regexp`은 패턴(pattern), `i`는 플래그

```js
const target = 'Is this all there is?'.

//패턴: is
//플래그 : i
const regexp = /is/i;

//test 메서드는 target 문자열에 대해 정규 표현식 regexp의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환
regexp.test(target); //true
```

RegExp 생성자 함수를 사용해 RegExp 객체를 생성할 수도 있다. 

```js
const target = 'Is this all there is?'.

const regexp = new RegExp(/is/i); //ES6
//const regexp = new RegExp(/is/, 'i'); 
//const regexp = new RegExp('is', 'i'); 

regexp.test(target); //true
```

RegExp 생성자 함수 사용 시 변수를 사용해 동적으로 RegExp 객체를 생성할 수 있다.

```js
const count = (str, char) => (str.match(new RegExp(char, 'gi')) ?? []).length;

count('Is this all there is?', 'is'); //3
count('Is this all there is?, 'xx'); //0
```

## 3. RegExp 메서드

정규표현식을 사용하는 메서드는 `RegExp.prototype.exec, RegExp.prototype.test, String.prototype.match, String.prototype.replace, String.prototype.search, String.prototype.split` 등이 있다.

### RegExp.prototype.exec

`exec` 메서드는 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색해 매칭 결과를 배열로 반환한다. 매칭 결과가 없는 경우 null을 반환한다. 

```js
const target = 'Is this all there is?';
const regExp = /is/;

target.exec(regExp); // ["is", index: 5, input: "Is this all there is?", groups: undefined]
```
exec 메서드는 첫 번째 매칭 결과만 반환한다.

### RegExp.prototype.test

`test` 메서드는 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.

```js
const target = 'Is this all there is?';
const regExp = /is/;

target.test(target); // true. is가 있으니깐
```

### String.prototype.match

String 표준 빌트인 객체가 제공하는 match 메서드는 대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환한다.

```js
const target = 'Is this all there is?';
const regExp = /is/;

target.match(target); // ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

```js
const target = 'Is this all there is?';
const regExp = /is/g;
//exec 메서드의 경우 문자열 내의 모든 패턴을 검색하는 g 플래그를 지정해도 첫 번째 매칭 결과만 반환하지만 .match 메서드는 g 플래그 지정 시 모든 매칭 결과를 배열로 반환한다.

target.match(regExp); // -> ["is", "is"]
```

## 4. 플래그 

| 플래그 | 의미 | 설명 |
| --- | --- | --- |
| i | Ignore Case | 대소문자를 구별하지 않고 패턴 검색 |
| g | Global | 대상 문자열 내에서 패턴과 일치하는  모든 문자열을 전역 검색 |
| m | Multi line | 문자열의 행이 바뀌더라도 패턴 검색을 계속함 |

- 플래그는 옵션이므로 선택적으로 사용할 수 있음
- 순서와 상관없이 하나 이상의 플래그를 동시에 설정한다
- 어떠한 플래그를 사용하지 않은 경우 대소문자를 구별해 패턴을 검색한다. 그리고 문자열에 패턴 검색 매칭 대상이 1개 이상 존재해도 첫 번째 매칭한 대상만 검색하고 종료한다.

```js
const target = 'Is this all there is?';

// target 문자열에서 is 문자열을 대소문자를 구별하여 한 번만 검색한다.
target.match(/is/);
// ["is", index: 5, input: "Is this all there is?", groups: undefined]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 한 번만 검색한다.
target.match(/is/i);
// ["Is", index: 0, input: "Is this all there is?", groups: undefined]

// target 문자열에서 is 문자열을 대소문자를 구별하여 전역 검색한다.
target.match(/is/g);
// ["is", "is"]

// target 문자열에서 is 문자열을 대소문자를 구별하지 않고 전역 검색한다.
target.match(/is/ig);
// ["Is", "is", "is"]
```

## 5. 패턴

정규 표현식은 `일정한 규칙(패턴)을 가진 문자열의 집합을 표현하기위해 사용하는 형식 언어이다. 이는 패턴과 플래그로 이뤄진다.`
패턴이 _문자열의 일정한 규칙을 표현하기 위해 사용된다면,_ 플래그는 _정규 표현식의 검색 방식을 설정하기 위해 사용된다._

- 패턴은 `/`으로 열고 닫으며 문자열의 따옴표는 생략한다. 따옴표 포함 시 따옴표까지도 패턴에 포함되어 검색된다.
- 특별한 의미를 가지는 메타문자(meta character) 또는 기호로 표현할 수 있다.
- 어떤 문자열 내에 패턴과 일치하는 문자열이 존재할 때 _정규 표현식과 매치(match)한다_ 고 표현한다.

## 6. 자주 사용하는 정규 표현식

### 1) 특정 단어로 시작하는지 검사

```js
const url = 'https://example.com';

// 'http://' 또는 'https://'로 시작하는지 검사한다.
/^https?:\/\//.test(url); // true

// 다른 방식의 검사
// ^ 은 문자열의 시작을 의미하고, ? 은 앞선 패턴이 최대 한 번 이상 반복되는지 의미(그니깐 0, 1번 보이냐)
/^(http|https):\/\//.test(url); // true
```

### 2) 특정 단어로 끝나는지 검사

```js
const fileName = 'index.html';

// 'html'로 끝나는지 검사한다.
/html$/.test(fileName); // -> true
```

### 3) 숫자로만 이뤄진 문자열인지 검사

```js
const target = '12345';

//숫자로만 이뤄진 문자열인지 검사. $은 문자열의 마지막을 의미한다. \d는 숫자를 의미하고, +는 앞선 패턴이 최소 한 번 이상 반복되는 문자열을 의미한다.
// 즉, 처음과 끝이 숫자이고 최소 한 번 이상 반복되는 문자열과 매치한다.
/^\d+$/.test(target)/; //true
```

### 4) 하나 이상의 공백으로 시작하는지 검사

```js
const target = ' Hi!';

// 하나 이상의 공백으로 시작하는지 검사한다.
/^[\s]+/.test(target); // true
```

### 5) 아이디로 사용 가능한지 검사

```js
const id = 'abc123';

// 알파벳 대소문자 또는 숫자로 시작하고 끝나며 4~10자리인지 검사한다.
/^[A-Za-z0-9]{4,10}$/.test(id); // true
```

### 6) 메일 주소 형식에 맞는지 검사

```js
const email = 'goww1357@gmail.com'

/^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/.test(email); // true
```

### 7) 핸드폰 번호 형식에 맞는지 검사

```js
const cellphone = '010-5033-1643';

/^\d{3}-\d{3,4}-\d{4}$/.test(cellphone); // true
```

### 8) 특수 문자 포함 여부 검사

```js
const target = "abc#123";

// A-Za-z0-9 이외의 문자가 있는지 검사한다.
/[^A-Za-z0-9]/gi.test(target); // true
```

--- 

## 📌 추가 덧

얼마전에 회원가입 페이지를 구현할 일이 있었는데 그때 리액트 훅 폼이랑 yup을 사용할 때 정규표현식을 사용했었습니다!
이런 식으로 정규 표현식이 활용되는구나.. 라고 봐주시면 좋을 것 같아요
```js
const validationSchema = yup.object({
    name: yup.string().required('성명을 입력해주세요!'),
    password: yup
        .string()
        .required('비밀번호를 입력해주세요!')
        .min(6, '비밀번호는 6글자 이상이여야 합니다.')
        .matches(
            /^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{6,}$/,
            '비밀번호는 영어 대소문자, 숫자, 특수문자(@$!%*#?&)를 포함해야 합니다.',
        ),
    phone: yup.string().required('전화번호를 입력해주세요!'),
    email: yup.string().required('이메일을 입력해주세요!').email('@를 포함한 유효한 이메일 주소를 작성해주세요.'),
});
```
