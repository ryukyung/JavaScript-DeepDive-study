### ✏️ Ajax(: Asynchronous JavaScript And XML)

자바스크립트를 사용하여 비동기 방식으로 서버에 데이터를 요청하고 서버가 응답한 데이터를 수신하여 웹 페이지를 동적으로 갱신하는 프로그래밍 방식이다.

- **전통적인 웹페이지 방식**
  [불필요한 데이터 통신] 변경할 필요가 없는 부분까지 포함된 완전한 HTML을서버로부터 매번 다시 전송 받는다.
  [화면 깜빡임 현상] 변경할 필요가 없는 부분까지 처음부터 다시 렌더링
  [동기 방식] 서버의 응답이 있을때까지 다음 처리는 블로킹
- **Ajax의 장점**
  [불필요한 데이터 통신X] 변경할 부분만 갱신하여 필요한 데이터만 서버로부터 전송받는다.
  [화면 깜빡임 현상X] 변경할 필요가 없는 부분은 다시 렌더링하지 않는다.
  [비동기 방식] 서버에게 요청을 보낸 이후 블로킹 발생X

### ✏️ JSON(: JavaScript Object Notation)

클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이며 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로 대부분의 프로그래밍 언어에서 사용할 수 있다.

1. **JSON 표기 방식**

   ```json
   {
     "name": "gaori",
     "age": 5,
     "alive": true,
     "hobby": ["traveling", "playing"]
   }
   ```

2. **JSON.stringify**

   객체 및 배열을 JSON 포맷의 문자열로 변환한다.

   🪝 **직렬화(serializing)**

   클라이언트가 서버로 객체를 전송하가 위해 객체를 문자열화 하는 작업

   ```jsx
   const obj = {
     name: 'gaori',
     age: 5,
     alive: true,
     hobby: ['traveling', 'playing'],
   };

   console.log(JSON.stringify(obj));
   // {"name":"gaori","age":5,"alive":true,"hobby":["traveling","playing"]}
   console.log(JSON.stringify(obj, null, 2));
   /* {
     "name": "gaori",
     "age": 5,
     "alive": true,
     "hobby": [
       "traveling",
       "playing"
     ]
   } */
   ```

3. **JSON.parse**

   JSON 포맷의 문자열을 객체로 변환한다.

   🪝 **역직렬화(deserializing)**

   서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열이고 이 문자열을 객체로서 사용하려면 JSON 포맷의 문자열을 객체화 하는 작업

   ```jsx
   const todos = [
     { id: 1, todo: '학교가기', completed: true },
     { id: 2, todo: '집가기', completed: false },
     { id: 3, todo: '잠자기', completed: false },
   ];

   const json = JSON.stringify(todos);
   console.log(json);
   // [{"id":1,"todo":"학교가기","completed":true},{"id":2,"todo":"집가기","completed":false},{"id":3,"todo":"잠자기","completed":false}]
   const parsed = JSON.parse(json);
   console.log(parsed);
   /* [
     { id: 1, todo: '학교가기', completed: true },
     { id: 2, todo: '집가기', completed: false },
     { id: 3, todo: '잠자기', completed: false }
   ] */
   ```

### ✏️ XMLHttpRequest

자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용한다. Web API인 XMLHttpRequest 객체는 HTTP 요청 전송 및 응답 수신을 위한 다양한 메서드와 프로퍼티를 제공한다.

1. **XMLHttpRequest 객체 생성**

   XMLHttpRequest 객체는 XMLHttpRequest 생성자 함수를 호출하여 생성하고 이 객체는 브라우저에서 제공하는 Web API이기 때문에 브라우저 환경에서만 정상 작동 한다.

   ```jsx
   const xhr = new XMLHttpRequest();
   ```

2. **XMLHttpRequest 객체의 프로퍼티와 메서드**

   **XMLHttpRequest 객체의 프로토타입 프로퍼티**

   → `readState`, `status`, `statusText`, `responseType`, `response`, `responseText`

   **XMLHttpRequest 객체의 이벤트 핸들러 프로퍼티**

   → `onreadystatechange`, `onloadstart`, `onprogress`, `onabort`, `onerror`, `onload`, `ontimeout`, `onloadend`

   **XMLHttpRequest 객체의 메서드**

   → `open`, `send`, `abort`, `setRequestHeader`, `getResponseHeader`

   **XMLHttpRequest 객체의 정적 프로퍼티**

   → `UNSENT`, `OPENED`, `HEADERS_RECEIVED`, `LOADING`, `DONE`

3. **HTTP 요청 전송**

   1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청 초기화

      (필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값 설정)

      ```jsx
      xhr.open('GET', '/users');
      xhr.setRequestHeader('content-type', 'application/json');
      ```

   2. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청 전송

      ```jsx
      xhr.send();
      ```

   - `XMLHttpRequest.prototype.open(method, url[, async])`
     `method`: HTTP 요청 메서드(”GET”, “POST”, “PUT”, “DELETE” …)
     `url`: HTTP 요청을 전송할 URL
     `async`: 비동기 여부 요청, 기본값은 `true`(=비동기 방식)
   - `XMLHttpRequest.prototype.send`
     open 메서드로 초기화된 HTTP 요청을 서버에 전송한다.
     GET: 데이터를 URL의 일부부인 쿼리 문자열로 서버에 전송
     POST: 데이터를 요청 몸체에 담아 전송
   - `XMLHttpRequest.prototype.setRequestHeader`
     특정 HTTP 요청의 헤더 값을 설정하는데 반드시 open 메서드 호출 이후에 호출해야 한다.
