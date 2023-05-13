🪝**DOM(: Document Object Model)** : HTML 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 API, 프로퍼티와 메서드를 제공하는 트리 자료구조

### ✏️ 노드

1. **HTML 요소와 노드 객체**

   HTML 요소는 HTML 문서를 구성하는 개별적인 요소를 의미하며 렌더링 엔진에 의해 파싱되어 DOM을 구성하는 요소 노드 객체로 변환된다.

   HTML 요소의 콘텐츠 영역에는 다른 HTML 요소도 포함할 수 있다. 이때 중첩 관계에 의해 계층적인 부자 관계가 형성되며 이러한 관계를 반영하여 HTML 요소를 객체화한 모든 노드 객체들을 트리 자료구조로 구성한다.

2. **노드 객체의 타입**
   - **문서노드(: document node)**
     DOM 트리의 최상위에 존재하는 루트 노드, `document` 객체를 말하며 `window.document`나 `document`로 참조할 수 있다.
   - **요소 노드(: element node)**
     HTML 요소를 가리키는 객체를 말하며 문서의 구조를 표현한다.
   - **어트리뷰트 노드(: attribute node)**
     HTML 요소의 어트리뷰트를 가리키는 객체를 말하며 어트리뷰트가 지정된 HTML 요소의 요소 노드와 연결되어 있다.
   - **텍스트 노드(: text node)**
     HTML 요소의 텍스트를 가리키는 객체, 요소 노드의 자식 노드를 말하며 문서의 정보를 표현한다.
3. **노드 객체의 상속 구조**

   | input 요소 노드 객체의 특성                                | 프로토타입을 제공하는 객체 |
   | ---------------------------------------------------------- | -------------------------- |
   | 객체                                                       | Object                     |
   | 이벤트를 발생시키는 객체                                   | EventTarget                |
   | 트리 자료구조의 노드 객체                                  | Node                       |
   | 브라우저가 렌더링할 수 있는 웹 문서의 요소를 표현하는 객체 | Element                    |
   | 웹 문서의 요소 중에서 HTML 요소를 표현하는 객체            | HTMLElement                |
   | HTML 요소 중에서 input 요소를 표현하는 객체                | HTMLInputElement           |

   DOM은 HTML 문서의 계층적 구조와 정보를 표현하며 노드 객체의 종류(노드 타입)에 따라 필요한 기능을 DOM API(프로퍼티와 메서드 집합)로 제공한다. 이 DOM API를 통해 HTML의 구조나 내용, 스타일 등을 동적으로 조작할 수 있다.

### ✏️ 요소 노드 취득

1. **id를 이용한 요소 노드 취득**

   `Document.prototype.getElementById`: 인수로 전달한 id 어트리뷰트 값을 갖는 첫 번째 요소 노드만 반환한다.

2. **태그 이름을 이용한 요소 노드 취득**

   `Document.protytpe.getElementsByTagName`: 루트 노드(문서 노드)를 통해 호출

   `Element.protytpe.getElementsByTagName`: 특정 요소 노드를 통해 호출(특정 요소 노드의 자식 중에서 탐색)

   `getElementsByTagName` 메서드가 반환하는 DOM 컬렉션 객체인 HTML Collection 객체는 유사 배열 객체이면서 이터러블이다.

3. **CSS 선택자를 이용한 요소 노드 취득**

   `Document.prototype.querySelector` | `Element.prototype.querySelector`

   : 인수로 전달한 CSS 선택자를 만족시키는 하나의 요소 노드를 탐색하여 반환한다.

   - 만족하는 요소가 여러 개 : 첫 번째 요소 노드만 반환
   - 만족하는 요소가 없는 경우 : null 반환
   - 문법에 맞지 않는 경우 : DOMException 에러 발생

   `Document.prototype.querySelectorAll` | `Element.prototype.qeurySelectorAll`

   : 인수로 전달한 CSS 선택자를 만족시키는 모든 요소 노드를 탐색하여 반환한다.

   - 만족하는 요소가 존재하지 않는 경우 : 빈 NodeList 객체 반환
   - 문법에 맞지 않는 경우 : DomException 에러 발생

4. **특정 요소 노드를 취득할 수 있는지 확인**

   `Element.prototype.matches`

   : 인수로 전달한 CSS 선택자를 통해 특정 노드를 취득할 수 있는지 확인하며 이벤트 위임을 사용할 때 유용하다.

5. **HTMLCollection과 NodeList**

   **HTMLCollection**

   객체의 상태 변화를 실시간으로 반영하기 때문에 살아있는 객체라고 부르기도 하며 상태가 즉시 반영되기 때문에 에러가 나타나기도 한다. 유사 배열 객체이면서 이터러블인 HTMLCollection 객체를 배열로 변환하면 부작용을 발생시키는 HTMLCollection 객체를 사용할 필요가 없고 유용한 배열의 고차함수(`forEach`, `filter`, `reduce`)를 사용할 수 있다.

   **NodeList**

   실시간으로 객체의 상태 변경을 반영하지 않는 객체를 말하며 `NodeList.prototype.forEach` 메서드를 상속 받아 사용할 수 있다. 예외적으로 `childNodes` 프로퍼티가 반환하는 NodeList 객체는 HTMLCollection과 같이 상태가 실시간으로 반영된다.

### ✏️ 노드 탐색

- `Node.prototype`
  `parentNode` : 부모 노드 탐색, 부모 노드는 텍스트 노드가 될 수 없다.
  `previousSibling` : 이전 형제 노드 탐색하여 반환, 요소 노드 or 텍스트 노드 반환
  `firstChild` : 첫 번재 자식 노드 반환, 요소 노드 or 텍스트 노드
  `childNodes` : 자식 노드 모두 탐색하여 NodeList로 반환, 텍스트 노드 포함
- `Element.prototye`
  `previousElemetSibling` : 이전 형제 노드 반환, 요소 노드만 반환
  `nextElementSibling` : 자신의 다음 형제 요소 노드 반환, 요소 노드만 반환
  `children` : 자식 노드 중 요소 노드만 모두 탐색하여 HTMLCollection으로 반환(텍스트 노트X)

### ✏️ 노드 정보 취득

- `Node.prototype.nodeType`
  노드 객체의 종류(노트 타입)를 나타내는 상수를 반환한다. (상수는 Node에 정의되어 있다.)
  `Node.ELEMENT_NODE` : 요소 노드 타입을 나타내는 상수 1 반환
  `Node.TEXT_NODE` : 텍스트 노드 타입을 나타내는 상수 3 반환
  `Node.DOCUMENT_NODE` : 문서 노드 타입을 나타내는 상수 9 반환
- `Node.prototype.nodeName`
  노드의 이름을 문자열로 반환한다.
  요소노드 : 대문자 문자열로 태그 이름 반환
  텍스트 노드 : “#text” 반환
  문서 노드 : “#document” 반환

### ✏️ 요소 노드의 텍스트 조작

1. **nodeValue**

   노드 객체의 값(텍스트 노드의 텍스트) 반환, 문서 노드 및 요소 노드는 `nodeValue` 참조 시 `null` 반환

2. **textContent**

   요소 노드의 텍스트와 자손 노드의 텍스트를 모두 취득하거나 변경한다. `textContent`는 요소 노드의 콘텐츠 영역의 모든 텍스트를 모두 반환하는데 이때 HTML 마크업은 무시된다.

### ✏️ DOM 조작

1. `Element.prototype.innerHTML`

   요소 노드의 콘텐츠 영역 내에 포함된 모든 HTML 마크업을 문자열로 반환한다.

   크로스 사이트 스크립트 공격에 취약하며 모든 자식 요소를 제거하고 할당하기 때문에 비효율적이며 새로운 요소를 삽입할 때 위치를 지정할 수 없다는 단점이 있다.

2. `Element.prototype.insertAdjacentHTML`

   기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를삽입한다. 첫 번째 인수로 전달할 수 있는 문자열은 ‘`beforebegin`’, ‘`afterbegin`’, ‘`beforeend`’, ‘`afterend`’ 총 4가지이다.

3. **노드 생성과 추가**
   - **요소 노드 생성**
     `Document.prototype.createElement(tagName)`
     : 요소 노드를 생성하여 반환, `tagName`에는 태그 이름을 나타내는 문자열을 전달한다.
     `createElement` : 요소 노드를 생성할 뿐 DOM에 추가X(DOM에 추가하는 별도의 처리 필요)
   - **텍스트 노드 생성**
     `Document.prototype.createTextNode(text)`: 텍스트 노드를 생성하여 반환한다.
     텍스트 노드를 생성할 뿐 요소 노드에 추가X(요소 노드에 추가하는 별도의 처리 필요)
   - **텍스트 노드를 요소 노드의 자식 노드로 추가**
     `Node.prototype.appendChild(childNode)`: 인수로 전달한 노드를 메서드를 호출한 노드의 마지막 자식 노드로 추가한다.

### ✏️ 어트리뷰트

1. **어트리뷰트 노드와 attributes 프로퍼티**

   HTML 문서가 파싱될 때 HTML 요소의 어트리뷰트는 어트리뷰트 노드로 변환되어 요소 노드와 연결된다.

   하나의 어트리뷰트 당 하나의 어트리뷰트 노드를 생성하는데 이때 모든 어트리뷰트 노드의 참조는 유사 배열 객체이자 이터러블인 `NamedNodeMap` 객체에 담겨 요소 노드의 `attributes` 프로퍼티에 저장된다.

2. **HTML 어트리뷰트 조작**

   `Element.prototype.getAttirbute(attributeName)` : 어트리뷰트 값 참조

   `Element.prototype.setAttirbute(attributeName, attributeValue)` : 어트리뷰트 값 변경

   `Element.prototype.hasAttirbute(attributeName)` : 어트리뷰트 존재 확인

   `Element.prototype.removeAttirbute(attributeName)` : 특정 어트리뷰트 삭제

3. **HTML 어트리뷰트 vs. DOM 프로퍼티**

   HTML 어트리뷰트 : HTML 요소의 초기 상태를 지정, HTML 어트리뷰트 값은 HTML 요소의 초기상태를 의미하며 변하지 않는다.

   DOM 프로퍼티 : 사용자의 입력에 의한 상태 변화에 반응하여 언제나 최신 상태를 유지한다.

4. **data 어트리뷰트와 dataset 프로퍼티**

   data 어트리뷰트와 dataset 프로퍼티를 사용하면 HTML 요소에 정의한 사용자 정의 어트리뷰트와 자바스크립트 간에 데이터를 교환할 수 있다.

   `data` 어트리뷰트는 `data-` 접두사 다음에 임의의 이름을 붙여서 사용한다.

### ✏️ 스타일

1. **인라인 스타일 조작**

   `HTMLElement.prototype.style`

   `getter`/`setter`가 모두 존재하는 접근자 프로퍼티, 요소 노드의 인라인 스타일을 취득 or 추가 or 변경

2. **클래스 조작**
   - `Element.prototype.className`
     class 어트리뷰트의 값을 문자열로 반환하며 클래스 값이 공백으로 구분되어 있을 경우 그대로 문자열로 반환한다.
   - `Element.prototype.classList`
     class 어트리뷰트의 정보를 담은 `DOMTokenList` 객체를 반환한다.
     `DOMTokenList` : class 어트리뷰트의 정보를 나타내는 컬렉션 객체(유사 배열 객체면서 이터러블)
     `add(…className)` : 인수로 전달된 1개 이상의 문자열을 class 어트리뷰트 값으로 추가
     `remove(…className)` : 인수로 전달된 1개 이상의 문자열과 일치하는 class 어트리뷰트에서 삭제
     `item(index)` : 인수로 전달한 index에 해당하는 클래스를 class 어트리뷰트에서 반환
     `contains(className)` : 인수로 전달한 문자열과 일치하는 클래스가 class 어트리뷰트에 포함되어 있는지 확인한다.
     `replace(oldClassName, newClassName)` : 첫 번째 인수 클래스를 두 번째 클래스 인수로 변경
     `toggle(className[, force])` : 인수로 전달한 문자열과 일치하는 클래스가 존재하면 제거, 존재하지 않으면 추가한다.
