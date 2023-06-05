# this

> 사용되는 위치에 따라 this키워드에 바인드 된 객체가 달라진다.

# Destructruing

### Array Destructuring

일반적으로 배열에 저장된 요소에 접근하기 위해서는 배열에 저장된 순서인 인덱스 번호를 사용하게 된다.

```javascript
function getScores() {
	return [70, 80, 90];
}
let scores = getScores();
let x = scores[0]; // 배열의 인덱스 번호로 접근
let y = scores[1];
let z = scroes[2];
```

Array Destructuring은 배열에 저장된 요소를 분해해서 배열의 순서에 따라 변수를 정의해서 사용할 수 있게 해준다.

```javascript
let [x, y, z] = getScores();

console.log(x); // 70
console.log(y); //80
console.log(z); //90
```

활용: 위도, 경도 데이터를 반환받아 사용할 때. 일반적으로 경도 위도 정보를 계산할 수있는 거의 모든 외부 라이브러리들은 위도, 경도 정보를 배열 형태로 반환한다. 

```javascript
let [longitude, latitude] = getCoordinates();
//getCoordinates() 함수는 위도, 경도 정보를 제공하는 외부 라이브러리라 가정
console.log(longitude);
console.log(latitude);
```

배열에 반환하는 데이터가 많을 경우 특정 요소는 바로 요소에 할당하고, 나머지 요소는 모두 배열에 할당하는 것도 가능하다.

```javascript
let [x, y, ...args] = getScores();
console.log(x); // 70
console.log(y); // 80
console.log(args); // [90, 100]
```

배열 안에 배열이 있는 경우도 Array Destructuring을 사용해서 분해 가능하다.

```Javascript
function getProfile(){
	return [
		'김영희',
		'능곡초등학교',
		['1반', '2번'];
	];
}

let [
		name,
		school,
		[
			class,
			idNum
		]
] = getProfile();

console.log(class, idNum); // 1반, 2번
```

### Object Destructuring

# Scope

> 선언된 변수에 대한 접근성을 의미

# Default Function Parameter

# Rest Parameter

# Arrow Function

# Template Literals

# Object Literal Syntax Extension

# Spread Operator

# 내장객체

> 브라우저의 js 엔진에 내장된 객체. Object 객체는 모든 js 객체의 루트 객체. 
>
> - Object 객체
> - String 객체
> - Number 객체
> - Date 객체
> - Array 객체
> - Set 객체
> - Map객체
> - Math 객체
> - JSON 객체
> - Window 객체
> - Symbol 객체

### 4. Date객체

```javascript
let now = new Date(); // 사용자 브라우저의 시간을 가져옴
let d = new Date(2023, 6, 5, 10, 33, 30, 0); // 특정 날짜 시간을 지정해서 Date객체 생성
let d2 = new Date(0); //Thu Jan 01 1970 09:00:00 GMT+0900 (한국 표준시) 자바스크립트에서 날짜와 시간을 다루기 위해 사용되는 기준점인 에포크(epoch)-컴퓨터 시스템에서 시간을 측정하는 기준
let d3 = new Date(1000000000); // 1970년 1월1일 + 1000000000
let d4 = new Date("October 13, 2014 11:13:00"); // 날짜 문자열로 Date객체 생성
```

<details><summary>get함수, set함수</summary>
</details>

 - Date 객체에 내장되어있는 함수
   - getFullYear(): 4자리의 년도 가져옴
   - getMonth(): 0-11 사이의 월정보 가져옴. 1월은 0이고, 12월은 11
   - getDate(): 1~31 사이의 일정보 가져옴
   - getHours(): 0~23 사이의 시간 정보 가져옴
   - getMinutes(): 0~59 사이의 분 정보 가져옴
   - getSeconds(): 0~59 사이의 초 정보 가져옴
   - getMilliseconds() : 0~999 사이의 밀리초 정보 가져옴
   - getTime() : 1970년 1월 1일 이후에 해당하는 밀리초정보를 가져옴
   - getDay() : 0~6 사이의 요일 정보를 가져옴. 일요일은 0이고, 월요일은 1
   - Date.now() : 현재를 기준으로 getTime()함수에 해당하는 정보를 가져옴

### 5. Array 객체

#### sort()

> 배열에 문자형 데이터가 있는 경우 오름차순으로 정렬. 

```javascript
let fruits = ["Banana", "orange", "Apple", "Mango"];
fruits.sort();
```

> 배열 안에 숫자형 데이터가 있더라도, 기본적으로는 문자로 인식해 오름차순으로 정렬. 그래서 숫자값으로 정렬하려면, 정렬함수를 정의해서 사용해야함.
>
> sort()함수는 비교 함수의 반환 값을 기반으로 요소의 순서를 변경한다. 반환 값이 음수인 경우, `a`는 `b`보다 앞에 위치해야 함을 의미한다.  

```javascript
let points = [40, 100, 1, 5, 25, 10];
points.sort(function(a, b){return a - b});
```

> 정렬된 배열을 역순으로 정렬하고싶은 경우 

```javascript
let fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.sort();
fruitts.reverse();
```

> 실무에서는 Object의 특정 키를 기준으로 sort() 함수를 사용하는 경우가 많음

```javascript
    let persons = [
    {
      name: "김시나",
      point: 78,
      city: "서울"
    },
    {
      name: "양시나",
      point: 33,
      city: "서울"
    },
    {
      name: "최시나",
      point: 25,
      city: "서울"
    },
    {
      name: "우시나",
      point: 99,
      city: "제주"
    },
    ]

    let sortePerson = persons.sort(function(a, b){
        return a.point > b.point ? -1 : a.point < b.point ? 1: 0;
    })
    console.log(sortePerson);
```

#### reduce()

> 배열에 담긴 데이터를 하나씩 순회하며 callback 함수의 실행값을 누적하여 결과값을 반환. 누적 결과값은 숫자, 문자, 객체 모두 가능. reduce() 함수는 주로 배열에 담긴 데이터의 합계를 구하는 데 많이 사용. 배열에 담긴 데이터가 오브젝트인 경우는 누적 값을 구하고자 하는 오브젝트 키를 사용해 누적값을 구함.

````javascript
let points = [40, 100, 1, 5, 25, 10];
let sum = points.reduce(function(total, currentValue){
	return total + currentValue;
}, 0);
````



### 6. Set 객체

> 배열처럼 데이터 타입에 상관없이 값 추가 가능. 중복값 허용x. 유일한 값을 보장. 

#### Set 생성자

```javascript
let mySet = new Set();
```

#### add()

```javascript
//배열에서의 push
let mySet = new Set();
mySet.add(2);
mySet.add(3);
mySet.add(2); // 중복값 허용x
mySet.size; // 2
```

#### has()

```javascript
// Set에 특정 데이터가 저장되어있는지 확인하는 용도
mySet.has(1); // false
```

#### delete()

```javascript
// 저장되어있는 특정 데이터를 삭제하기 위해 사용
mySet.delete(2);
```

#### clear()

```javascript
// 저장되어있는 모든 데이터를 한번에 삭제하는 용도
mySet.clear()
```

#### forEach()

```javascript
// Set에 저장되는 모든 데이터를 읽을 때 사용하는 용도
mySet.forEach(function (item){
	console.log(item);
})
```

### 7. Map 객체

> Object 객체와 매우 유사. 키(Key)와 값(Value)을 매핑시켜 값을 저장. 저장된 순서대로 각 요소에 접근 가능 Object와 달리, 키로 문자열 이외 어떤 타입이든 사용 가능.  Object는 저장된 데이터를 for-in 반복문으로 읽었을 때, 데이터를 저장한 순서대로 읽는다고 보장할 수 없음. 하지만 Map은 데이터를 저장한 순서대로 보장함.

#### Map 생성자

```javascript
let newMap = new Map();
```

#### set()

```javascript
// Map 객체에 데이터를 저장할 때 파라미터로 키와 값을 이용
let newMap = new Map();
newMap.set("name", "홍길동");
newMap.set("email", "abc@gmail.com");
newMap.size; // 2
```

#### get()

```javascript
newMap.get("name");
```

#### has()

```javascript
newMap.has("name"); //true
```

#### delete()

```javascript
newMap.delete("name"); // 파라미터로 키 전달
```

#### clear()

```javascript
newMap.clear(); // 저장되어있는 모든 데이터가 삭제됨
```

#### forEach()

```javascript
newMap.forEach(function(item){
	conosle.log(item);
})
```

### 8. Math 객체

- round()

- ceil()

- floor()

- trunc()

- pow()

- sqrt()

- abs()

- min(), max()

  ```javascript
  Math.min(0, 150, 30, 20, -7 -200); // returns -200
  Math.max(0, 150, 30, 20, -7, -200); // returns 150
  ```



### 9. JSON 객체 

> 데이터를 저장하거나, 전송할 때 많이 사용되는 경량의 DATA교환 형식. JSON은 데이터 포맷

- JSON 데이터는 자바스크립트 JSON 객체의 parse()함수를 이용하면 자바스크립트 Object 객체로 변환해서 사용가능하다.
- 프로그래밍 언어에 상관없이 사용할 수 있는 데이터 교환형식
- 대부분의 언어에서 JSON 데이터를 처리할 수 있는 라이브러리를 제공

```javascript
// 데이터를 서버로 전송하기 위해 데이터 형태를 문자열 형태로 변환해야함. JSON.stringify는 Object데이터를 문자열로 변환해줌 C->S
JSON.stringify
// 서버로부터 응답받은 데이터는 문자열 형태. JSON.parse함수를 사용하면 자바스크립트 Object 객체로 변환해줌 S->C
JSON.parse
```

### 10. Window 객체

> 전역 객체임

- alert()

- confirm() : 진행할지, 종료할지에 대한 진행여부 확인

- prompt() : 문자열을 입력받을 수 있는 함수

  ```javascript
  let txt = prompt("비밀번호를 입력하세요");
  if(txt == null){
  	// 취소버튼을 클릭했을 때
  } else if(txt == ""){
  	// 어떤 값도 입력하지 않고 확인버튼을 클릭했을 때
  } else if( txt != ""){
  	// 값을 입력하고, 확인버튼을 클릭했을 때
  }
  ```

- open() : 윈도우 새창/새탭 으로 지정한 url을 오픈하는 함수

  ```javascript
  window.open("https://naver.com")
  ```

- setTimeout(): 두번째 파라미터로 지정한 시간 간격 이후에 첫 번째 파라미터에 정의한 함수를 실행하는 함수. 아직 setTimeout() 함수에서 정의한 함수가 실행되지 않았다면, clearTimeout() 함수를 사용해서 함수가 실행되는 것을 중지할 수 있음

  ```javascript
  let myExec;
  
  function myFunction() {
  	myExec = setTimeout(function(){console.log("5초 후 프로그램 실행"); }, 5000);
  }
  
  function myStopFunction() {
      clearTimeout(myExec);
  }
  ```

- setInterval(): 두번째 파라미터로 지정한 시간 간격마다 첫번째 파라미터에 정의한 함수를 반복적으로 실행하는 함수. 반복적으로 실행되기 때문에 이를 종료하기 위해 clearInterval()함수를 사용해야함

  ```javascript
  let i = 0;
  let fnc = setInterval(function() {
  	console.log("3초마다 프로그램 실행 - " + i);
  	if(i == 3){
  		clearInterval(fnc);
  	}
  	i++;
  }, 3000);
  ```

  



# XMLHttpRequest

# Fetch API

# Promise & Async/Await

> ❔Promise
>
> ❕ 자바스크립트에서 비동기 처리에 사용되는 객체. 비동기 처리란, **특정 코드의 실행이 완료될 때까지 기다리지 않고**, 다음 코드를 실행할 수 있게 해주는 방식을 의미

# Modules

# Class

> 객체를 생성하기 위한 템플릿. 데이터와 이를 조작하는 코드를 하나로 추상화.

# Error 처리

# Strict Mode

# 정규표현식

> 문자열에 포함된 특정 문자 조합을 찾기 위해 사용되는 패턴
>
> [손에 잡히는 정규 표현식 | 벤 포터 - 교보문고 (kyobobook.co.kr)](https://product.kyobobook.co.kr/detail/S000001469840)
