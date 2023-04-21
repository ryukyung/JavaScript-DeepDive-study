---
week: 8주차
---

# 모던 자바스크립트 Deep Dive CH30. Date

## 목차

  - [표준 빌트인 객체 Date](#표준-빌트인-객체-date)
  - [Date 생성자 함수](#date-생성자-함수)
  - [Date 메서드](#date-메서드)

## 표준 빌트인 객체 Date

Date는 날짜와 시간을 위한 메서드를 제공하는 빌트인 객체이면서 생성자 함수다. 현재 날짜와 시간은 자바스크립트 코드가 실행된 시스템의 시계에 의해 결정된다.

* UTC(혐정 세계시): 국제 표준시, GMT(그리니치 평균시)로 불리기도 한다.
* KST(한국 표준시): UTC에 9시간을 더한 시간, KST는 UTC보다 9시간이 빠르다.

## Date 생성자 함수

Date 생성자 함수로 생성한 Date 객체는 내부적으로 날짜와 시간을 나타내는 정수값을 갖는다. 이 값은 1970년 1월 1일 00:00:00(UTC)를 기점으로 Date 객체가 나타내는 날짜와 시간까지의 밀리초를 나타낸다.

현재 날짜와 시간이 아닌 다른 날짜와 시간을 다루고 싶은 경우 생성자 함수에 명시적으로 해당 날짜와 시간 정보를 인수로 지정한다.

> 1초는 1000ms

Date 생성자 함수로 객체를 생성하는 방법은 아래 4가지가 있다.

1. `new Date()`
   - 현재 날짜와 시간을 갖는 Date 객체 반환
   - 내부적으로는 정수값을 갖지만 콘솔에 출력시 기본적으로 날짜와 시간 정보 출력
      ```js
      new Date(); // 2023-04-21T16:45:51.662Z
      ```
   - `new` 연산자 없이 호출하면 날짜와 시간 정보를 나타내는 문자열 반환
      ```js
      Date(); // Fri Apr 21 2023 16:45:51 GMT+0000 (Coordinated Universal Time)
      ```
2. `new Date(milliseconds)`
   - 1970년 1월 1일 00:00:00(UTC)를 기점으로 인수로 전달된 밀리초만큼 경과한 날짜와 시간을 나타내는 Date 객체 반환
3. `new Date(dateString)`
   - 문자열을 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체 반환
   - 인수로 전달된 문자열은 Date.parse 메서드에 의해 해석 가능한 형식이어야 함
      > ex. 2021/02/13/12:00:00, May 23, 2021 12:00:00
4. `new Date(year, month[, day, hour, minute, second, millisecond])`
   - 숫자를 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체 반환
   - 연, 월은 반드시 지정해야 하며, 지정하지 않은 옵션 정보는 0 또는 1로 초기화 됨
      | 인수        | 내용                                                            |
      | ----------- | --------------------------------------------------------------- |
      | year        | 연을 나타내는 1900 이후의 정수, 0부터 99는 1900부터 1999로 처리 |
      | month       | 월을 나타내는 0 ~ 11까지의 정수(주의: 0 = 1월)                  |
      | day         | 일을 나타내는 1 ~ 31까지의 정수                                 |
      | hour        | 시를 낱내는 0 ~ 23까지의 정수                                   |
      | minute      | 분을 나타내는 0 ~ 59까지의 정수                                 |
      | second      | 초를 나타내는 0 ~ 59까지의 정수                                 |
      | millisecond | 밀리초를 나타내는 0 ~ 999까지의 정수                            |

## Date 메서드

* `Date.now`
  - 1970년 1월 1일 00:00:00(UTC)를 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환
* `Date.parse`
  - 1970년 1월 1일 00:00:00(UTC)를 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환
  - `new Date(dateString)`과 동일한 형식의 인수 사용
* `Date.UTC`
  - 1970년 1월 1일 00:00:00(UTC)를 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환
  - `new Date(year, month[, day, hour, minute, second, millisecond])`와 같은 형식의 인수 사용
  - 주의: KST가 아닌 UTC로 인식
* `Date.prototype.getFullYear`
  - Date 객체의 연도를 나타내는 정수 반환
* `Date.prototype.setFullYear`
  - Date 객체에 연도를 나타내는 정수 설정
  - 연도 이외에 월, 일도 설정 가능
* `Date.prototype.getMonth`
  - Date 객체의 월을 나타내는 0 ~ 11의 정수 반환
* `Date.prototype.setMonth`
  - Date 객체에 월을 나타내는 0 ~ 11의 정수 설정
  - 월 이외에 일도 설정 가능
* `Date.prototype.getDate`
  - Date 객체의 날짜를 나타내는 0 ~ 31의 정수를 반환
* `Date.prototype.setDate`
  - Date 객체에 날짜를 나타내는 0 ~ 31의 정수 설정
* `Date.prototype.getDay`
  - Date 객체의 요일을 나타내는 정수 반환
    |  요일  | 반환값 |
    | :----: | :----: |
    | 일요일 |   0    |
    | 월요일 |   1    |
    | 화요일 |   2    |
    | 수요일 |   3    |
    | 목요일 |   4    |
    | 금요일 |   5    |
    | 토요일 |   6    |
* `Date.prototype.getHours`
  - Date 객체의 시간을 나타내는 정수 반환
* `Date.prototype.setHours`
  - Date 객체에 시간을 나타내는 정수 설정
  - 시간 이외에 분, 초, 밀리초도 설정 가능
* `Date.prototype.getMinutes`
  - Date 객체의 분을 나타내는 정수 반환
* `Date.prototype.setMinutes`
  - Date 객체에 분을 나타내는 정수 설정
  - 분 이외에 초, 밀리초도 설정 가능
* `Date.prototype.getSeconds`
  - Date 객체의 초를 나타내는 정수 반환
* `Date.prototype.setSeconds`
  - Date 객체에 초를 나타내는 정수 설정
  - 초 이외에 밀리초도 설정 가능
* `Date.prototype.getMilliseconds`
  - Date 객체의 밀리초를 나타내는 정수 반환
* `Date.prototype.setMilliseconds`
  - Date 객체에 밀리초를 나타내는 정수 설정
* `Date.prototype.getTime`
  - 1970년 1월 1일 00:00:00(UTC)를 기점으로 Date 객체의 시간까지 경과된 밀리초 반환
* `Date.prototype.setTime`
  - Date 객체에 1970년 1월 1일 00:00:00(UTC)를 기점으로 경과된 밀리초 설정
* `Date.prototype.getTimezoneOffset`
  - UTC와 Date 객체에 지정된 locale 시간과의 차이를 분 단위로 반환
  - KST는 UTC에 9시간을 더한 시간이니 -9시간이다.
* `Date.prototype.toDateString`
  - 사람이 읽을 수 있는 형식의 문자열로 Date 객체의 날짜를 변환
* `Date.prototype.toTimeString`
  - 사람이 읽을 수 있는 형식으로 Date 객체의 시간을 표현한 문자열 반환
* `Date.prototype.toISOString`
  - ISO 8601 형식으로 Date 객체의 날짜와 시간을 표현한 문자열 반환
* `Date.prototype.toLocaleString`
  - 인수로 전달한 지역을 기준으로 Date 객체의 날짜와 시간을 표현한 문자열 반환
  - 인수 생략시 브라우저가 동작 중인 시스템의 지역 적용
* `Date.prototype.toLocaleTimeString`
  - 인수로 전달한 지역을 기준으로 Date 객체의 시간을 표현한 문자열 반환
  - 인수 생략시 브라우저가 동작 중인 시스템의 지역 적용
