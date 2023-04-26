---
week: 7주차
---

# 모던 자바스크립트 Deep Dive CH26. ES6 함수의 추가 기능

## 목차

- [함수의 구분](#함수의-구분)
  - [1. ES6 이전](#1-es6-이전)
  - [2. ES6](#2-es6)
- [메서드](#메서드)
- [화살표 함수](#화살표-함수)
  - [1. 정의 문법](#1-정의-문법)
  - [2. 화살표 함수 vs. 일반 함수](#2-화살표-함수-vs-일반-함수)
  - [3. this](#3-this)
    - [주의](#주의)
  - [4. super, arguments](#4-super-arguments)
- [Rest 파라미터](#rest-파라미터)
  - [1. 기본 문법](#1-기본-문법)
  - [2. arguments 객체](#2-arguments-객체)
- [매개변수 기본값](#매개변수-기본값)

## 함수의 구분

### 1. ES6 이전

모든 함수는 사용 목적에 따라 명확히 구분되지 않아, 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다. (`callable`이면서 `constructor`)

> ⭐ **`callable`, `constructor`/`non-constructor`?**
> 
> * `callable`: 호출할 수 있는 함수 객체
> * `constructor`: 인스턴스를 생성할 수 있는 함수 객체
> * `non-constructor`: 인스턴스를 생성할 수 없는 함수 객체

`constructor`라는 것은 함수가 prototype 프로퍼티를 가지며, 프로토타입 객체도 생성한다는 것을 의미한다. 따라서 객체에 바인딩된 함수(메서드)나 콜백 함수도 `constructor`이기 때문에 불필요한 프로토타입 객체를 생성한다. 

### 2. ES6

* 사용 목적에 따른 명확한 구분 X
* 호출 방식에 특별한 제약 X
* 생성자 함수로 호출되지 않아도 프로토타입 객체 생성

위와 같은 문제를 해결하기 위해 ES6에서는 함수를 사용 목적에 따라 세 가지 종류로 명확히 구분했다.

| ES6 함수의 구분    | constructor | prototype | super | arguments |
| :----------------- | :---------: | :-------: | :---: | :-------: |
| 일반 함수(Normal)  |     Ｏ      |    Ｏ     |  Ｘ   |    Ｏ     |
| 메서드(Method)     |     Ｘ      |    Ｘ     |  Ｏ   |    Ｏ     |
| 화살표 함수(Arrow) |     Ｘ      |    Ｘ     |  Ｘ   |    Ｘ     |

## 메서드

ES6 이전에는 메서드에 대한 명확한 정의가 없었고, 일반적으로 객체에 바인딩된 함수를 일컫는 의미로 사용되었다. ES6 사양에서는 **메서드 축약 표현으로 정의된 함수만을 의미**한다. ES6 메서드는 본연의 기능(super)을 추가하고 의미적으로 맞지 않는 기능(constructor)은 제거했다.

ES6 메서드는 인스턴스를 생성할 수 없는 non-constructor기 때문에 생성자 함수로서 호출할 수 없다. 따라서 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.

```js
const man = {
  name: '홍길동',
  
  // 메서드
  getName() { return this.name; },

  // 일반 함수 (프로퍼티 값으로 익명 함수 표현식 할당)
  normalGetName: function() { return this.name; }
};

// ES6 메서드
// non-constructor라 생성자 함수로서 호출 불가능
new man.getName(); // TypeError
// prototype 프로퍼티 X
man.getName.hasOwnProperty('prototype'); // false

// 일반 함수
// constructor라 생성자 함수로서 호출 가능
new man.normalGetName(); // normalGetName {}
// prototype 프로퍼티 O
man.normalGetName.hasOwnProperty('prototype'); // true
```

> 💭 참고
> 
> — *표준 빌트인 객체가 제공하는 프로토타입 메서드와 정적 메서드는 모두 non-constructor다.*

ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 `[[HomeObject]]`를 갖는다. 내부 슬롯 `[[HomeObject]]`를 갖는 ES6 메서드는 수퍼클래스의 메서드를 참조하는 super 키워드를 사용할 수 있다.

```js
const base = {
  name: '홍길동',
  introduce() {
    return `제 이름은 ${this.name}입니다.`;
  }
};

const derived = {
  __proto__: base,
  // ES6 메서드는 [[HomeObject]]를 갖는다.
  // introduce의 [[HomeObject]]는 derived.prototype을 가리키고,
  // super는 introduce의 [[HomeObject]]의 프로토타입(base.prototype)을 가리킨다.
  introduce() {
    return `${super.introduce()} 잘부탁드립니다.`;
  }

  // ES6 메서드가 아니면 [[HomeObject]]를 갖지 않는다.
  // 따라서 super 키워드를 사용할 수 없다.
  introduce: function() {
    return `${super.introduce()} 잘부탁드립니다.`;
  }
}
```

## 화살표 함수

**화살표 함수**<sup>arrow function</sup>는 function 키워드 대신 화살표(`=>`, fat arrow)를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있다. 또한 내부 동작도 기존의 함수보다 간략하다. 화살표 함수는 콜백 함수 내부에서 this가 전역 객체를 가리키는 문제를 해결할 때 유용하다.

### 1. 정의 문법

* **함수 정의**
  - 함수 선언문으로 정의 불가, 함수 표현식으로 정의해야 함
    ```js
    const add = (x, y) => x + y;
    ```
  - 호출 방식은 기존과 동일
    ```js
    add(1, 2); // 3
    ```
* **매개변수 선언**
  ```js
  // 매개변수가 여러 개인 경우: 소괄호 안에 선언
  const foo = (x, y) => { ... };
  // 매개변수가 한 개인 경우: 소괄호 생략 가능
  const foo = x => { ... };
  // 매개변수가 없는 경우: 소괄호 생략 불가
  const foo = () => { ... };
  ```
* **함수 몸체 정의**
  - 하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 생략 가능
    > *함수 몸체 내부의 문이 값으로 평가될 수 있는 표현식인 문이라면 암묵적으로 반환*
    ```js
    const increase = x => ++x;
    // 함수 몸체 내부의 문이 표현식이 아닌 문이라면 중괄호 생략 불가
    const test = () => { const x = 1; };
    // 객체 리터럴을 반환하는 경우 객체 리터럴을 소괄호로 감싸주어야 함
    const createObject = (name, age) => ({ name, age });
    ```
  - 여러 개의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 생략 불가
    > *반환값이 있는 경우 명시적으로 반환해야 함*
  - 즉시 실행 함수로 사용 가능
    ```js
    const man = (name => ({
      introduce() { return `제 이름은 ${name}입니다.`; }
    }))('홍길동');

    console.log(man.introduce()); // 제 이름은 홍길동입니다.
    ```
  - 일급 객체이므로 고차 함수(`Array.prototype.map`, `Array.prototype.filter`, `Array.prototype.reduce` 등)에 인수로 전달 가능
    ```js
    [1, 2, 3].map(x => x * x); // [1, 4, 9]
    ```

### 2. 화살표 함수 vs. 일반 함수

1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다.
2. 화살표 함수는 중복된 매개변수 이름을 선언할 수 없다.
   > *단, 일반 함수도 strict mode에서 중복된 매개변수 이름을 선언하면 에러가 발생한다.*
3. 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.

### 3. this

화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 this를 참조할 경우 상위 스코프의 this를 그대로 참조한다. (lexical this)

> ⭐ **lexical this**
>
> 렉시컬 스코프와 같이 화살표 함수의 this가 함수가 정의된 위치에 의해 결정된다는 것을 의미

이는 콜백 함수 내부의 this가 외부 함수의 this와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것이다.

#### 주의

화살표 함수로 메서드를 정의하는 경우 상위 스코프의 this를 가리키기 때문에 몇가지 주의해야 할 점이 있다.

1. 메서드를 정의할 때는 ES6 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋다.
2. 프로퍼티를 동적 추가할 때는 일반 함수를 할당한다.
    ```js
    function Man(name) {
      this.name = name;
    }

    Man.prototype.introduce = function () { console.log(`제 이름은 ${this.name}입니다.`); };
    ```
3. ES6 메서드를 동적 추가하고 싶다면 객체 리터럴을 바인딩하고 프로토타입의 constructor 프로퍼티와 생성자 함수 간의 연결을 재설정한다.
    ```js
    function Man(name) {
      this.name = name;
    }

    Man.prototype = {
      // constructor 프로퍼티와 생성자 함수 간의 연결을 재설정
      constructor: Man,
      introduce() { console.log(`제 이름은 ${this.name}입니다.`); }
    };
    ```

### 4. super, arguments

화살표 함수는 함수 자체의 super, arguments 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 super나 arguments를 참조할 경우 this와 마찬가지로 상위 스코프의 super나 arguments를 참조한다.

## Rest 파라미터

Rest 파라미터는 매개변수 이름 앞에 세개의 점(`...`)을 붙여서 정의한 매개변수를 의미하며 함수에 전달된 인수들의 목록을 배열로 전달받는다.

```js
function myNumbers(...num) {
  console.log(num);
}

myNumbers(2, 4, 6, 8); // [ 2, 4, 6, 8 ]
```

### 1. 기본 문법

* 일반 매개변수와 Rest 파라미터는 함께 사용할 수 있다.
  - 함수에 전달된 인수들은 매개변수와 Rest 파라미터에 순차적으로 할당된다.
  - Rest 파라미터는 반드시 마지막 파라미터여야 한다.
    > Rest 파라미터는 먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당되기 때문이다.
* Rest 파라미터는 단 하나만 선언할 수 있다.
* Rest 파라미터는 함수 객체의 length 프로퍼티에 영향을 주지 않는다.

> ⭐ **length 프로퍼티**
>
> 함수 정의 시 선언한 매개변수 개수를 나타낸다.

### 2. arguments 객체

함수와 ES6 메서드는 Rest 파라미터와 arguments 객체를 모두 사용할 수 있다. 하지만 화살표 함수는 함수 자체의 arguments 객체를 갖지 않으므로 가변 인자 함수 구현시 반드시 Rest 파라미터를 사용해야 한다.

rest 파라미터를 사용하면 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있어 arguments 객체를 배열로 변환하지 않아도 된다.

> ⭐ **arguments 객체**
>
> arguments 객체는 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체로, 함수 내부에서 지역 변수처럼 사용할 수 있다. 객체가 아닌 유사 배열 객체이므로 배열 메서드를 사용할 때 arguments 객체를 배열로 변환해야 하는 번거로움이 있다. 

## 매개변수 기본값

함수를 호출할 때 매개변수의 개수만큼 인수를 전달하지 않은 경우, 인수가 전달되지 않은 매개변수의 값은 undefined다. 이는 의도치 않은 결과가 나올 수 있기 때문에 매개변수에 기본값을 할당할 필요가 있다.

```js
function add(x, y) {
  // 매개변수에 인수가 전달되었는지 확인 후 기본값 할당
  x = x || 0;
  y = y || 0;

  return x + y;
}
```

ES6에서 도입된 매개변수 기본값을 사용하면 인수 체크 및 초기화를 간소화할 수 있다. 매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효하다.

```js
function add(x = 0, y = 0) {
  return x + y;
}
```

Rest 파라미터에는 기본값을 지정할 수 없고, 매개변수 기본값은 함수 객체의 length 프로퍼티와 arguments 객체에 아무런 영향을 주지 않는다.