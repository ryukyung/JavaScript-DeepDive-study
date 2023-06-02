---
week: 13주차
---

# 모던 자바스크립트 Deep Dive CH46. 제너레이터와 async/await

## 제너레이터란

**제너레이터**<sup>generator</sup>는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수로, ES6에서 도입되었다. 

> ⭐ **제너레이터 vs. 일반 함수**
>
> 1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 **양도**<sup>yield</sup>할 수 있다.
> 2. 제너레이터 함수는 함수 호줄자와 함수의 상태를 주고받을 수 있다.
> 3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.

## 제너레이터 함수의 정의

* `function*` 키워드로 선언
  - 애스터리스크(`*`)의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관 없음
    > 그러나 일관성 유지를 위해 function 키워드 바로 뒤에 붙이는 것을 권장한다.
* 하나 이상의 yield 표현식 포함
* 화살표 함수로 정의 불가
* new 연산자와 함께 생성자 함수로 호출 불가

```js
// 제너레이터 함수 선언문
function* aGenerator() {
  yield 1;
}

// 제너레이터 함수 표현식
const bGenerator = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  * cGenerator() {
    yield 1;
  }
};

// 제너레이터 클래스 메서드
class ThisIsClass {
  * dGenerator() {
    yield 1;
  }
}
```

## 제너레이터 객체

* 제너레이터 함수 호출 시, 제너레이터 객체를 생성해 반환
* 제너레이터 객체: 이터러블이면서 이터레이터
  - `Symbol.iterator` 메서드를 상속받는 이터러블
  - value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유하는 이터레이터
* next 메서드 이외에 return, throw 메서드를 갖음
  - next 메서드 호출
    + yield 표현식까지 코드 블록 실행
    + yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
  - return 메서드 호출
    + 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환
  - throw 메서드 호출
    + 인수로 전달받은 에러를 발생시킴
    + undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체 반환환

## 제너레이터의 일시 중지와 재개

제너레이터는 yield 키워드, next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개할 수 있다.

1. 제너레이터 객체의 next 메서드를 호출하면 yield 표현식까지 실행되고 일시 중지된다.
   > 이때, 함수의 제어권이 호출자로 양도된다.
2. 이후 필요한 시점에 호출자가 또다시 next 메서드를 호출하면 일시 중지된 코드부터 실행을 재개하기 시작하여 다음 yield 표현식까지 실행되고 또 다시 일시 중지된다.
3. 제너레이터 함수가 끝까지 실행되면 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값이, done 프로퍼티에는 true가 할당된다.

> ⭐ **next 메서드가 반환하는 이터레이터 리절트 객체**
>
> - value 프로퍼티: yield 표현식에서 yield된 값 (yield 키워드 뒤의 값)
> - done 프로퍼티: 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값

### next 메서드에 전달한 인수

next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당된다.

아래 예제와 같이 일반적으로 제너레이터의 반환값은 의미가 없기 때문에 return은 종료의 의미로만 사용해야 한다.

```js
function* myFunc() {
  // 첫 번째 next 메서드 호출, x 변수에는 아무것도 할당되지 않음
  const x = yield 1;

  // 두 번째 next 메서드 호출
  // x 변수에는 인수로 전달한 10이 할당
  // y 변수에는 아무것도 할당되지 않음
  const y = yield (x + 10);

  // 세 번째 next 메서드 호출
  // y 변수에 인수로 전달한 20이 할당
  // 최종적으로 x: 10, y: 20
  return x + y;
}

const generator = myFunc(0);
let res = generator.next(); // { value: 1, done: false }
res = generator.next(10); // { value: 20, done: false }
res = generator.next(20); // { value: 30, done: true }
```

## 제너레이터의 활용

1. 이터러블의 구현
   - 이터레이션 프로토콜을 준수하여 이터러블을 생성하는 방식보다 간단히 구현 가능
2. 비동기 처리
   - 제너레이터 함수의 next 메서드, yield 표현식: 함수 호출자와 함수의 상태를 주고 받을 수 있음
   - 이러한 특성을 활용해 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현 가능
     > *프로미스의 후속 처리 메서드 없이 비동기 처리 결과를 반환하도록 구현 가능*

## async/await

ES8에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 async/await가 도입되었다.

async/await는 프로미스를 기반으로 동작하며, 프로미스의 후속 처리 메서드 없이 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

### async 함수

await 키워드는 반드시 async 함수 내부에서 사용해야 한다. async 키워드를 사용해 async 함수를 정의할 수 있고, 언제나 프로미스를 반환한다. async 함수가 명시적으로 프로미스를 반환하지 않더라도 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

```js
// async 함수 선언문
async function apple(x) { return x; }

// async 함수 표현식
const bpple = async function (x) { return x; };

// async 화살표 함수
const cpple = async x => x;

// async 메서드
const obj = {
  async dpple(x) { return x; }
};

// async 클래스 메서드
// 클래스의 constructor 메서드는 async 메서드가 될 수 없음
// async 함수는 언제나 프로미스를 반환해야 하지만, constructor 메서드는 인스턴스를 반환해야 하기 때문
class ThisIsClass {
  async epple(x) { return x; }
}
```

### await 키워드

await 키워드는 반드시 프로미스 앞에서 사용해야 하며, 프로미스가 settled 상태가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환한다.

모든 프로미스에 await 키워드를 사용하는 것은 주의해야 한다. 아래 예제와 같이 서로 연관이 없이 개별적으로 수행되는 비동기 처리라면 모든 프로미스에 await 키워드를 사용하지 않아도 된다. 그러나 비동기 처리의 처리 순서가 보장되어야 하는 경우에는 모든 프로미스에 await 키워드를 사용해 순차적으로 처리해야 한다.

<table>
<tr align="center">
<td>Before</td>
<td>After</td>
</tr>
<tr>
<td>

```js
async function apple() {
  const a = await new Promise(resolve => setTimeout(() => resolve(1), 3000));
  const b = await new Promise(resolve => setTimeout(() => resolve(2), 2000));
  const c = await new Promise(resolve => setTimeout(() => resolve(3), 1000));

  console.log([a, b, c]); // [1, 2, 3]
}

apple(); // 약 6초 소요
```

</td>
<td>

```js
async function apple() {
  const res = await Promise.all([
    new Promise(resolve => setTimeout(() => resolve(1), 3000)),
    new Promise(resolve => setTimeout(() => resolve(2), 2000)),
    new Promise(resolve => setTimeout(() => resolve(3), 1000))
  ]);

  console.log(res); // [1, 2, 3]
}

apple(); // 약 3초 소요
```

</td>
</tr>  
</table>

### 에러 처리

비동기 처리를 위한 콜백 패턴은 `try...catch` 문을 사용해 에러를 캐치할 수 없지만, async/await에서는 가능하다. 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확하다.

```js
const fetch = require('node-fetch');

const foo = async () => {
  try {
    const wrongUrl = 'wrong.url';

    const response = await fetch(wrongUrl);
    const data = await response.json();

    console.log(data);
  } catch (err) {
    console.log(err);
  }
};
```

async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환한다. 따라서 async 함수를 호출하고 `Promise.prototype.catch` 후속 처리 메서드를 사용해 에러를 캐치할 수도 있다.

```js
const fetch = require('node-fetch');

const foo = async () => {
  const wrongUrl = 'wrong.url';

  const response = await fetch(wrongUrl);
  const data = await response.json();

  return data;
};

foo()
  .then(console.log)
  .catch(console.error);
```














