# 언제까지 넘겨 짚을래 ch9. 타입변화와 단축평가

### ✏️ 타입 변환이란?

개발자가 의도적으로 값의 타입을 변환시키는 것을 **명시적 타입 변환**, **타입 캐스팅**이라고 하고 의도와 상관없이 자바스크립트 엔진에 의해 변환되는 것을 **암묵적 타입 변환**, **타입 강제 변환**이라고 한다.

원시 값은 변경 불가능한 값이기 때문에 타입 변환을 통해 원시 값을 직접 변경하는 것이 아닌 다른 타입의 새로운 원시 값을 생성하는 것이다.

### ✏️ 암묵적 타입 변환: implicit coercion

문자열, 숫자, 불리언과 같은 원시 탕비 중 하나로 타입을 자동 변환한다.

- 문자열 타입으로 변환
  `+` 연산자는 피연산자 중 하나 이상이 문자열이면 문자열 연결 연산자로 동작한다.
  Number | Boolean | null | undefined | Symbol | Object + `‘’` = String
- 숫자 타입으로 변환
  산술 연산자의 모든 피연산자는 코드 문맥상 모두 숫자 타입이여야 한다. 그렇기 때문에 피연산자가 숫자 타입이 아니라면 숫자 타입으로 암묵적 타입 변환한다. 피연산자를 숫자 타입으로 변환할 수 없는 경우 평가 결과는 NaN이 된다.
  ```jsx
  true +; // 1
  false +; // 0
  null +; // 0
  undefined +;// NaN
  {} +; // NaN
  [] +; // 0
  [10, 20, 30] +;// NaN
  function () {}; // NaN
  ```
- 불리언 타입으로 변환
  자바스크립트 엔진은 불리언 타입이 아닌 값을 Truthy, Falsy값으로 구분하는데 Truthy값은 ture로, Falsy 값은 false로 암묵적 타입 변환된다.
  false로 평가되는 Falsy 값에는 false, undefined, null, 0, -0, NaN, ‘’ 등이 있다.

### ✏️ 명시적 타입 변환: explicit coercion

표준 빌트인 생성자 함수(String, Number, Boolean)를 new 연산자 없이 호출하는 방법, 빌트인 메서드를 사용하는 방법, 암묵적 타입 변환을 이용한다.

🪝 **표준 빌트인 생성자 함수**: 객체를 생성하기 위한 함수, new 연산자 사용

🪝 **표준 빌트인 메서드**: 자바스크립트에서 기본 제공하는 빌트인 객체의 메서드

- 문자열 타입으로 변환
  - String 생성자 함수를 new 연산자 없이 호출
    ```jsx
    String(1); // "1"
    String(Infinity); // "Infinity"
    String(true); // "true"
    ```
  - Object.prototype.toString 메서드 사용
    ```jsx
    (1).toString(); // "1"
    Infinity.toString(); // "Infinity"
    true.toString(); // "true
    ```
  - 문자열 연결 연산자 이용
    ```jsx
    1 + ''; // "1"
    Infinity + ''; // "Infinity"
    true + ''; // "true"
    ```
- 숫자 타입으로 변환

  - Number 생성자 함수를 new 연산자 없이 호출
    ```jsx
    Number(1); // 1
    Number(0.8); // 0.8
    Number(true); // 1
    ```
  - parseInt, parseFloat 함수를 사용
    ```jsx
    parseInt('1'); // 1
    parseFloat('0.8'); // 0.8
    parseInt(true); // NaN
    ```
  - 단항 산술 연산자를 이용

    ```jsx
    +'1' + // 1
      '0.8' + // 0.8
      true; // true
    ```

  - 산술 연산자를 이용

    ```jsx
    '1' * 1; // 1
    '0.8' * 1; // 0.8
    true * 1; // 1
    ```

- 불리언 타입으로 변환
  - Boolean 생성자 함수를 new 연산자 없이 호출
    ```jsx
    Boolean('a'); // true
    Boolean(''); // false
    Boolean(0); // false
    Boolean(1); // true
    Booelan(null); // false
    Boolean(undefined); // false
    Boolean({}); // true
    Boolean([]); // true
    ```
  - ! 부정 논리 연산자를 두 번 사용하는 방법
    ```jsx
    !!'a'; // true
    !!''; // false
    !!0; // false
    !!1; // true
    !!null; // false
    !!undefined; // false
    !!{}; // true
    !![]; // true
    ```

### ✏️ 단축 평가

- 논리 연산자를 사용한 단축평가
  논리합, 논리곱 연산자 표현식은 2개의 피연산자 중 어느 한쪽으로 평가된다.
  - 논리곱: &&
    ```jsx
    'coco' && 'gaori'; // "gaori"
    false && 'gaori'; // false
    'gaori' && false; // false
    ```
    2개의 피연산자가 모두 true로 평가될 때 true를 반환하는데 ‘coco’는 Truthy 값이고, ‘gaori’ 또한 Truthy 값이기 때문에 모두 true를 반환한다. 논리곱 연산자는 왼쪽에서 오른쪽으로 평가가 진행되기 때문에 두 번째 피연산자인 ‘gaori’를 반환한다.
  - 논리합: ||
    ```jsx
    'coco' || 'gaori'; // "coco"
    false || 'gaori'; // "gaori"
    'gaori' || fasle; // "gaori"
    ```
    논리합은 논리곱과 비슷하지만 논리합은 첫 번째 피연산자를 반환하기 때문에 ‘coco’를 반환한다.

🪝 **옵셔널 체이닝 연산자(?.): Optional chaining:** 좌항의 피연산자가 false로 평가되는 Falsy값이라도 null, undefined가 아니라면 우항의 프로퍼티 참조를 이어간다. (null, undefined인 경우 undefined를 반환)

🪝 **null 병합 연산자(??): nullish coalescing:** 좌항의 피연산자가 null, undefined인 경우 우항의 피연산자를 반환하고 그렇지 않으면 좌항의 피연산자를 반환한다.
