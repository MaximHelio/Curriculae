# 변수와 상수

### var 선언자

- 데이터 타입에 상관없이 변수를 저장할 때, var 선언자와 let 선언자 사용 가능
- 실무에서는 오류를 최소화하기 위해 주로 let선언자를 사용하나, 이미 개발된 많은 js프로그램이 var 선언자로 되어있기 때문에 var선언자에 대해서도 제대로 이해해야함

```javascript
var x = 5; // 변수 x에 5를 저장
var y = 6; // 변수 y에 6을 저장
var x + y = 11; // 변수 z에 변수 x와 변수 y를 더한 값을 저장

var x = 7; // 변수를 새로 선언하고 변수명 x에 7을 저장
z =  x + y; // 13 
```

- <details><summary>확인</summary>

  var 선언자를 사용하면 동일한 변수명을 사용해도 에러가 나지 않기 때문에 매번 새로운 변수명을 만들어서 사용하지 않아도 된다는 장점이 있다. 하지만 이건 단점이 되기도 해서 Typescript의 태동을 불러왔다.

  </details>

### let 선언자

### const 선언자(상수)

# 데이터 타입

### 기본자료형

### 객체

### typeof 사용하여 현재 변수의 데이터 타입 알아내기

```javascript
console.log(typeof 3.14) // number
console.log(typeof "철수") // string
console.log(typeof true) // boolean
console.log(typeof [1, 2, 3]) // object
console.log(typeof undefined) // undefined
console.log(typeof null) // object
```



# 64비트 부동소수점

# 연산자

### 할당 연산자

### 산술 연산자

### 비교 연산자

### 논리 연산자

### 조건(삼항) 연산자

# 조건문

### if문과 switch 문 차이

> if : 비교연산, 순차적

> switch: 비교연산 x, 상수값 판단



# 반복문

### for-loop

### for-in

### for-of

### while

# 함수

```
❔목적
❕코드의 재활용
  유지보수의 편의성
  코드 가독성 향상
  코드품질 향상 및 신뢰성 확보 
```

