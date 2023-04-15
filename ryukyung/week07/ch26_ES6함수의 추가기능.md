### ✏️ 함수의 구분

```jsx
var foo = function () {};

// ES6 이전의 모든 함수는 callable이면서 constructor다.
foo(); // -> undefined
new foo(); // -> foo {}
```

ES6 이전까지의 자바스크립트의 함수는 별다른 구분 없이 다양한 목적으로 사용되었는데 일반적인 함수로, 생성자 함수로, 메서드로 호출할 수 있는데 쉽게 말하자면 ES6 이전의 모든 함수는 callable이면서 constructor이라는 말이다.

ES6 이전의 모든 함수는 사용 목적에 따라 명확한 구분이 없어 호출 방식에 특별한 제약이 없고 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성한다. 이런 문제를 해결하기 위해 ES6에서는 함수를 사용 목적에 따라 3가지 종류로 명확히 구분했다.

| ES6 함수의 구분    | constructor | prototype | super | arguments |
| ------------------ | ----------- | --------- | ----- | --------- |
| 일반 함수(Normal)  | O           | O         | X     | O         |
| 메서드(Method)     | X           | X         | O     | O         |
| 화살표 함수(Arrow) | X           | X         | X     | X         |

### ✏️ 메서드

ES6 이전 사양에서는 명확한 정의가 없었지만 ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.

- 인스턴스를 생성할 수 없는 `non-constructor`다.(생성자 함수를 호출할 수 없다.)
  (표준 빌트인 객체가 제공하는 프로토타입 메서드와 정적 메서드는 모두 `non-constructor`)
  → `prototype` 프로퍼티가 없고 프로토타입도 생성하지 않는다.
- 자신을 바인딩한 객체를 가리키는 내부 슬롯 `[[HomeObject]]`를 갖는다.
  → 메서드가 아닌 함수는 내부 슬롯 `[[HomeObject]]`를 갖지 않기 때문에 `super` 키워드를 사용할 수 없다.

### ✏️ 화살표 함수: Arrow function

화살표 함수는 function 키워드 대신 화살표를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의하고 내부 동작도 기존의 함수보다 간략하다.

1. **화살표 함수 정의**

   - 함수 정의: 함수 선언문으로 정의할 수 없고 함수 표현식으로 정의해야 하며 호출 방식은 기존 함수와 같다.
     ```jsx
     const multiply = (x, y) => x * y;
     multiply(2, 3); // -> 6
     ```
   - 매개변수 선언: 매개변수가 1개 이상이거나 없다면 소괄호를 사용하고 1개일 경우 소괄호를 생략할 수 있다.
     ```jsx
     const arrow0 = () => { ... };
     const arrow1 = x => { ... };
     const arrow2 = (x, y) => { ... };
     ```
   - 함수 몸체 정의

     - 하나의 표현식인 문으로 구성된다면 중괄호를 생략할 수 있고 표현식이 아닌 문이라면 생략할 수 없다.

       ```jsx
       // 표현식인 문
       const power = x => x ** 2;
       power(2); // -> 4

       // 위 표현은 다음과 동일하다.
       // block body
       const power = x => { return x ** 2; };

       // 표현식이 아닌 문
       const arrow = () => const x = 1; // SyntaxError: Unexpected token 'const'

       // 위 표현은 다음과 같이 해석된다.
       const arrow = () => { return const x = 1; };
       ```

     - 객체 리터럴을 반환하는 경우 객체 리터럴을 소괄호로 감싸 주어야 한다.

       ```jsx
       const create = (id, content) => ({ id, content });
       create(1, 'JavaScript'); // -> {id: 1, content: "JavaScript"}

       // 위 표현은 다음과 동일하다.
       const create = (id, content) => {
         return { id, content };
       };

       // { id, content }를 함수 몸체 내의 쉼표 연산자문으로 해석한다.
       const create = (id, content) => {
         id, content;
       };
       create(1, 'JavaScript'); // -> undefined
       ```

2. **화살표 함수와 일반 함수의 차이**

   - 화살표 함수는 인스턴스를 생성할 수 없는 `non-constructor`다.
     → 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.
   - 중복된 매개변수 이름을 선언할 수 없다.
     → 일반 함수는 중복된 매개변수 이름을 선언해도 에러가 발생하지 않지만 화살표 함수는 아니다.

     ```jsx
     function normal(a, a) {
       return a + a;
     }
     console.log(normal(1, 2)); // 4

     const arrow = (a, a) => a + a;
     // SyntaxError: Duplicate parameter name not allowed in this context
     ```

   - 화살표 함수는 함수 자체의 `this`, `arguments`, `super`, `new.target` 바인딩을 갖지 않는다.
     → 그렇기 때문에 `this`, `arguments`, `super`, `new.target`을 참조하면 스코프 체인을 통해 상위 스코프의 `this`, `arguments`, `super`, `new.target`을 참조한다.

3. **this**

   화살표 함수의 `this`는 일반 함수의 `this`와 다르게 동작한다. 콜백 함수 내부의 `this`가 외부 함수 `this`와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것이다.

4. **super**

   화살표 함수는 함수 자체의 `super` 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 `super`를 참조하면 this와 마찬가지로 상위 스코프의 `super`를 참조한다.

5. **arguments**

   화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않는다. 따라서 화살표 함수 내부에서 arguments를 참조하면 this와 마찬가지로 상위 스코프의 arguments를 참조한다. 화살표 함수로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용해야 한다.

### ✏️ Rest 파라미터

1. **기본 문법**

   Rest 파라미터는 매개변수 이름 앞에 3개의 점 …을 붙여서 정의한 매개변수를 의미한다. Rest 파라미터는 함수에 전달된 인수들의 목록을 **배열로** 전달받는다.

   일반 매개변수와 Rest 매개변수는 함께 사용할 수 있고 순차적으로 할당된다. 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 Rest에 할당되기 때문에 Rest 파라미터는 반드시 마지막 파라미터이어야 한다.

   Rest 파라미터는 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 `length` 프로퍼티에 영향을 주지 않는다.

   ```jsx
   function foo(param, ...rest) {
     console.log(param); // 1
     console.log(rest); // [ 2, 3, 4, 5 ]
   }

   foo(1, 2, 3, 4, 5);

   function bar(param1, param2, ...rest) {
     console.log(param1); // 1
     console.log(param2); // 2
     console.log(rest); // [ 3, 4, 5 ]
   }

   bar(1, 2, 3, 4, 5);
   ```

2. **Rest 파라미터와 arguments 객체**

   ES6에서는 rest 파라미터를 사용하여 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있다. 이르 ㄹ통해 유사 배열 객체인 arguments 객체를 배열로 변환하는 번거로움을 피할 수 있다.

   함수와 ES6 메서드는 Rest 파라미터와 arguments 객체를 모두 사용할 수 있지만 화살표 함수는 함수 자체의 arguments 객체를 갖지 않기 때문에 화살표 함수로 가변 인자 함수를 구현할 때는 반드시 Rest 파라미터를 사용해야 한다.

### ✏️ 매개변수 기본값

ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다. 매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우와 `undefined`를 전달한 경우에만 유효하다. 매개변수 기본값은 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 `length` 프로퍼티와 `arguments` 객체에 아무런 영향을 주지 않는다.
