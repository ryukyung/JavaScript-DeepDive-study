---
week: 11주차
---

# 모던 자바스크립트 Deep Dive CH37. set과 Map

## Set

Set 객체는 중복되지 않는 유일한 값들의 집합으로, 수학적 집합을 구현하기 위한 자료구조다. 따라서 교집합, 합집합, 차집합, 여집합 등을 구현할 수 있다.

> ⭐ **Set 객체 vs. 배열**
>
> 구분 | 배열 | Set 객체
> :--- | :---: | :---:
> 동일한 값을 중복하여 포함할 수 있는가? | O | X
> 요소 순서에 의미가 있는가? | O | X
> 인덱스로 요소에 접근할 수 있는가? | O | X

### Set 생성자 함수

Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다. 이때 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다. 이 특성을 통해 배열에서 중복된 요소를 제거할 수 있다.

생성자 함수에 인수를 전달하지 않은 경우 빈 Set 객체를 생성한다.

### 요소 개수 확인

* `Set.prototype.size` 프로퍼티 사용
  - Set 객체의 요소 개수 확인
  - getter 함수만 존재하는 접근자 프로퍼티 → Set 객체의 요소 개수 변경 불가

### 요소 추가

* `Set.prototype.add` 메서드 사용
  - 새로운 요소가 추가된 Set 객체를 반환
    > add 메서드 호출 후, add 메서드를 연속적으로 호출<sup>method chaining</sup> 가능
  - 중복된 요소 추가시 에러 발생 없이 무시됨
  - NaN: 중복 추가 불가
  - +0, -0: 중복 추가 불가

### 요소 존재 여부 확인

* `Set.prototype.has` 메서드 사용
  - 특정 요소의 존재 여부를 나타내는 불리언 값 반환

### 요소 삭제

* `Set.prototype.delete` 메서드 사용
  - 삭제하려는 요소값을 인수로 받아 삭제 성공 여부를 나타내는 불리언 값 반환
    > 연속적 호출 불가능
  - 존재하지 않는 Set 객체의 요소값을 인수로 전달한 경우 에러 발생 없이 무시됨

### 요소 일괄 삭제

* `Set.prototype.clear` 메서드 사용
  - 객체의 모든 요소 삭제
  - 언제나 undefined 반환

### 요소 순회

* `Set.prototype.forEach` 메서드 사용
  - 콜백 함수와 forEach 메서드의 콜백 함수 내부에서 this로 사용될 객체를 인수로 전달
    > 콜백 함수는 아래 3개의 인수를 전달받는다. 첫 번째 인수와 두 번째 인수는 같은 값이다.
    > 
    > * 첫 번째 인수: 현재 순회 중인 요소값
    > * 두 번째 인수: 현재 순회 중인 요소값
    > * 세 번째 인수: 현재 순회 중인 Set 객체 자체

> 💭 Set 객체를 순회하는 순서
> 
> — *ECMAScript 사양에 규정되어 있지는 않지만, 다른 이터러블의 순회와 호환성을 유지하기 위해 요소가 추가된 순서를 따른다.*

> ⭐ **Set 객체는 이터러블이다.**
>
> 따라서 `for...of` 문으로 순회할 수 있고, 스프레드 문법과 배열 구조 분해 할당의 대상이 될 수 있다.

### 집합 연산

* 교집합 $A ∩ B$: 집합 A와 집합 B의 공통 요소로 구성
  - 방법 1
    ```js
    Set.prototype.intersection = function (set) {
      const result = new Set();

      for (const value of set) {
        // 2개의 set의 요소가 공통되는 요소이면 교집합의 대상
        if (this.has(value)) result.add(value);
      }

      return result;
    };
    ```
  - 방법 2
    ```js
    Set.prototype.intersection = function (set) {
      return new Set([... this].filter(v => set.has(v)));
    };
    ```
* 합집합 $A ∪ B$: 집합 A와 집합 B의 중복 없는 요소로 구성
  - 방법 1
    ```js
    Set.prototype.union = function (set) {
      // this(Set 객체)를 복사
      const result = new Set(this);

      for (const value of set) {
        // 합집합은 2개의 Set 객처/의 모든 요소루 구성된 집합이다. 중복된 요소는 포함되지 않는다.
        result.add(value);
      }

      return result;
    };
    ```
  - 방법 2
    ```js
    Set.prototype.union = function (set) {
      return new Set([...this, ...set]);
    };
    ```
* 차집합 $A - B$: 집합 A에는 존재하지만 집합 B에는 존재하지 않는 요소로 구성
  - 방법 1
    ```js
    Set.prototype.difference = function (set) {
      // this(Set 객체)를 복사
      const result = new Set(this);

      for (const value of set) {
        // 차집합은 어느 한쪽 집합에는 존재하지만 다른 한쪽 집합에는 존재하지 않는 요소로 구성된 집합이다.
        result.delete(value);
      }

      return result;
    };
    ```
  - 방법 2
    ```js
    Set.prototype.difference = function (set) {
      return new Set([...this].filter(v => !set.has(v)));
    };
    ```
* $A ⊆ B$: 집합 A가 집합 B에 포함되는 경우
  - 집합 A: 집합 B의 부분 집합
  - 집합 B: 집합 A의 상위 집합
  - 방법 1
    ```js
    // this가 subset의 상위 집합인지 확인
    Set.prototype.isSuperset = function (subset) {
      for (const value of subset) {
        // superset의 모든 요소가 subsets 모든 요소를 포함하는지 확인
        if (!this.has(value)) return false;
      }

      return true;
    };
    ```
  - 방법 2
    ```js
    Set.prototype.isSuperset = function (subset) {
      const supersetArr = [...this];
      return [...subset].every(v => supersetArr.includes(v));
    };
    ```

## Map

Map 객체는 키와 값의 쌍으로 이루어진 컬렉션이다.

> ⭐ **객체 vs. Map 객체**
>
> 구분 | 객체 | Map 객체
> :--- | :---: | :---:
> 키로 사용할 수 있는 값 | 문자열 또는 심벌 값 | 객체를 포함한 모든 값
> 이터러블 | X | O
> 요소 개수 확인 | `Object.keys(obj).length` | `map.size`

### Map 생성자 함수

Map 생성자 함수는 이터러블(키와 값의 쌍으로 이루어진 요소로 구성)을 인수로 전달받아 Map 객체를 생성한다. 인수로 전달한 이터러블에 중복된 키를 갖는 요소가 존재하면 값이 덮어써진다. 따라서 Map 객체에는 중복된 키를 갖는 요소가 존재할 수 없다.

생성자 함수에 인수를 전달하지 않은 경우 빈 Map 객체를 생성한다.

### 요소 개수 확인

* `Map.prototype.size` 프로퍼티 사용
  - Map 객체의 요소 개수 확인
  - getter 함수만 존재하는 접근자 프로퍼티 → Map 객체의 요소 개수 변경 불가

### 요소 추가

* `Map.prototype.set` 메서드 사용
  - 새로운 요소가 추가된 Map 객체 반환
    > set 메서드 호출 후, set 메서드를 연속적으로 호출<sup>method chaining</sup> 가능
  - 중복된 키를 갖는 요소 추가 시 에러 발생 없이 값이 덮어써짐
  - NaN: 중복 추가 불가
  - +0, -0: 중복 추가 불가

### 요소 취득

* `Map.prototype.get` 메서드 사용
  - Map 객체에서 인수로 전달받은 키를 갖는 값 반환
  - 전달받은 키를 갖는 요소가 존재하지 않을 경우 undefined 반환

### 요소 존재 여부 확인

* `Map.prototype.has` 메서드 사용
  - 특정 요소의 존재 여부를 나타내는 불리언 값 반환

### 요소 삭제

* `Map.prototype.delete` 메서드 사용
  - 삭제하려는 키를 인수로 받아 삭제 성공 여부를 나타내는 불리언 값 반환
    > 연속적 호출 불가능
  - 존재하지 않는 Map 객체의 키를 인수로 전달한 경우 에러 발생 없이 무시됨

### 요소 일괄 삭제

* `Map.prototype.clear` 메서드 사용
  - 언제나 undefined 반환

### 요소 순회

* `Map.prototype.forEach` 메서드 사용
  - 콜백 함수와 forEach 메서드의 콜백 함수 내부에서 this로 사용될 객체를 인수로 전달
    > 콜백 함수는 아래 3개의 인수를 전달받는다.
    > 
    > * 첫 번째 인수: 현재 순회 중인 요소값
    > * 두 번째 인수: 현재 순회 중인 요소키
    > * 세 번째 인수: 현재 순회 중인 Map 객체 자체

> 💭 Map 객체를 순회하는 순서
> 
> — *ECMAScript 사양에 규정되어 있지는 않지만, 다른 이터러블의 순회와 호환성을 유지하기 위해 요소가 추가된 순서를 따른다.*

> ⭐ **Map 객체는 이터러블이다.**
>
> 따라서 `for...of` 문으로 순회할 수 있고, 스프레드 문법과 배열 구조 분해 할당의 대상이 될 수 있다.
>
> Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드를 제공한다.
>
> Map 메서드 | 설명
> --- | ---
> `Map.prototype.keys` | Map 객체에서 요소키를 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
> `Map.prototype.values` | Map 객체에서 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
> `Map.prototype.entries` | Map 객체에서 요소키와 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
