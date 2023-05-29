---
week: 12주차
---

# 모던 자바스크립트 Deep Dive CH43. Ajax

## Ajax

**Ajax**<sup>Asynchronous JavaScript and XML</sup>란 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말한다. 브라우저에서 제공하는 Web API인 XMLHttpRequest 객체를 기반으로 동작하며, XMLHttpRequest는 HTTP 비동기 통신을 위한 메서드와 프로퍼티를 제공한다.

이전의 웹페이지는 완전한 HTML을 서버로부터 전송받아 웹페이지 전체를 처음부터 다시 렌더링하는 방식으로 동작했는데, 아래와 같은 단점이 있었다.

1. 이전 웹페이지와 차이가 없어서 변경할 필요가 없는 부분까지 포함된 완전한 HTML을 서버로부터 매번 다시 전송받기 때문에 불필요한 데이터 통신이 발생한다.
2. 변경할 필요가 없는 부분까지 처음부터 다시 렌더링한다. 이로 인해 화면 전환이 일어나면 화면이 순간적으로 깜박이는 현상이 발생한다.
3. 클라이언트와 서버와의 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있을 때까지 다음 처리는 블로킹된다.

반면 Ajax는 아래와 같은 장점이 있다.

1. 변경할 부분을 갱신하는 데 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신이 발생하지 않는다.
2. 변경할 필요가 없는 부분은 다시 렌더링하지 않는다. 따라서 화면이 순간적으로 깜박이는 현상이 발생하지 않는다.
3. 클라이언트와 서버와의 통신이 비동기 방식으로 동작하기 때문에 서버에게 요청을 보낸 이후 블로킹이 발생하지 않는다.

## JSON

**JSON**<sup>JavaScript Object Notation</sup>은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다. 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로, 대부분의 프로그래밍 언어에서 사용할 수 있다.

### 표기 방식

JSON은 자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 순수한 텍스트다.

* 키: 큰따옴표로 묶어야 함 (작은따옴표 불가)
* 값: 객체 리터럴과 같은 표기법 사용, 문자열은 반드시 큰따옴표로 묶어야 함 (작은따옴표 불가)

```js
{
  "name": "slime",
  "level": 89,
  "isAttackable": true,
  "spawnArea": ["Forest", "Desert"]
}
```

### JSON.stringify

* `JSON.stringify` 메서드
  - 객체(또는 배열)를 JSON 포맷의 문자열로 변환
    > ⭐ **직렬화**<sup>serializing</sup>
    > 
    > 클라이언트가 서버로 객체를 전송하기 위해 객체를 문자열화 하는 것

### JSON.parse

* `JSON.parse` 메서드
  - JSON 포맷의 문자열을 객체로 변환, 배열의 경우 배열 객체로 변환
    > ⭐ **역직렬화**<sup>deserializing</sup>
    > 
    > 서버로부터 클라이언트에게 전송된 문자열 형식의 JSON 데이터를 객체로서 사용하기 위해 문자열을 객체화하는 것

## XMLHttpRequest

자바스크립트를 사용해 HTTP 요청을 전송하려면 Web API인 XMLHttpRequest 객체를 사용한다. XMLHttpRequest 객체는 HTTP 요청 전송과 HTTP 응답 수신을 위한 다양한 메서드와 프로퍼티를 제공한다.

### XMLHttpRequest 객체 생성

XMLHttpRequest 생성자 함수를 호출하여 생성한다. XMLHttpRequest 객체는 브라우저에서 제공하는 Web API이므로 브라우저 환경에서만 정상적으로 실행된다.

```js
const xmlHttpRequest = new XMLHttpRequest();
```

### 프로퍼티와 메서드

* **프로토타입 프로퍼티**

  | 프로토타입 프로퍼티 | 설명                                                                                                                                                                                   |
  | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | readyState          | HTTP 요청의 현재 상태를 나타내는 정수, 다음과 같은 XMLHttpRequest의 정적 프로퍼티를 값으로 갖는다.<br>- UNSENT: 0<br>- OPENED: 1<br>- HEADERS_RECEIVED: 2<br>- LOADING: 3<br>- DONE: 4 |
  | status              | HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수                                                                                                                             |
  | statusText          | HTTP 요청에 대한 응답 메시지를 나타내는 문자열                                                                                                                                         |
  | responseType        | HTTP 응답 타입                                                                                                                                                                         |
  | response            | HTTP 요청에 대한 응답 몸체, responseType에 따라 타입이 다르다.                                                                                                                         |
  | responseText        | 서버가 전송한 HTTP 요청에 대한 응답 문자열                                                                                                                                             |

* **이벤트 핸들러 프로퍼티**

  | 이벤트 핸들러 프로퍼티 | 설명                                                         |
  | ---------------------- | ------------------------------------------------------------ |
  | onreadystatechange     | readyState 프로퍼티 값이 변경된 경우                         |
  | onloadstart            | HTTP 요청에 대한 응답을 받기 시작한 경우                     |
  | onprogress             | HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생            |
  | onabort                | abort 메서드에 의해 HTTP 요청이 중단된 경우                  |
  | onerror                | HTTP 요청에 에러가 발생한 경우                               |
  | onload                 | HTTP 요청이 성공적으로 완료한 경우                           |
  | ontimeout              | HTTP 요청 시간이 초과한 경우                                 |
  | onloadend              | HTTP 요청이 완료한 경우, HTTP 요청이 성공 또는 실패하면 발생 |

* **메서드**

  | 메서드            | 설명                                     |
  | ----------------- | ---------------------------------------- |
  | open              | HTTP 요청 초기화                         |
  | send              | HTTP 요청 전송                           |
  | abort             | 이미 전송된 HTTP 요청 중단               |
  | setRequestHeader  | 특정 HTTP 요청 헤더의 값을 설정          |
  | getResponseHeader | 특정 HTTP 요청 헤더의 값을 문자열로 반환 |

* **정적 프로퍼티**

  정적 프로퍼티 | 값 | 설명
  UNSENT | 0 | open 메서드 호출 이전
  OPENED | 1 | open 메서드 호출 이후
  HEADERS_RECEIVED | 2 | send 메서드 호출 이후
  LOADING | 3 | 서버 응답 중 (응답 데이터 미완성 상태)
  DONE | 4 | 서버 응답 완료

### HTTP 요청 전송

HTTP 요청을 전송하는 경우 아래와 같은 순서를 따른다.

1. XMLHttpRequest.prototype.open 메서드로 HTTP 요청을 초기화한다.
2. 필요에 따라 XMLHttpRequest.prototype.setRequestHEader 메서드로 특정 HTTP 요청의 헤더 값을 설정한다.
3. XMLHttpRequest.prototype.send 메서드로 HTTP 요청을 전송한다.

  ```js
  // XMLHttpRequest 객체 생성
  const xhr = new XMLHttpRequest();

  // HTTP 요청 초기화
  xhr.open('GET', '/users');

  // HTTP 요청 헤더 설정
  xhr.setRequestHeader('content-type', 'application/json');

  // HTTP 요청 전송
  xhr.send();
  ```

* **`XMLHttpRequest.prototype.open`**
  - 서버에 전송할 HTTP 요청을 초기화
  - `xhr.open(method, url[, async])`
    + `method`: HTTP 요청 메서드("GET", "POST", "PUT", "DELETE", ...)
    + `url`: HTTP 요청을 전송할 URL
    + `async`: 비동기 요청 여부, 옵션으로 기본값은 true이며 비동기 방식으로 동작
      > ⭐ **HTTP 요청 메서드**
      >
      > 클라이언트가 서버에게 요청의 종류와 목적을 알리는 방법, 주로 아래 5가지 요청 메서드를 사용해 CRUD를 구현한다.
      >
      > HTTP 요청 메서드 | 종류 | 목적 | 페이로드
      > --- | --- | --- | ---
      > GET | index/retrieve | 모든/특정 리소스 취득 | X
      > POST | create | 리소스 생성 | O
      > PUT | replace | 리소스의 전체 교체 | O
      > PATCH | modify | 리소스의 일부 수정 | O
      > DELETE | delete | 모든/특정 리소스 삭제 | X


* **`XMLHttpRequest.prototype.send`**
  - 초기화된 HTTP 요청을 서버에 전송
  - GET 요청: 데이터를 URL의 일부분인 쿼리 문자열로 서버에 전송
    + send 메서드에 페이로드로 전달한 인수는 무시되고 요청 몸체는 null로 설정 됨
  - POST 요청: 데이터(페이로드)를 요청 몸체에 담아 전송
    + 페이로드가 객체인 경우 반드시 `JSON.stringify` 메서드를 사용해 직렬화 후 전달해야 함

* **`XMLHttpRequest.prototype.setRequestHeader`**
  - 특정 HTTP 요청의 헤더 값을 설정, 반드시 open 메서드 호출 이후에 호출
  - 자주 사용하는 HTTP 요청 헤더
    + `Content-type`: 요청 몸체에 담아 전송할 데이터의 MIME 타입의 정보
    + `Accept`: HTTP 클라이언트가 서버에 요청할 때 서버가 응답할 데이터의 MIME 타입

      | MIME 타입   | 서브 타입                                          |
      | ----------- | -------------------------------------------------- |
      | text        | text/plain, text/html, text/css, text/javascript   |
      | application | application/json, application/x-www-form-urlencode |
      | multipart   | multipart/formed-data                              |

### HTTP 응답 처리

서버가 전송한 응답을 처리하려면 XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해야 한다.

send 메서드를 통해 HTTP 요청을 서버에 전송하면 서버는 응답을 반환한다. 그러나 언제 응답이 클라이언트에 도달할지는 알 수 없다. 따라서 readystatechange 이벤트를 캐치한다.

* `readystatechange` 이벤트
  - HTTP 요청의 현재 상태 확인
  - HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티가 변경될 때마다 발생
  - readyState가 XMLHttpRequest.DONE인지 확인하여 서버의 응답이 완료되었는지 확인

서버의 응답이 완료되면 HTTP 요청에 대한 HTTP 상태 코드가 200인지 확인하여 정상 처리와 에러 처리를 구분한다. 200이라면 response 프로퍼티에서 서버가 전송한 데이터를 취득하고, 아니라면 필요한 에러 처리를 한다.

readystatechange 이벤트 대신 HTTP 요청이 성공적으로 완료된 경우 발생하는 load 이벤트를 캐치해도 된다. 이 경우, readyState가 XMLHttpRequest.DONE인지 확인할 필요가 없다.







