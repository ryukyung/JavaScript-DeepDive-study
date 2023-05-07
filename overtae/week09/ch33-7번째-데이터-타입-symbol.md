---
week: 9주차
---

# 모던 자바스크립트 Deep Dive CH33. 7번째 데이터 타입 Symbol

## 목차

  - [심벌](#심벌)
  - [심벌 값의 생성](#심벌-값의-생성)
    - [Symbol 함수](#symbol-함수)
  - [심벌과 상수](#심벌과-상수)
  - [심벌과 프로퍼티 키](#심벌과-프로퍼티-키)
    - [프로퍼티 은닉](#프로퍼티-은닉)
  - [심벌과 표준 빌트인 객체 확장](#심벌과-표준-빌트인-객체-확장)
  - [Well-known Symbol](#well-known-symbol)

## 심벌

심벌은 ES6에서 도입된 7번째 데이터 타입으로 원시 타입의 값이다. 심벌 값은 다른 값과 중복되지 않는 값이기 때문에 주로 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용한다.

## 심벌 값의 생성

### Symbol 함수

심벌 값은 Symbol 함수를 호출하여 생성해야 한다. (new 연산자와 함께 호출하지 않는다.) 이때 생성된 값은 외부로 노출되지 않아 확인할 수 없고, 다른 값과 절대 중복되지 않는 값이다.

```js
const testSymbol = Symbol();

// 확인할 수 없다.
console.log(testSymbol); // Symbol()
```

Symbol 함수에 선택적으로 문자열을 인수로 전달할 수 있다. 이 문자열은 생성된 심벌 값에 대한 설명으로 디버깅 용도로만 사용되고, 심벌 값 생성에 어떠한 영향도 주지 않는다. 심벌 값에 대한 설명이 같아도 생성된 심벌 값은 유일무이한 값이다.

심벌 값을 객체처럼 접근하면 암묵적으로 래퍼 객체를 생성한다.

```js
const testSymbol = Symbol('testSymbol');

// description과 toString 메서드는 Symbol.prototype의 프로퍼티다.
console.log(testSymbol.description); // testSymbol
console.log(testSymbol.toString()); // Symbol(testSymbol)
```

심벌 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않지만, 불리언 타입으로는 변환된다. 

```js
const testSymbol = Symbol();

// 암묵적 타입 변환 X
console.log(testSymbol + ''); // TypeError
console.log(+testSymbol); // TypeError

// 불리언 값으로는 가능
console.log(!!testSymbol) // true
```

> ⭐ **`Symbol.for`/`Symbol.keyFor` 메서드**
>
> * `Symbol.for` 메서드
>   - 인수로 전달받은 문자열을 키로 사용하여 키와 심벌 값의 쌍들이 저장되어 있는 전역 심벌 레지스트리에서 해당 키와 일치하는 심벌 값을 검색
>   - 이 메서드를 통해 애플리케이션 전역에서 중복되지 않는 심벌 값을 단 하나만 생성하여 전역 심벌 레지스트리를 통해 공유 가능
>   - 검색 성공 시: 새로운 심벌 값 생성 X, 검색된 심벌 값 반환
>   - 검색 실패 시: 새로운 심벌 값 생성 후 `Symbol.for` 메서드의 인수로 전달된 키로 전역 심벌 레지스트리에 저장, 생성된 심벌 값 반환
> * `Symbol.keyFor` 메서드
>   - 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출

## 심벌과 상수

값에는 특정한 의미가 없고, 상수 이름 자체에 의미가 있는 경우 중복이나 변경될 가능성이 있는 무의미한 상수 대신 중복될 가능성이 없는 유일무이한 심벌 값을 사용할 수 있다.

```js
const Direction = {
  UP: Symbol('up'),
  DOWN: Symbol('down'),
};
```

> ⭐ **enum, 열거형**
>
> **명명된 숫자 상수**<sup>named numeric constant</sup>의 집합으로 자바스크립트는 지원하지 않는다. (타입스크립트에서는 지원한다.)
> 
> 자바스크립트에서 enum을 흉내 내어 사용하려면 객체 변경 방지를 위한 `Object.freeze` 메서드와 심벌 값을 사용한다.
> ```js
> const Direction = Object.freeze({
>   UP: Symbol('up'),  
>   DOWN: Symbol('down'),  
> });
> ```

## 심벌과 프로퍼티 키

심벌 값은 유일무이한 값이므로 심벌 값으로 프로퍼티 키를 만들면 다른 프로퍼티 키와 절대 충돌하지 않는다. 심벌 값으로 프로퍼티 키를 사용하고 싶은 경우 프로퍼티 키로 사용할 심벌 값에 대괄호를 사용해야 한다. 접근할 때도 대괄호를 사용해야 한다.

```js
const fruit = {
  [Symbol.for('banana')]: 'banana'
};

fruit[Symbol.for('banana')]; // banana
```

### 프로퍼티 은닉

심벌 값을 프로퍼티 키로 사용하여 생성한 프로퍼티는 `for...in` 문이나 `Object.keys`, `Object.getOwnPropertyNames` 메서드로 찾을 수 없다. 이와 같이 심벌 값을 사용해 프로퍼티를 생성하는 경우 외부에 노출할 필요가 없는 프로퍼티를 은닉할 수 있다.

그러나 ES6에서 도입된 `Object.getOwnPropertySymbols` 메서드를 사용하면 심벌 값을 프로퍼티 키로 사용하여 생성한 프로퍼티를 찾을 수 있기 때문에 완전히 숨길 수 있는 것은 아니다.

```js
const fruit = {
  [Symbol.for('banana')]: 'banana'
};

console.log(Object.getOwnPropertySymbols(fruit)); // [Symbol.for(banana)]

const symbolKey = Object.getOwnPropertySymbols(fruit)[0];
console.log(fruit[symbolKey]); // banana
```

## 심벌과 표준 빌트인 객체 확장

일반적으로 표준 빌트인 객체에 사용자 정의 메서드를 직접 추가하여 확장하는 것은 권장하지 않는다. 개발자가 직접 추가한 메서드와 미래에 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문이다.

> 💭 왜?
>
> — *개발자가 직접 추가한 메서드와 미래에 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문*

하지만 중복될 가능성이 없는 심벌 값으로 프로퍼티 키를 생성하여 표준 빌트인 객체를 확장하면 어떠한 프로퍼티 키와도 충돌할 위험이 없어 안전하게 표준 빌트인 객체를 확장할 수 있다.

```js
Array.prototype[Symbol.for('sum')] = function () {
  return this.reduce((acc, cur) => acc + cur, 0)
};

[1, 2][Symbol.for('sum')](); // 3
```

## Well-known Symbol

자바스크립트가 기본으로 제공하는 빌트인 심벌 값을 ECMAScript 사양에서는 **Well-known Symbol**이라 한다. 이 값은 Symbol 함수의 프로퍼티에 할당되어 있으며, 자바스크립트 엔진의 내부 알고리즘에 사용된다.

예를 들어, `for...of` 문으로 순회 가능한 빌트인 이터러블은 Well-known Symbol인 `Symbol.iterator`를 키로 갖는 메서드를 가지며, `Symbol.iterator` 메서드를 호출하면 이터레이터를 반환하도록 ECMAScript 사양에 규정(이터레이션 프로토콜)되어 있다. 

일반 객체를 이터러블처럼 동작하도록 구현하고 싶은 경우 이터레이션 프로토콜을 따라 `Symbol.iterator`를 키로 갖는 메서드를 객체에 추가하고 이터레이터를 반환하도록 구현하면 된다.

> 💭 빌트인 이터러블?
>
> — *Array, String, Map, Set, TypedArray, arguments, NodeList, HTMLCollection, ...*

이와 같이 심벌은 중복되지 않는 상수 값을 생성하는 것은 물론 기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가하기 위해, 즉 하위 호환성을 보장하기 위해 도입되었다.