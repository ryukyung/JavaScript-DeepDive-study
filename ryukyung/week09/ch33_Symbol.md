### ✏️ 심벌이란?

ES6에서 도입된 7번째 데이터 타입으로, 변경 불가능한 원시 타입의 값이다. 심벌 값은 다른 값과 중복되지 않는 유일무이한 값이며 이름의 충돌 위험이 없는 유일한 프로퍼티 키를 만들기 위해 사용한다.

### ✏️ 심벌 값의 생성

1. **Symbol 함수**

   ```jsx
   const mySymbol = Symbol();
   console.log(typeof mySymbol); // symbol
   console.log(mySymbol); // Symbol()
   ```

   심벌 값은 Symbol 함수를 호출하여 생성한다. 이때 생성된 심벌 값은 외부로 노출되지 않아 확인할 수 없다.

   `Symbol()`에는 선택적으로 문자열을 인수로 전달할 수 있는데 이는 생성된 심벌 값에 대한 설명으로 디버깅 용도로만 사용될 뿐 심벌 값 생성에 어떠한 영향도 주지 않는다.

   심벌 값은 객체처럼 접근하면 암묵적으로 래퍼 객체를 생성한다.

   ```jsx
   const mySymbol = Symbol('mySymbol');

   // 심벌도 레퍼 객체를 생성한다
   console.log(mySymbol.description); // mySymbol
   console.log(mySymbol.toString()); // Symbol(mySymbol)
   ```

   심벌 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않지만 불리언 타입으로는암묵적으로 변환한다.

   ```jsx
   const mySymbol = Symbol();

   // TypeError: 심벌 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않는다.
   console.log(mySymbol + '');
   console.log(+mySymbol);

   // 불리언 타입으로는 암묵적으로 타입 변환된다.
   console.log(!!mySymbol); // true

   // if 문 등에서 존재 확인을 위해 사용할 수 있다.
   if (mySymbol) console.log('mySymbol is not empty.');
   // mySymbol is not empty.
   ```

2. **Symbol.for / Symbol.keyFor 메서드**

   **Symbol.for 메서드**

   인수로 전달받은 문자열을 키로 사용하여 키와 시멀 값의 쌍들이 저장되어 있는 전역 심벌 레지스트리에서 해당 키와 일치하는 심벌 값을 검색한다.

   - 검색 성공O : 새로운 심벌 값 생성X, 검색된 심벌 값 반환
   - 검색 성공X : 새로운 심벌 값 생성하여 Symbol.for 메서드의 인수로 전달된 키로 전역 심벌 레지스트리에 저장 후 생성된 심벌 값 반환

   **Symbol.keyFor 메서드**

   전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출

### ✏️ 심벌과 상수

값에는 특별한 의미가 없고 상수 이름 자체에 의미가 있는 경우 변경/중복될 가능성이 있는 무의미한 상수 대신 중복될 가능성이 없는 유일무이한 심벌 값을 사용할 수 있다.

```jsx
const Direction = {
  UP: Symbol('up'),
  DOWN: Symbol('down'),
  LEFT: Symbol('left'),
  RIGHT: Symbol('right'),
};
```

### ✏️ 심벌과 프로퍼티 키

심벌 값을 프로퍼티 키로 사용하려면 프로퍼티 키로 사용할 심벌 값에 대괄호를 사용해야 하며 프로퍼티에 접근할 때도 마찬가지로 대괄호를 사용해야 한다.

```jsx
const obj = {
  // 심벌 값으로 프로퍼티 키를 생성
  [Symbol.for('mySymbol')]: 1,
};

obj[Symbol.for('mySymbol')]; // 1
```

### ✏️ 심벌과 프로퍼티 은닉

심벌 값을 프로퍼티 키로 사용하여 생성한 프로퍼티는 `for…in`, `Object.keys`, `Object.getOwnPropertyNames` 메서드로 찾을 수 없다.
심벌 값을 프로퍼티 키로 사용하여 프로퍼티를 생성하면 외부에 노출할 필요가 없는 프로퍼티를 은닉할수 있지만 ES6에서 도입된 `Object.getOwnPropertySymbols` 메서드를 사용하면 심벌 값을 프로퍼티 키로 사용하여 생성한 프로퍼티를 찾을 수 있다.

### ✏️ 심벌과 표준 빌트인 객체 확장

일반적으로 표준 빌트인 객체에 사용자 정의 메서드를 직접 추가하여 확장하는 것은 권장하지 않는다.(개발자가 직접 추가한 메서드와 미래에 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문이다.)

중복될 가능성이 없는 심벌 값으로 프로퍼티 키를 생성하여 표준 빌트인 객체를 확장하면 기본 프로퍼티 키 및 앞으로 생길 프로퍼티 키와도 충돌할 위험이 없어 안전하게 표준 빌트인 객체를 확장할 수 있다.

### ✏️ Well-known Symbol

자바스크립트가 기본으로 제공하는 빌트인 심벌 값은 ECMAScript 사양에서는 Well-Known Symbol이라 부르고 이는 자바스크립트 엔진의 내부 알고리즘에서 사용된다.

ex) for…of문으로 순회 가능한 빌트인 이터러블은 Well-Known Symbol인 `Symbol.iterator`를 키로 갖는 메서드를 가지며 `Symbol.iterator` 메서드를 호출하면 이터레이터를 반환하도록 ECMAScript 사양에 규정(이터레이션 프로토콜)되어 있다.

일반 객체를 이터러블처럼 동작하도록 구현하려면 이터레이션 프로토콜을 따르면 된다. (`Symbol.iterator`를 키로 갖는 메서드를 객체에 추가하고 이터레이터를 반환하도록 구현한다.)

심볼은 중복되지 않는 상수 값을 생성하는 것은 물론 기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가하기 위해, 하위 호환성을 보장하기 위해 도입되었다.
