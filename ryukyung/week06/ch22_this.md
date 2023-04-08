### ✏️ this 키워드

동작을 나타내는 메서드는 자신이 속한 객체의 상태(프로퍼티)를 참조하고 변경할 수 있어야 한다. 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 하는데 이때 this를 사용한다.

```jsx
// getDiameter 메서드 내에서 메서드 자신이 속한 객체를 가리키는 Circle을 참조하고 있다.
const circle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  radius: 5,
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getDiameter() {
    return 2 * circle.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

`getDiameter` 메서드 내에세 메서드 자신이 속한 객체를 가리키는 식별자 `circle`을 참조하고 있는데 이 참조 표현식이 평가되는 시점은 `getDiameter` 메서드가 호출되어 함수 몸체가 실행되는 시점이다.

자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지 않으며 바람직하지 않는데 생성자 함수방식으로 인스턴스를 생성하는 경우를 보자.

```jsx
function Circle(radius) {
  // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  ????.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  return 2 * ????.radius;
};

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다.
const circle = new Circle(5);
```

함수를 선언하는 시점에는 자신이 생성한 인스턴스를 가리키는 식별자를 알 수 없다. 생성자 함수로 객체를 생성하려면 생성자 함수를 먼저 정의한 후 `new` 연산자와 함께 생성자 함수를 호출해야 한다. 쉽게 말해서 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수가 존재해야 한다.

생성자 함수를 정의하는 시점에는 인스턴스를 가리키는 식별자를 알 수 없기 때문에 `this` 식별자를 사용한다. `this`는 자신이 속한 객체나 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다. `this`는 자바스크립트 엔진에 의해 암묵적으로 생성되며 코드 어디서든 참조할 수 있다. 자바스크립트의 `this`는 함수가 호출되는 방식에 따라 `this` 바인딩될 값이 동적으로 결정된다.

```jsx
// 객체 리터럴
const circle = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체를 가리킨다.
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

```jsx
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  return 2 * this.radius;
};

// 인스턴스 생성
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

### ✏️ 함수 호출 방식과 this 바인딩

1. **일반 함수 호출**

   기본적으로 `this`에는 전역 객체가 바인딩되고 `strict mode`에서는 `this`에 `undefined`가 바인딩된다. 메서드 내의 중첩 함수라도 일반 함수로 호출되면 함수 내부의 this에는 전역 객체가 바인딩된다. 콜백 함수도 일반 함수로 호출되면 `this`에 전역 객체가 바인딩 된다.

   메서드 내부 중첩 함수나 콜백 함수에 `this` 바인딩을 메서드의 `this`와 일치시키기 위한 방법은 `that`, `Function.prototype.apply/call/bind` 메서드, 화살표 함수를 활용하는 것이다.

2. **메서드 호출**

   메서드 내부의 `this`에는 메서드를 호출한 객체(메서드를 호출할 때 메서드 이름 앞의 마침표 연산자 앞에 기술한 객체)가 바인딩 된다. `this`는 메서드를 소유한 객체가 아닌 **메서드를 호출한 객체에 바인딩** 된다.

   만약 메서드를 일반 변수에 할당하여 호출하면 일반함수로 호출된다. 프로토타입 메서드 내부에서 사용된 `this`도 일반 메서드와 동일하게 해당 메서드를 호출한 객체에 바인딩 된다.

3. **생성자 함수 호출**

   생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩되고 `new` 연산자와 함께 호출하지 않으면 일반 함수로 호출된다.

4. `**Function.prototype.apply/call/bind` 메서드에 의한 간접 호출\*\*

   `apply`, `call`, `bind` 메서드는 `Function.prototype`의 메서드다. 이 메서드는 모든 함수가 상속받아서 사용할 수 있다.

   - `Function.prototype.apply`, `Function.prototype.call`
     `this`로 사용할 객체를 전달하고 함수를 호출한다. `apply` 메서드는 함수의 인수를 배열로 묶어 전달하고 `call` 메서드는 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
   - `Function.prototype.bind`
     함수를 호출하지 않고 `this`로 사용할 객체만 전달하는데 명시적으로 호출을 해줘야 한다.
     `bind` 함수는 메서드의 `this`와 메서드 내부의 중첩 함수나 콜백 함수의 `this`가 불일치하는 문제를 해결하기 위해 사용된다.

| 함수 호출 방법                                             | this 바인딩                                                            |
| ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| 일반 함수 호출                                             | 전역 객체                                                              |
| 메서드 호출                                                | 메서드를 호출한 객체                                                   |
| 생성자 함수 호출                                           | 생성자 함수가 생성할 인스턴스                                          |
| Function.prototype.apply/call/bind 메서드에 의한 간접 호출 | Function.prototype.apply/call/bind 메서드에 첫 번째 인수로 전달된 객체 |
