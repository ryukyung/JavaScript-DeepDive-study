### ✏️ Set

🪝 **Set vs. Array**

**Set**: 중복되지 않는 유일한 값들의 집합

**Array**: 여러 개의 값을 순차적으로 나열한 자료구조

| 구분                                 | 배열 | Set 객체 |
| ------------------------------------ | ---- | -------- |
| 동일한 값을 중복하여 포함할 수 있다. | O    | X        |
| 요소 순서에 의미가 있다.             | O    | X        |
| 인덱스로 요소에 접근할 수 있다.      | O    | X        |

1. **Set 객체의 생성**

   Set 객체는 Set 생성자 함수로 생성하는데 Set 생성자 함수는 이터러블을 인수로 전달받아 Set객체를 생성하는데 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다.

   ```jsx
   const set1 = new Set(); // Set(0) {}
   const set2 = new Set([1, 2, 2, 3]); // Set(3) { 1, 2, 3 }
   ```

2. **요소 개수 확인 →** `Set.prototype.size`

   ```jsx
   const { size } = new Set([1, 2, 2, 3]); // 3
   Object.getOwnPropertyDescriptor(Set.prototype, 'size');
   // { get: [Function: get size], set: undefined, enumerable: false, configurable: true }
   ```

   size 프로퍼티는 getter함수만 존재하는 접근자 프로퍼티이기 때문에 프로퍼티에 숫자를 할당하여 Set 객체의 요소 개수를 변경할 수 없다.

3. **요소 추가** → \*\*\*\*`Set.prototype.add`

   ```jsx
   const set = new Set(); // Set(0) {}
   set.add(5); // Set(1) { 5 }
   set.add(NaN).add(NaN).add(0).add(-0); // Set(3) { 5, NaN, 0 }
   ```

   add 메서드는 새로운 요소가 추가된 Set 객체를 반환하기 때문에 add 메서드를 호출한 후 add 메서드를 연속적으로 호출할 수 있다. 일치 비교 연산자를 사용하여 NaN을 비교하면`(NaN === NaN)` `false`라고 평가하지만 Set 객체를 사용하면 같다고 평가한다.

   ```jsx
   const set = new Set();
   set
     .add(1)
     .add('a')
     .add(true)
     .add(undefined)
     .add(null)
     .add({})
     .add([])
     .add(() => {})
     .add();
   // Set(8) { 1, 'a', true, undefined, null, {}, [], [Function (anonymous)] }
   ```

   Set 객체는 객체나 배열과 같이 자바스크립트의 모든 값을 요소로 저장할 수 있다.

4. **요소 존재 여부 확인** → `Set.prototype.has`

   ```jsx
   const set = new Set([1, 2, 3]);
   set.has(2); // true
   set.has(4); // false
   ```

   특정 요소의 존재 여부를 나타내는 불리언 값을 반환한다.

5. **요소 삭제** → `Set.prototype.delete`

   ```jsx
   const set = new Set([1, 2, 3]);
   set.delete(2); // Set(2) { 1, 3 }
   ```

   Set 객체는 순서에 의미가 없다. 쉽게 말해서 인덱스가 없기 때문에 delete 메서에는 인덱스가 아닌 삭제하려는 요소 값을 인수로 전달해야 한다.

   delete 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환하기 때문에 add 메서드와 달리 연속적으로 호출할 수 없으며 존재하지 않는 Set 객체의 요소를 삭제하려 한다면 에러 없이 무시된다.

6. **요소 일괄 삭제** → `Set.prototype.clear`

   ```jsx
   const set = new Set([1, 2, 3]);
   set.clear(); // Set(0) {}
   ```

   Set 객체의 모든 요소를 일괄 삭제할 때 사용하며 언제나 `undefined`를 반환한다.

7. **요소 순회** → `Set.prototype.forEach`

   ```jsx
   // forEach
   const set = new Set([1, 2, 3]);
   set.forEach((v, v2, set) => console.log(v, v2, set));
   // 1 1 Set(3) { 1, 2, 3 } | 2 2 Set(3) { 1, 2, 3 } | 3 3 Set(3) { 1, 2, 3 }

   // for...of
   console.log(Symbol.iterator in set); // true
   for (const value of set) {
     console.log(value);
   } // 1 2 3
   ```

   `forEach(현재_순회중인_요소값, 현재_순회중인_요소값, 현재_순회중인_Set객체자체)`

   Set 객체의 요소를 순회하려면 forEach 메서드를 사용하는데 총 3개의 인수를 전달받는다. 첫 번째 인수와 두 번째 인수는 같은 값이다.(`Array.prototype.forEach` 메서드와 인터페이스를 통일하기 위함)

   Set 객체는 이터러블이기 때문에 for…of문으로 순회할 수 있고 스프레드 문법과 배열 디스트럭처링의 대상이 될 수도 있다.

8. **집합 연산**

   Set 객체는 수학적 집합을 구현하기 위한 자료구조다.

   - **교집합: A∩B**

     ```jsx
     Set.prototype.intersection = function (set) {
       return new Set([...this].filter((v) => set.has(v)));
     };

     const setA = new Set([1, 2, 3, 4]);
     const setB = new Set([2, 4]);

     setA.intersection(setB); // Set(2) { 2, 4 }
     setB.intersection(setA); // Set(2) { 2, 4 }
     ```

     집합 A와 집합 B의 공통 요소로 구성된다.

   - **합집합: A∪B**

     ```jsx
     Set.prototype.union = function (set) {
       return new Set([...this, ...set]);
     };

     const setA = new Set([1, 2, 3, 4]);
     const setB = new Set([2, 4]);

     setA.union(setB); // Set(4) { 1, 2, 3, 4 }
     setB.union(setA); // Set(4) { 2, 4, 1, 3 }
     ```

     집합A와 집합B의 중복 없는 모든 요소로 구성된다.

   - **차집합: A-B**

     ```jsx
     Set.prototype.difference = function (set) {
       return new Set([...this].filter((v) => !set.has(v)));
     };

     const setA = new Set([1, 2, 3, 4]);
     const setB = new Set([2, 4]);

     setA.difference(setB); // Set(2) { 1, 3 }
     setB.difference(setA); // Set(0) {}
     ```

     집합A에는 존재하지만 집합B에는 존재하지 않는 요소로 구성된다.

   - **부분 집합과 상위 집합**

     ```jsx
     Set.prototype.isSuperset = function (subset) {
       const supersetArr = [...this];
       return [...subset].every((v) => supersetArr.includes(v));
     };

     const setA = new Set([1, 2, 3, 4]);
     const setB = new Set([2, 4]);

     setA.isSuperset(setB); // true
     setB.isSuperset(setA); // false
     ```

     집합A가 집합B에 포함되는 경우 집합A는 집합B의 부분집합이며 집합B는 집합A의 상위 집합이다.

### ✏️ Map

Map 객체는 키와 값의 쌍으로 이루어진 컬렉션이다. Map 객체는 객체와 유사하지만 다음과 같은 차이가 있다.

| 구분                   | 객체                    | Map 객체              |
| ---------------------- | ----------------------- | --------------------- |
| 키로 사용할 수 있는 값 | 문자열 or 심벌값        | 객체를 포함한 모든 값 |
| 이터러블               | X                       | O                     |
| 요소 개수 확인         | Object.keys(obj).length | map.size              |

1. **Map 객체의 생성**

   ```jsx
   const map = new Map(); // Map(0) {}
   const map1 = new Map([
     ['key1', 'value1'],
     ['key2', 'value2'],
   ]);
   // Map(2) { 'key1' => 'value1', 'key2' => 'value2' }
   ```

   Map 생성자 함수는 이터러블을 인수로 전달받아 Map 객체를 생성하는데 이때 인수로 전달되는 이터러블은 키와 쌍으로 이루어진 요소로 구성되어야 한다.

   Map 생성자 함수의 인수로 전달한 이터러블에 중복된 키를 갖는 요소가 존재하면 값이 덮어써지므로 Map 객체에는 중복된 키를 갖는 요소가 존재할 수 없다.

2. **요소 개수 확인** → `Map.prototype.size`

   ```jsx
   const { size } = new Map([
     ['key1', 'value1'],
     ['key2', 'value2'],
   ]); // 2
   const map = new Map([
     ['key1', 'value1'],
     ['key2', 'value2'],
   ]);
   console.log(Object.getOwnPropertyDescriptor(Map.prototype, 'size'));
   // { get: [Function: get size], set: undefined, enumerable: false, configurable: true }
   ```

   Map 객체의 요소 개수 확인

3. **요소추가** → `Map.prototype.set`

   ```jsx
   const map = new Map(); // Map(0) {}
   map.set('key1', 'value1').set('key2', 'value2');
   // Map(2) { 'key1' => 'value1', 'key2' => 'value2' }
   ```

   set 메서드는 Map 객체에 요소를 추가할 때 사용하며 새로운 요소가 추가된 Map 객체를 반환한다. 또 set 메서드를 연속적으로 호출할 수 있다.

   Map 객체에는 중복된 키를 갖는 요소가 존재할 수 없기 때문에 중복된 키를 갖는 요소를 추가하면 값이 덮어 써지며 이때 에러가 발생하지 않는다.

4. **요소 취득** → `Map.prototype.get`

   ```jsx
   const map = new Map();
   const gaori = { name: 'gaori' };
   map.set(gaori, 5);
   map.get(gaori); // 5
   map.get('5'); // undefined
   ```

   Map 객체에서 특정 요소를 취득하기 위해 인수로 키를 전달하면 인수로 전달받은 키를 갖는 값을 반환한다. 만약 키를 가진 요소가 존재하지 않다면 `undefined`를 반환한다.

5. **요소 존재 여부 확인** → `Map.prototype.has`

   ```jsx
   const gaori = { name: 'gaori' };
   const map = new Map([[gaori, 5]]);
   map.has(gaori); // true
   map.has('5'); // false
   ```

   Map 객체의 특정 요소의 존재 여부를 나타내는 불리언 값을 반환한다.

6. **요소 삭제** → `Map.prototype.delete`

   ```jsx
   const gaori = { name: 'gaori' };
   const coco = { name: 'coco' };

   const map = new Map([
     [gaori, 5],
     [coco, 1],
   ]);
   map.delete(coco); // Map(1) { { name: 'gaori' } => 5 }
   ```

   Map 객체의 특정 요소의 삭제 성공 여부를 나타내는 불리언 값을 반환하며 add 메서드와 달리 연속적으로 호출할 수 없다.

7. **요소 일괄 삭제**→ `Map.prototype.clear`

   ```jsx
   const gaori = { name: 'gaori' };
   const coco = { name: 'coco' };

   const map = new Map([
     [gaori, 5],
     [coco, 1],
   ]);
   map.clear(); // Map(0) {}
   ```

   Set 객체의 모든 요소를 일괄 삭제하며 `undefined`를 반환한다.

8. **요소 순회** → `Map.prototype.forEach`

   ```jsx
   // forEach
   const gaori = { name: 'gaori' };
   const coco = { name: 'coco' };
   const map = new Map([
     [gaori, 5],
     [coco, 1],
   ]);
   map.forEach((v, k, map) => console.log(v, k, map));
   // 5 { name: 'gaori' } Map(2) { { name: 'gaori' } => 5, { name: 'coco' } => 1 }
   // 1 { name: 'coco' } Map(2) { { name: 'gaori' } => 5, { name: 'coco' } => 1 }

   // for...of
   console.log(Symbol.iterator in map); // true
   for (const entry of map) {
     console.log(entry);
   }
   // [ { name: 'gaori' }, 5 ]
   // [ { name: 'coco' }, 1 ]
   ```

   Set 객체와 동일하며 다음과 같은 3가지의 차이점이 존재하는데 Map 객체는 이터러블이면서 이터레이터인 객체를 반환하는 메서드를 제공한다.

   - `Map.prototype.keys` : Map 객체에서 요소키를 값으로 갖는 이터러블이면서 이터레이터인 객체 반환
   - `Map.prototype.values` : Map 객체에서 요소값을 값으로 갖는 이터러블이면서 이터레이터인 객체 반환
   - `Map.prototype.entries` : Map 객체에서 요소키와 요소값을 갖는 이터러블이면서 이터레이터인 객체 반환
