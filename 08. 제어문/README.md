## 8. 제어문

보통 코드는 위에서 아래 방향으로 순차적으로 실행이 되지만 제어문을 사용하면 코드으 실행 흐름을 인위적으로 제어할 수 있다. 

👀 제어문은 코드의 흐름을 인위적으로 방해하는 것으로 가독성을 해치는데, `forEach, map, filter, reduce`와 같은 고차 함수는 제어문 사용을 억제해 복잡성을 해결한다.

### 1) 블록문

0개 이상의 문을 중괄호로 묶은 것으로, 코드 블록 또는 블록이라 부른다. 
JS는 블록문 하나를 실행 단위로 취급한다. 

```javascript
// 블록문
{
    var foo = 10;
}

// 제어문
var x = 1;
if (x < 10) {
    x++;
}

// 함수 선언문
function sum(a, b) {
    return a + b;
}
```

### 2) 조건문

__주어진 조건식의 평가 결과에 따라 코드 블록(블록문)의 실행을 결정하는 문__

이때 조건식은 __불리언 값으로 평가될 수 있는 표현식__

__[1] `if ... else`문__

- 주어진 조건식(불리언 값으로 평가되는 표현식)의 평가 결과, 즉, 논리적 참/거짓에 따라 실행할 코드 블록을 결정한다.

- if문의 조건식은 불리언 값으로 평가되어야 한다. 만약 불리언 값이 아닌 값으로 평가될 경우 JS 엔진이 암묵적으로 불리언 값으로 강제 변환해 실행할 코드 블록을 결정한다. 

```javascript

//else if와 else문은 옵션임. 코드 블록 내의 문이 하나라면 중괄호 생략 가능. 
if (조건식1){
  //조건식1이 참이면 코드 블록 실행
}

else if(조건식2){
  // 조건식2가 참이면 이 코드 블록 실행
}

else if(조건식3){
  // 조건식3이 참이면 이 코드 블록 실행
}

else{
  // 조건식1과 조건식2과 조건식3이 모두 거짓이면 이 코드 블록 실행
}
```
- 대부분 `if... else`문은 삼항 조건 연산자로 바꿔 쓸 수 있다.
  - `if ... else` 문은 표현식이 아닌 문이기 떄문에 변수 할당이 불가능하다. 하지만 조건에 따라 실행할 내용이 많을 때 사용이 된다.
  - 삼항 조건 연산자 표현식은 값처럼 사용할 수 있기 때문에 변수에 할당할 수 있다. 또, 간결하여 가독성이 좋다는 장점이 있다. 

```javascript
//if else문 사용한 경우
var num = 2;
var kind;

if(num > 0) kind = '양수';
else if(num < 0) kind = '음수';
else kind = '영';

console.log(kind); //양수

//삼항 조건 연산자로 바꿔쓴 경우
var num = 2;
var kind = num ? (num > 0 ? '양수' : '음수') : '영';

console.log(kind); //양수
```

__[2] `switch`문__

`switch`문은 주어진 표현식을 평가하여 그 값과 일치하는 표현식을 갖는 case문으로 실행 흐름을 옮긴다. 

이때 조건식은 __문자열이나 숫자 값인 경우가 많다.__ (`if ... else`는 불리언 값)
고려해야 하는 조건이 많을 때는 `switch`문을 사용하여 가독성을 높일 수 있지만, 보통 `if ... else`를 사용하는 것이 좋다. 

```javascript
switch(표현식){
  case 표현식1:
    //표현식과 표현식1이 일치하면 실행이 됨
    break;
  case 표현식2:
    //표현식과 표현식2가 일치하면 실행이 됨
    break;
  default:
   //표현식과 일치하는 case문이 없을 때 실행이 됨. 이때, default은 옵션 사항이고, break문이 따로 필요하지 않다. 
}
```

📌 __폴스루(fall through) 현상__
- `switch`문을 사용할 때는 폴스루 현상을 조심해야 한다.
- 이는 case 문의 마지막에 break문을 넣어주지 않아 코드 블록에서 탈출하지 않고 다음 case로 넘어가 마지막 default까지 실행이 되는 현상을 의미한다.
  ```javascript
  var month = 11;
  var monthName;

  switch (month) {
      case 1: monthName = 'Jan';
      case 2: monthName = 'Feb';
      case 3: monthName = 'Mar';
      case 4: monthName = 'Apr';
      case 5: monthName = 'May';
      case 6: monthName = 'Jun';
      case 7: monthName = 'Jul';
      case 8: monthName = 'Aug';
      case 9: monthName = 'Sep';
      case 10: monthName = 'Oct';
      case 11: monthName = 'Nov';
      case 12: monthName = 'Dec';
      default: monthName = 'Invalid month';
  }

  console.log(monthName); // Invalid month
  //monthName 변수에 'Nov'가 할당된 후 switch문을 탈출하지 못했기 때문에 'Dec' > 'Invalid Month'가 재할당된다. 폴스루의 대표적인 사례!
  ```
- 때문에 폴스루 현상을 막기 위해서는 `break`문을 꼭 넣어줘야 한다. `break`문을 넣어줘야 코드 블록에서 탈출이 가능해 원하는 결과를 얻을 수 있다. 
- 이때, 여러 개의 case 문을 하나의 조건으로 사용하는 경우 break 문을 생략한 폴스루가 유용할 때도 있다.
  
  ```javascript
  var year = 2000; // 2000년은 윤년으로 2월이 29일이다.
  var month = 2;
  var days = 0;
  
  switch (month) {
      case 1: case 3: case 5: case 7: case 8: case 10: case 12:
          days = 31;
          break;
      case 4: case 6: case 9: case 11:
          days = 30;
          break;
      case 2:
          // 윤년 계산 알고리즘
          // 1. 연도가 4로 나누어 떨어지는 해는 윤년이다.
          // 2. 연도가 4로 나누어 떨어지더라도 100으로 나누어 떨어지는 해는 윤년이 아니다.
          // 3. 연도가 400으로 나누어 떨어지는 해는 윤년이다.
          days = ((year % 4 === 0 && year % 100 !== 0) || (year % 400 === 0)) ? 29 : 28;
          break;
      default:
          console.log('Invalid month');
  }
  
  console.log(days); // 29
  ```

### 3) 반복문

조건식의 평가 결과가 참인 경우 코드 블록을 실행한다. 조건식이 거짓일 때까지 반복이 된다. 

👀 _반복문을 대체하는 기능들_
- forEach 메서드: 배열 순회 시 사용
- for...in문: 객체의 프로퍼티 열거 시 사용
- for...of문: ES6에 도입되어 이터러블을 순회할 수 있는 기능

### for문

조건식이 거짓으로 평가될 때까지 코드 블록을 반복 실행한다. 

```javascript
//실행 순서: (1) 초기값 확인 > (2) 조건식 만족 여부 확인 > (3) 코드 블록 속 문 실행 > (4) 증감식 > (5) 조건식 확인 > (6) 문 실행 ...
for(var i = 0; i < 2; i++){
  console.log(i);
}
// 0 1

//for문의 변수 선언문, 조건식, 증감식은 모두 옵션이다. 모두 생략 시 무한 루프가 된다.
for(;;) { ... }

//중첩 for문도 가능하다!
for(var i = 1; i <= 6; i++){
  for(var j = 1; j <= 6; j++){
    if(i + j === 6) console.log(`$[i], $[j]`);
  }
}
// [1, 5] [2, 4] [3, 3] [4, 2] [5, 1] 
```

### while문

조건식의 평가 결과가 참이면 코드 블록을 계속해서 반복 실행한다. while문은 for문과 달리 반복 횟수가 불명확할 때 주로 사용된다.

__조건문의 평가 결과가 거짓이 되면 코드 블록을 실행하지 않고 종료한다. 조건식의 평가 결과가 불리언이 아니러면 타입 강제 변환이 일어난다.__

```javascript
while (조건식) {
    // 조건식이 참인 경우 반복 실행될 문;
}

// 무한 루프
while (true) {
    ...
}
// 무한 루프에서는 코드 내에 if 문으로 탈출 조건을 만들고 break 문으로 코드 블록을 탈출한다.
var count = 0;
// 무한 루프 with break문
while (true) {
    console.log(count);
    count++;
    // count가 3이면 코드 블록을 탈출한다.
    if (count === 3) break;
} // 0 1 2

```

### do while문

__코드 블록을 먼저 실행하고 조건식을 평가한다. 따라서 코드 블록은 무조건 한 번 이상 실행된다.__

```javascript
var count = 8;

//count가 3보다 작을 때까지 코드 블록을 계속 반복 실행한다.
do{
  console.log(count);
  count++;
} while(count < 3);

//출력 결과는 8. 조건식이 평가되기 전에 코드 블록이 한 번 실행이 되어 콘솔이 찍히고 count는 9가 된다.
```

### break문

__레이블 문, 반복문(`for, for...in, for...of, while, do...while`), `switch`문의 코드 블록을 탈출하는 기능을 가진다.__
이 외에 코드 블록에 break문을 활용할 경우 SyntaxError(문법에러)가 발생한다. 

📌 레이블 문이란?
  - 식별자가 붙은 문을 말한다.
  - `switch`문의 case문과 default문도 레이블 문의 예시이다.
  ```javascript
  foo: {
    console.log(1);
    break foo; //foo 레이블 블록문 탈출! break문에 정확한 레이블 식별자를 지정해줘야 한다.
    console.log(2);
  }
  //1만 출력된다.
  ```
  - 중첩된 for문의 내부 for문에서 break문 실행 시 내부 for문만 탈출한다. 하지만 이는 코드 흐름의 복잡도를 높이고 가독성이 나빠져 오류 발생 가능성이 높아진다.

  ```javascript
  // outer라는 식별자가 붙은 레이블 for 문
  outer: for (var i = 0; i < 3; i++) {
      for (var j = 0; j < 3; j++) {
          // i + j === 3이면 outer라는 식별자가 붙은 레이블 for 문을 탈출한다.
          if (i + j === 3) {
              break outer;
          }
          console.log(`inner [${i}, ${j}]`);
      }
  }
  
  console.log('Done!')
  ```

-  레이블 문 말고 반복문, switch문에서도 활용이 된다. 이때는 _break문에 레이블 식별자를 지정하지 않는다!_
  
```javascript
//문자열에서 특정 문자의 인덱스(위치) 검색하는 코드

var string = 'Hello World!';
var search = 'l';
var index;

//문자열은 유사 배열이므로 for문으로 순회가능하다.
for(var i = 0; i < string.length; i++){
  //문자열의 개별 문자가 'l'이면
  if(string[i] === search){
    index = i;
    break; //for문 탈출
  }
}

console.log(index); //2
```
### continue문

반복문의 코드 블록 실행을 __현 지점에서 중단하고 반복문의 증감식으로 실행 흐름을 이동시킨다.__
만약 if문 내에서 실행할 코드가 길다면 들여쓰기가 한 단계 더 깊어지므로 continue문을 사용하는 게 가독성이 좋다.
```javascript
var string = "Hello World"
var search = "l"
var count = 0;

// continue 문을 사용하지 않으면 if 문 내에 코드를 작성해야 한다.
for (var i = 0; i < string.length; i++) {
  //'l'이면 count 증가
  if (string[i] === search) {
    count++;
    //code
  }
}

var count = 0;
// continue 문을 사용하면 if 문 밖에 코드를 작성할 수 있다.
for (var i = 0; i < string.length; i++) {
  //'l'이 아니면 count 증가시키지 않음. continue문을 쓰니 한 단계 깊게 들어가지 않아도 된다.
  if (string[i] !== search) continue;
  count++;
  //code
}
```



