# Destructruing

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

