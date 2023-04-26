---
week: 8주차
---

# 모던 자바스크립트 Deep Dive CH31. RegExp

## 목차

- [정규 표현식이란](#정규-표현식이란)
- [정규 표현식의 생성](#정규-표현식의-생성)
- [RegExp 메서드](#regexp-메서드)
  - [RegExp.prototype.exec](#regexpprototypeexec)
  - [RegExp.prototype.test](#regexpprototypetest)
  - [String.prototype.match](#stringprototypematch)
- [플래그](#플래그)
- [패턴](#패턴)
  - [문자열 검색](#문자열-검색)
  - [임의의 문자열 검색](#임의의-문자열-검색)
  - [반복 검색](#반복-검색)
  - [OR 검색](#or-검색)
  - [NOT 검색](#not-검색)
  - [시작 위치로 검색](#시작-위치로-검색)
  - [마지막 위치로 검색](#마지막-위치로-검색)
- [자주 사용하는 정규 표현식](#자주-사용하는-정규-표현식)
  - [특정 단어로 시작하는지 검사](#특정-단어로-시작하는지-검사)
  - [특정 단어로 끝나는지 검사](#특정-단어로-끝나는지-검사)
  - [숫자로만 이루어진 문자열인지 검사](#숫자로만-이루어진-문자열인지-검사)
  - [하나 이상의 공백으로 시작하는지 검사](#하나-이상의-공백으로-시작하는지-검사)
  - [아이디로 사용 가능한지 검사](#아이디로-사용-가능한지-검사)
  - [메일 주소 형식에 맞는지 검사](#메일-주소-형식에-맞는지-검사)
  - [핸드폰 번호 형식에 맞는지 검사](#핸드폰-번호-형식에-맞는지-검사)
  - [특수 문자 포함 여부 검사](#특수-문자-포함-여부-검사)

## 정규 표현식이란

**정규 표현식**<sup>regular expression</sup>은 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 **형식 언어**<sup>formal language</sup>로, 자바스크립트의 고유 문법은 아니다. 자바스크립트는 **펄**<sup>Perl</sup>의 정규 표현식 문법을 ES3부터 도입했다.

정규 표현식은 문자열을 대상으로 *특정 패턴과 일치하는 문자열을 검색하거나 추출 또는 치환*할 수 있는 패턴 매칭 기능을 제공한다. 정규 표현식을 사용하면 반복문이나 조건문 없이 패턴을 정의하고 테스트하는 것으로 간단히 체크할 수 있지만, 가독성이 좋지 않다는 문제점이 있다.

## 정규 표현식의 생성

정규 표현식 객체는 아래 두가지 방법으로 생성할 수 있다.

1. 정규 표현식 리터럴 사용 (일반적)
    - 표현 방식

      ![image](../images/ch31-01.png)

      > 패턴: 문자열의 일정한 규칙을 표현
      > 
      > 플래그: 정규 표현식의 검색 방식을 설정

    - 예제
      ```js
      const testString = 'super duper ultra happy';

      // 패턴: happy
      // 플래그 i: 대소문자 구별 없이 검색
      const regexp = /happy/i;

      // test: 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환
      regexp.test(testString);
      ```

2. `RegExp` 생성자 함수 사용
    - 예제
      ```js
      /**
       * pattern: 정규 표현식의 패턴
       * flags: 정규 표현식의 플래그(g, i, m, u, y)
       */
      new RegExp(pattern[, flags])

      const regexp = new RegExp(/happy/i); // ES6
      // new RegExp(/happy/, 'i');
      // new RegExp('happy', 'i');
      ```

## RegExp 메서드

### RegExp.prototype.exec

인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 배열로 반환한다. 매칭 결과가 없는 경우 null을 반환한다.

```js
const target = 'super duper ultra happy';
const regexp = /happy/;

regexp.exec(target);
// [ 'happy', index: 18, input: 'super duper ultra happy', groups: undefined ]
```

> ⭐ **주의**
>
> g 플래그(문자열 내의 모든 패턴 검색)를 지정해도 첫 번째 매칭 결과만 반환한다.

### RegExp.prototype.test

인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭 결과를 불리언 값으로 반환한다.

```js
const target = 'super duper ultra happy';
const regexp = /happy/;

regexp.test(target);
```

### String.prototype.match

대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환한다. exec 메서드와는 다르게 g 플래그가 지정되면 모든 매칭 결과를 배열로 반환한다.

```js
const target = 'super happy duper happy ultra happy';
const regexp = /happy/g;

target.match(regexp);
// [ 'happy', 'happy', 'happy' ]
```

## 플래그

플래그는 총 6개가 있는데, 그 중 세가지만 알아보자.

* i: Ignore case, 대소문자를 구별하지 않고 패턴을 검색한다.
* g: Global, 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색한다.
* m: Multi line, 문자열의 행이 바뀌더라도 패턴 검색을 계속한다.

플래그는 옵션이라 선택적으로 사용할 수 있다. 플래그를 사용하지 않은 경우 대소문자를 구별해서 패턴을 검색하고 첫 번째 매칭한 대상만 검색하고 종료한다.

또한 순서와 상관없이 하나 이상의 플래그를 동시에 설정할 수 있다. 

## 패턴

정규 표현식의 패턴은 `/`로 열고 닫으며 문자열의 따옴표는 생략한다. 또한 메타문자 또는 기호로 표현할 수 있다.

### 문자열 검색

정규 표현식의 패턴에 문자 또는 문자열을 지정한다.

```js
/a/

/apple/
```

### 임의의 문자열 검색

`.`은 임의의 문자 한 개를 의미한다.

```js
const target = 'super duper ultra happy';

// 임의의 3자리 문자열을 대소문자를 구별하여 전역으로 검색
const regexp = /.../g;

target.match(regexp);
// [ 'sup', 'er ', 'dup', 'er ', 'ult', 'ra ', 'hap' ]
```

### 반복 검색

* `{m,n}`: 앞선 패턴이 최소 m번, 최대 n번 반복되는 문자열
  ```js
  const target = 'XYZ XY YZ zZ ZZ';

  // 'Z'가 최소 1번, 최대 2번 반복되는 문자열 전역 검색
  const regexp = /Z{1,2}/g;

  target.match(regexp);
  // [ 'Z', 'Z', 'Z', 'ZZ' ]
  ```
* `{n}`: 앞선 패턴이 n번 반복되는 문자열 (= `{n,n}`)
  ```js
  const target = 'XYZ XY YZ zZ ZZ';

  // 'Z'가 2번 반복되는 문자열 전역 검색
  const regexp = /Z{2}/g;

  target.match(regexp);
  // [ 'ZZ' ]
  ```
* `{n,}`: 앞선 패턴이 최소 n번 이상 반복되는 문자열
  ```js
  const target = 'XYZ XY YZ zZ ZZ';

  // 'Z'가 최소 2번 이상 반복되는 문자열 전역 검색
  const regexp = /Z{2,}/g;

  target.match(regexp);
  // [ 'ZZ' ]
  ```
* `+`: 앞선 패턴이 최소 한번 이상 반복되는 문자열 (= `{1,}`)
  ```js
  const target = 'XYZ XY YZ zZ ZZ';

  // 'Z'가 최소 1번 이상 반복되는 문자열 전역 검색
  const regexp = /Z+/g;

  target.match(regexp);
  // [ 'Z', 'Z', 'Z', 'ZZ' ]
  ```
* `?`: 앞선 패턴이 최대 한 번(0번 포함) 이상 반복되는 문자열 (= `{0,1}`)
  ```js
  const target = 'color colour';

  // 'colo' 다음 'u'가 최대 한 번(0번 포함) 이상 반복되고,
  // 'r'이 이어지는 문자열 전역 검색
  const regexp = /colou?r/g;

  target.match(regexp);
  // [ 'color', 'colour' ]
  ```

### OR 검색

`|`은 or의 의미를 갖는다. `[]` 내의 문자도 or로 동작하며 그 뒤에 `+`를 사용하면 앞선 패턴을 한 번 이상 반복한다.

```js
// 'X' 또는 'Y'를 전역 검색
/X|Y/g

// 'X' 또는 'Y'가 한 번 이상 반복되는 문자열 전역 검색
/X+|Y+|/g
/[XY]+/g

// -: 범위 지정
// 'A' ~ 'Z'가 한 번 이상 반복되는 문자열 전역 검색
/[A-Z]+/g
// 대소문자 구분 X
/[A-Za-z]+/g
// 숫자 검색
/[0-9]+/g
/[\d,]+/g // \d: 숫자를 의미
```

* `\d`: 숫자를 의미 (= `[0-9]`)
* `\D`: 숫자가 아닌 문자를 의미
* `\w`: 알파벳, 숫자, 언더스코어를 의미 (= `[A-Za-z0-9_]`)
* `\W`: 알파벳, 숫자, 언더스코어가 아닌 문자를 의미

### NOT 검색

`[]` 내의 `^`은 not의 의미를 갖는다.

```js
// 숫자를 제외한 문자
/[^0-9]+/g
```

### 시작 위치로 검색

`[]` 밖의 `^`은 문자열의 시작을 의미한다.

```js
// 'hello'로 시작하는지
/^hello/
```

### 마지막 위치로 검색

`$`은 문자열의 마지막을 의미한다.

```js
// 'hello'로 끝나는지
/hello$/
```

## 자주 사용하는 정규 표현식

### 특정 단어로 시작하는지 검사

```js
// 'http://' 또는 'https://'로 시작하는지 검사
/^https?:\/\//
/^(http|https)\/\//
```

### 특정 단어로 끝나는지 검사

```js
// 'com'으로 끝나는지 검사
/com$/
```

### 숫자로만 이루어진 문자열인지 검사

```js
// 처음과 끝이 숫자이고, 최소 한 번 이상 반복되는 문자열
/^\d+$/
```

### 하나 이상의 공백으로 시작하는지 검사

```js
// \s: 여러 가지 공백 문자를 의미 (= [\t\r\n\v\f])
/^[\s]+/
```

### 아이디로 사용 가능한지 검사

```js
// 4~10자리로 이루어진 알파벳 대소문자 또는 숫자
/^[A-Za-z0-9]{4,10}$/
```

### 메일 주소 형식에 맞는지 검사

```js
/^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/
```

### 핸드폰 번호 형식에 맞는지 검사

```js
// 000-000-0000 또는 000-0000-0000
/^\d{3}-\d{3,4}-\d{4}$/
```

### 특수 문자 포함 여부 검사

```js
// A~Z, a~z, 0~9 이외의 문자
/[^A-Za-z0-9]/gi

// 특수 문자 선택적 검사
(/[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]/gi)
```