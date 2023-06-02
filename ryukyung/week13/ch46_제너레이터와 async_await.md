### ✏️ 제너레이터: generator

: [ES6] 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수

1. **제너레이터 함수는 호출자에게 함수 실행의 제어권을 양도할 수 있다.**

   일반 함수: 함수 호출자(caller)는 함수를 호출한 이후 함수 실행 제어X

   제너레이터 함수: 함수 호출자가 함수 실행을 일시 중지 시키거나 재개시킬 수 있다.

2. **제너레이터 함수는 함수 호출자와 함수의 상태를 주고 받을 수 있다.**

   일반 함수: 함수가 실행되고 있는 동안에는 함수 외부에서 함수 내부로 값을 전달하여 함수의 상태를 변경할 수 없다.

   제너레이터 함수: 함수 호출자에게 상태를 전달할 수 있고 함수 호출자로부터 상태를 전달받을 수도 있다.

3. **제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.**

   일반 함수: 호출 시 함수 코드를 일괄 실행하고 값을 반환

   제너레이터 함수: 호출 시 함수 코드를 실행하는 것이 아니라 이터러블이면서 동시에 이터레이터인 제너레이터 객체 반환

### ✏️ 제너레이터 함수의 정의

`function*` 키워드로 선언하며 하나 이상의 yield 표현식을 포함하며 화살표 함수, `new` 연산자와 함께 생성자 함수로 선언할 수 없다.

### ✏️ 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환하고 반환한 객체는 `iterable`이면서 `iterator`이다.

```jsx
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수 호출 시 제너레이터 객체 반환
// 제너레이터 객체는 이터러블이면서 이터레이터
const generator = genFunc();

// Symbol.iterator 메서드를 직접 구현하거나 프로토타입 체인으로 상속 받음
console.log(Symbol.iterator in generator); // true

// 이터레이터는 net 메서드 가짐
console.log('next' in generator); // true
```

```jsx
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next());
// {value: 1, done: false} : 제너레이터 함수의 yield 표현식까지 코드 블록 실행
console.log(generator.return('End!'));
// {value: "End!", done: true} : iterator에는 없는 return, throw 메서드를 가짐
console.log(generator.throw('Error'));
// {value: undefined, done: true} : 인수로 전달 받은 에러 발생시킴
```

### ✏️ 제너레이터의 일시 중지와 재개

```jsx
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.next()); // {value: 2, done: false}
console.log(generator.next()); // {value: 3, done: false}

// 남은 yield 표현식X: 제너레이터 함수의 마지막까지 실행
console.log(generator.next()); // {value: undefined, done: true}
```

```jsx
function* genFunc() {
  // [1] next 호출: 1번 yield 표현식까지 실행, x에 할당X(: 2번 next 호출 시 결정)
  const x = yield 1;
  // [2] next 호출: x에 10 할당, 2번 yield 표현식까지 실행, y에 할당X
  const y = yield x + 10;
  // [3] next 호출: y에 20 할당, 함수 끝까지 실행
  // 제너레이터의 반환값 의미X => 값을 반환할 필요X, return은 종료의 의미로만 사용
  return x + y;
}

const generator = genFunc(0);

let res = generator.next();
console.log(res); // {value: 1, done: false}

res = generator.next(10);
console.log(res); // {value: 20, done: false}

res = generator.next(20);
console.log(res); // {value: 30, done: true}
```

### ✏️ async/await

ES8에서 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 `async/await`가 도입되었는데 이는 프로미스 기반으로 동작하며 프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

1. **async 함수**

   ```jsx
   class MyClass {
     async constructor() { }
     // SyntaxError: Class constructor may not be an async method
   }

   const myClass = new MyClass();
   ```

   `await` 키워드는 반드시 `async` 함수 내부에서 사용해야 하며 `async` 함수는 언제나 프로미스를 반환한다. 클래스의 `constructor` 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환하기 때문에 클래스의 `constructor` 메서드는 `async` 메서드가 될 수 없다.

2. **await 키워드**

   반드시 프로미스 앞에서 사용해야 하며 `await` 키워드는 프로미스가 settled 상태가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 `resolve`한 처리 결과를 반환한다.

   서로 연관이 없이 개별적으로 수행되는 비동기 처리에서는 순차적으로 처리할 필요가 없지만 비동기 처리의 순서가 보장되어야 할 때는 모든 프로미스에 `await` 키워드를 사용하여 순차적으로 처리한다.

3. **에러 처리**

   콜백 함수를 인수로 전달받는 비동기 함수와 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확하므로 `async/await`에서 에러 처리는 `try…catch` 문을 사용할 수 있다.

   `async` 함수 내에서 `catch` 문을 사용해서 에러 처리를 하지 않으면 `async` 함수는 발생한 에러를 `reject`하는 프로미스를 반환하므로 `async` 함수를 호출하고 `Promise.prototype.catch` 후속 처리 메서드를 사용해 에러를 캐치할 수도 있다.
