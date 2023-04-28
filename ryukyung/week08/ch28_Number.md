### ✏️ Number 생성자 함수

Number 객체는 생성자 함수 객체이고 new 연산자와 함께 호출하여 Number 인스턴스를 생성할 수 있다. Number 생성자 함수에 인수를 전달하지 않고 new 연산자와 함께 호출하면 [[NumberData]] 내부 슬롯에 0을 할당한 Number 래퍼 객체를 생성한다.

```jsx
const numObj1 = new Number();
// Number {0} [[Prototype]]: Number[[PrimitiveValue]]: 0
const numObj2 = new Number(5);
// Number {5} [[Prototype]]: Number[[PrimitiveValue]]: 5
const numObj3 = new Number('gaori');
// Number {NaN} [[Prototype]]: Number[[PrimitiveValue]]: NaN
```

### ✏️ Number 프로퍼티

1. **Number.EPSILON**

   1과 1보다 큰 숫자 중에서 가장 작은 숫자와의 차이(약`2.2204460492503130808472633361816 * 10^16`)와 같다. 부동소수점 산술 연산은 정확한 결과를 기대하기 어려운데 이를 해결하기 위해 사용한다.

   ```jsx
   // a와 b를 뺀 값의 절대값이 Number.EPSILON보다 작으면 같은 수로 인정한다.
   function isEqual(a, b) {
     return Math.abs(a - b) < Number.EPSILON;
   }

   isEqual(0.1 + 0.2, 0.3); // true
   ```

2. **Number.MAX_VALUE**

   자바스크립트에서 표현할 수 있는 가장 큰 양수값(`1.7976931348623157 * 10^308`), 이보다 큰 숫자는 Infinity

3. **Number.MIN_VALUE**

   자바스크립트에서 표현할 수 있는 가장 작은 양수값(`5 * 10^(-324)`), 이보다 작은 숫자는 0

4. **Number.MAX_SAFE_INTEGER**

   자바스크립트에서 안전하게 표현할 수 있는 가장 큰 정수값(`9007199254740991`)

5. **Number.MIN_SAFE_INTEGER**

   자바스크립트에서 안전하게 표현할 수 있는 가장 작은 정수값(`-9007199254740991`)

6. **Number.POSITIVE_INFINITY**

   양의 무한대를 나타내는 숫자값 `Infinity`와 같다.

7. **Number.NEGATIVE_INFINITY**

   음의 무한대를 나타내는 숫자값 `-Infinity`와 같다.

8. **Number.NaN**

   숫자가 아님(Not a Number)을 나타내는 숫자값, `Number.NaN`은 `window.NaN`과 같다.

### ✏️ Number 메서드

1. **Number.isFinite**

   인수로 전달된 숫자값이 정상적인 유한수(Infinity | -Infinity)인지 검사하여 그 결과를 불리언 값으로 반환

   ```jsx
   // 인수가 정상적인 유한수라면 true
   Number.isFinite(0); // true
   Number.isFinite(Number.MAX_VALUE); // true
   Number.isFinite(Number.MIN_VALUE); // true

   // 인수가 무한수라면 false
   Number.isFinite(Infinity); // false
   Number.isFinite(-Infinity); // false

   // Number.isFinite: 인수를 숫자로 암묵적 타입 변환X
   Number.isFinite(null); // false

   // isFinite: 인수를 숫자로 암묵적 타입 변환O
   isFinite(null); // -> true
   ```

2. **Number.isInteger**

   인수로 전달된 숫자값이 정수인지 검사하여 그 결과를 불리언 값으로 반환하는데 인수를 숫자로 암묵적 타입변환하지 않는다.

   ```
   Number.isInteger(0);        // true
   Number.isInteger(1);        // true
   Number.isInteger(0.1);      // false
   Number.isInteger('1');      // false
   Number.isInteger(true);     // false
   Number.isInteger(Infinity); // false
   ```

3. **Number.isNaN**

   인수로 전달된 숫자값이 NaN인지 검사하여 그 결과를 불리언 값으로 반환하는데 인수를 숫자로 암묵적 타입변환하지 않는다.

4. **Number.isSafeInteger**

   인수로 전달된 숫자값이 **안전한 정수**인지 검사하여 그 결과를 불리언 값으로 반환하는데 인수를 숫자로 암묵적 타입 변환하지 않는다.

   🪝 **안전한 정수**: `-(253-1) ~ 253-1` 사이의 정수값

5. **Number.prototype.toExponential**

   숫자로 **지수 표기법**으로 변환하여 문자열로 반환한다.

   🪝 **지수 표기법**: 매우 크거나 작은 숫자를 표기할 때 주로 사용하며 e(Exponent) 앞에 있는 숫자를 10의 n승으로 곱하는 형식으로 수를 나타내는 방식

6. **Number.prototype.toFixed**

   숫자를 반올림하여 문자열로 반환한다. 반올림하는 소수점 이하 자릿수를 나타내는 0~20 사이의 정수값을 인수로 전달할 수 있으며 인수를 생략하면 기본값(0)이 지정된다.

7. **Number.prototype.toPrecision**

   인수로 전달받은 전체 자릿수까지 유효하도록 나머지 자릿수를 반올림하여 문자열로 반환한다. 전체 자릿수를 나타내는 0~21 사이의 정수값을 인수로 전달할 수 있으며 인수를 생략하면 기본값(0)이 지정된다.

8. **Number.prototype.toString**

   숫자를 문자열로 변환하여 반환하며 2~36 사이의 정수값을 인수로 전달할 수 있으며 인수를 생략하면 기본값(10진법)이 지정된다.
