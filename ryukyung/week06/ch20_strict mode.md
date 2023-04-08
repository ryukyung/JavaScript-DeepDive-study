### ✏️ strict mode란?

자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

```jsx
function foo() {
  x = 10;
}
foo();
console.log(x); // 10: ReferenceError(X), 암묵적 선언(O)
```

### ✏️ strict mode의 적용

전역의 선두 또는 함수 몸체의 선두에 `‘use strict’;`를 추가하는데 코드의 선두에 위치하지 않으면 `strict mode`가 제대로 동작하지 않는다.

```jsx
// 전역의 선두에 선언
'user strict';
function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();

//  함수 몸체의 선두에 선언
function foo() {
  'user strict';
  x = 10; // ReferenceError: x is not defined
}
foo();
```

### ✏️ 전역에 strict mode를 적용하는 것은 피하자

`strict mode`는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용되는데 `strict mode`와 `non-strict mode`를 혼용해서 사용하면 오류를 발생시킬 수 있다.

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      'use strict';
      x = 1;
    </script>
  </body>
</html>
```

외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 `non-strict mode`인 경우도 있기 때문에 전역에 사용하는 것은 바람직하지 않고 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 `strict mode`를 적용하는 것이 좋다.

```jsx
(function () {
  'use strict';
})();
```

### ✏️ 함수 단위로 strict mode를 적용하는 것도 피하자

함수에 따라 `strict mode`를 적용하거나 적용하지 않거나 하는 것은 바람직하지 않고 모든 함수에 일일이 적용하는 것은 번거로운 일이다. `**strict mode`는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다.\*\*

```jsx
(function () {
  // non-strict mode
  var lеt = 10; // Error(X)

  function foo() {
    'use strict';

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
})();
```

### ✏️ strict mode가 발생시키는 에러

| 암묵적 전역                 | 선언하지 않은 변수를 잠조하면 ReferenceError가 발생                |
| --------------------------- | ------------------------------------------------------------------ |
| 변수, 함수, 매개변수의 삭제 | delete 연산자로 변수, 함수, 매개변수를 삭제하면 syntaxError가 발생 |
| 매개변수 이름의 중복        | 중복된 매개변수 이름을 사용하면 SyntaxError가 발생                 |
| with문의 사용               | with문 사용하면 SyntaxError 발생                                   |

### ✏️ strict mode 적용에 의한 변화

1. 일반 함수의 `this`

   `strict mode`에서 함수를 일반함수로 호출하면 `this`에 `undefined`가 바인딩된다. 생성자 함수가 아닌 일반 함수 내부에서는 `this`를 사용할 필요가 없기 때문이다.

2. arguments 객체

   `strict mode`에서 매개변수에 전달된 인수를 재할당하여 변경해도 `arguments` 객체에 반영되지 않는다.
