---
week: 6주차
---

# 모던 자바스크립트 Deep Dive CH22. this

## 목차

- [`this` 키워드](#this-키워드)
- [함수 호출 방식과 this 바인딩](#함수-호출-방식과-this-바인딩)
  - [일반 함수 호출](#일반-함수-호출)
  - [메서드 호출](#메서드-호출)
  - [생성자 함수 호출](#생성자-함수-호출)
  - [`Function.prototype.apply/call/bind` 메서드에 의한 간접 호출](#functionprototypeapplycallbind-메서드에-의한-간접-호출)

## `this` 키워드

객체는 상태를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조다. 이때, 메서드는 자신이 속한 객체의 프로퍼티를 참조하고 변경할 수 있어야 한다. 이를 위해서는 먼저 **자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.**

```js
// 객체 리터럴 방식으로 생성한 객체
const rectangle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  width: 10,
  height: 5,
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getVolume() {
    // 자신이 속한 객체 rectangle을 재귀적으로 참조
    return rectangle.width * rectangle.height;
  }
};
```

위 예제는 일반적이지 않고 바람직하지 않은 방식이다. 생성자 함수 방식으로 인스턴스를 생성하는 경우 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없기 때문이다. 따라서 자바스크립트는 **자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자**인 `this`를 제공한다.

`this`는 **자기 참조 변수**<sup>self-referencing variable</sup>로, 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다. `this`는 자바스크립트 엔진에 의해 암묵적으로 생성되고, 코드 어디서든 참조할 수 있다. 함수를 호출하게 되면, arguments 객체와 `this`가 암묵적으로 함수 내부에 전달된다. 즉, arguments 객체와 마찬가지로 `this`도 지역 변수처럼 사용할 수 있다.

> ⭐ **this 바인딩**
>
> this가 가리킬 객체를 바인딩하는 것으로, 함수 호출 방식에 의해 동적으로 결정된다. 
> > 💭 바인딩이란?
> >
> > — *식별자와 값을 연결하는 과정을 의미한다.*

## 함수 호출 방식과 this 바인딩

> ⭐ **렉시컬 스코프 vs. this 바인딩**
>
> - 렉시컬 스코프: 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정
> - this 바인딩: 함수 호출 시점에 결정

동일한 함수더라도 아래와 같이 다양한 방식으로 호출이 가능하다.

1. 일반 함수 호출
  ```js
  foo();
  ```
2. 메서드 호출
  ```js
  obj.foo();
  ```
3. 생성자 함수 호출
  ```js
  new foo();
  ```
4. `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출
  ```js
  foo.call(x);
  foo.apply(x);
  foo.bind(x)();
  ```

| 함수 호출 방식                                               | `this` 바인딩                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------------------ |
| 일반 함수 호출                                               | 전역 객체                                                                |
| 메서드 호출                                                  | 메서드를 호출한 객체                                                     |
| 생성자 함수 호출                                             | 생성자 함수가 (미래에) 생성할 인스턴스                                   |
| `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출 | `Function.prototype.apply/call/bind` 메서드에 첫 번째 인수로 전달한 객체 |

### 일반 함수 호출

일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 `this`에는 **전역 객체가 바인딩**된다. 다만 객체를 생성하지 않는 일반 함수에서 `this`는 의미가 없기 때문에 strict mode가 적용된 일반 함수의 `this`에는 `undefined`가 바인딩된다.

```js
// 전역 객체의 프로퍼티
var x = 1;

const test = {
  x: 10,
  zero() {
    // 메서드 내에서 정의한 중첩 함수
    function one() {
      console.log(this, this.x);
    }
  }

  // 일반 함수로 호출, 전역 객체 바인딩
  one(); // window, 1
}
```

메서드 내에서 정의된 중첩 함수 또는 메서드에게 전달한 콜백 함수가 일반 함수로 호출될 때 this가 전역 객체를 바인딩하는 것은 문제가 있다. 보통 중첩 함수나 콜백 함수는 외부 함수를 돕는 헬퍼 함수의 역할을 하는데, 외부 함수인 메서드와 중첩 함수 또는 콜백 함수의 this가 일치하지 않을 경우 헬퍼 함수로 동작하기 어려워지기 때문이다. 이를 일치시키기 위한 방법은 아래와 같다.

```js
var x = 1;

const obj = {
  x: 10,
  test() {
    // this 바인딩(obj)을 변수 that에 할당
    const that = this;

    // 콜백 함수 내부에서 this 대신 that을 참조
    setTimeout(function () {
      console.log(that.x); // 10
    }, 100);

    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킴
    setTimeout(() => console.log(this.x), 100); // 10
  }
};

obj.test();
```

### 메서드 호출

메서드 내부의 `this`에는 메서드를 호출한 객체(메서드 이름 앞의 마침표 연산자 앞에 기술한 객체)가 바인딩된다. 이때, *메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩*된다는 점을 주의해야 한다.

```js
const fridge = {
  fruit: 'banana',
  getFruit() {
    return this.fruit;
  }
};

// 메서드를 호출한 객체: fridge
console.log(fridge.getFruit()); // banana

const freezer = {
  fruit: 'ice banana',
};
// getFruit 메서드를 freezer 객체의 메서드로 전달
freezer.getFruit = fridge.getFruit;

// 메서드를 호출한 객체: freezer
console.log(freezer.getFruit()); // ice banana
```

### 생성자 함수 호출

생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩된다.

```js
function Rectangle(width, height) {
  this.width = width;
  this.height = height;

  this.getVolume = function () {
    return this.width * this.height;
  };
}

const one = new Rectangle(2, 5);
const two = new Rectangle(3, 9);

console.log(one.getVolume(), two.getVolume()); // 10 27
```

### `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출

`apply`, `call`, `bind` 메서드는 `Function.prototype`의 메서드로 모든 함수가 상속받아 사용할 수 있다.

`apply`와 `call` 메서드는 this로 사용할 객체와 인수 리스트를 인수로 전달받아 **함수를 호출한다.**

```js
/**
* 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다.
* @param thisArg - this로 사용할 객체
* @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
* @returns 호출된 함수의 반환값
*/
Function.prototype.apply(thisArg[, argsArray])

/**
* 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출한다.
* @param thisArg - this로 사용할 객체
* @param arg1, arg2, ... - 함수에게 전달할 인수 리스트
* @returns 호출된 함수의 반환값
*/
Function.prototype.call (thisArg[, argl[, arg2[, ...]]])
```

`apply`와 `call` 메서드는 호출할 함수에 인수를 전달하는 방식만 다를 뿐 동일하게 동작한다.

```js
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// getThisBinding 함수를 호출하면서
// 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
// apply: 호출할 함수의 인수를 배열로 묶어 전달
console.log(getThisBinding.apply(thisArg, [1, 2])); // {a: 1}
// call: 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달
console.log(getThisBinding.call(thisArg, 1, 2)); // {a: 1}
```

`bind` 메서드는 함수를 호출하지 않고 this로 사용할 객체만 전달한다. 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결할 때 유용하게 사용된다.

```js
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// this로 사용할 객체 전달, 함수 호출 X
console.log(getThisBinding.bind(thisArg)); // getThisBinding

// 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```