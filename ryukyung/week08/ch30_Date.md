날짜와 시간(연, 월, 일, 시, 분, 초, 밀리초)을 위한 메서드를 제공하는 빌트인 객체이면서 생성자 함수이다.

- UTC(협정 세계시): 국제 표준시, GMT(그리니치 평균시)로 불리기도 한다.
- KST(한국 표준시): UTC에 9시간을 더한 시간, UTC보다 9시간 빠르다.

### ✏️ Date 생성자 함수

Date 생성자 함수로 생성한 Date 객체는 내부적으로 날짜와 시간을 나타내는 정수값을 갖는다. 이 값은 1970년 1월 1일 00:00:00(UTC)을 기점으로 Date 객체가 나타내는 날짜와 시간까지의 **밀리초**를 나타낸다.

1. **new Date()**

   인수 없이 new 연산자와 함께 호출하면 현재 날짜와 시간을 가지는 Date 객체를 반환한다.

   ```jsx
   new Date(); // Sat Apr 29 2023 02:10:09 GMT+0900 (한국 표준시) | object
   Date(); // 'Sat Apr 29 2023 02:10:36 GMT+0900 (한국 표준시)' | String
   ```

2. **new Date(milliseconds)**

   숫자 타입의 밀리초를 인수로 전달하면 1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 밀리초만큼 경과한 날짜와 시간을 나타내는 Date 객체를 반환한다.

3. **new Date(dateString)**

   문자열을 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체를 반환한다.

   인수로 전달한 문자열은 Date.parse()로 해석 가능한 형식이어야 한다.

   ex) `‘May 26 2020 10:00:00’`, `‘2020/03/26/10:00:00’`

4. **new Date(year, month[, day, hour, minute, second, millisecond])**

   연, 월, 일, 시, 분, 초, 밀리초를 의미하는 숫자를 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체를 반환한다.

   | year        | 연     | 1900년 이후의 정수(0~99는 1900~1999로 처리) |
   | ----------- | ------ | ------------------------------------------- |
   | month       | 월     | 0~11까지의 정수, 0=1월                      |
   | day         | 일     | 0~31까지의 정수                             |
   | hour        | 시     | 0~23까지의 정수                             |
   | minute      | 분     | 0~59까지의 정수                             |
   | second      | 초     | 0~59까지의 정수                             |
   | millisecond | 밀리초 | 0~999까지의 정수                            |

### ✏️ Date 메서드

1. **Date.now**

   1970년 1월 1일 00:00:00(UTC)을 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환한다.

2. **Date.parse**

   1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 **지정 시간**까지의 밀리초를 숫자로 반환한다.

   🪝**지정 시간**: new Date(dateString)의 인수와 동일한 형식

3. **Date.UTC**

   1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환하며 `new Date(year, month[, day, hour, minute, second, millisecond])`와 같은 형식의 인수를 사용한다.

4. **Date.prototype.getFullYear**

   Date 객체의 연도를 나타내는 정수를 반환한다.

   ```jsx
   console.log(new Date().getFullYear()); // 2023
   ```

5. **Date.prototype.setFullYear**

   Date 객체에 연도를 나타내는 정수를 설정하며 연도 이외에 옵션으로 월, 일도 설정할 수 있다.

   ```jsx
   const date = new Date();
   date.getFullYear(); // 2023

   date.setFullYear(2021);
   date.getFullYear(); // 2021
   ```

6. **Date.prototype.getMonth**

   Date 객체의 월을 나타내는 0~11의 정수를 반환한다.

7. **Date.prototype.setMonth**

   Date 객체에 월을 나타내는 0~11의 정수를 설정하며 월 이외에 옵션으로 일도 설정할 수 있다.

8. **Date.prototype.getDate**

   Date 객체의 날짜를 나타내는 1~31의 정수를 반환한다.

9. **Date.prototype.setDate**

   Date 객체에 날짜를 나타내는 정수를 설정할 수 있다.

10. **Date.prototype.getDay**

    Date 객체의 요일을 나타내는 0~6의 정수를 반환한다.

    ```jsx
    [일, 월, 화, 수, 목, 금, 토] = [0, 1, 2, 3, 4, 5, 6];
    ```

11. **Date.prototype.getHours**

    Date 객체의 시간을 나타내는 0~23의 정수를 반환한다.

12. **Date.prototype.setHours**

    Date 객체의 시간을 나타내는 정수를 설정하며 시간 이외에 옵션으로 분, 초, 밀리초도 설정할 수 있다.

13. **Date.prototype.getMinutes**

    Date 객체의 분을 나타내는 0~59의 정수를 반환한다.

14. **Date.prototype.setMinutes**

    Date 객체의 분을 나타내는 정수를 설정하며 시간 이외에 옵션으로 초, 밀리초도 설정할 수 있다.

15. **Date.prototype.getSeconds**

    Date 객체의 초를 나타내는 0~59의 정수를 반환한다.

16. **Date.prototype.setSeconds**

    Date 객체의 초를 나타내는 정수를 설정하며 시간 이외에 옵션으로 밀리초도 설정할 수 있다.

17. **Date.prototype.getMilliseconds**

    Date 객체의 밀리초를 나타내는 0~999의 정수를 반환한다.

18. **Date.prototype.setMilliseconds**

    Date 객체의 밀리초를 나타내는 정수를 설정한다.

19. **Date.prototype.getTime**

    1970년 1월 1일 00:00:00(UTC)을 기점으로 Date 객체의 시간까지 경과된 밀리초를 반환한다.

20. **Date.prototype.setTime**

    Date 객체에 1970년 1월 1일 00:00:00(UTC)를 기점으로 경과된 밀리초를 설정한다.

21. **Date.prototype.getTimezoneOffset**

    UTC와 Date 객체에 지정된 로캘locale) 시간과의 차이를 분단위로 반환한다.

    UTC = KST - 9h

22. **Date.prototype.toDateString**

    사람이 읽을 수 있는 형식의 문자열로 Date 객체의 날짜를 반환한다.

    ```jsx
    new Date().toDateString(); // 'Sat Apr 29 2023'
    ```

23. **Date.prototype.toTimeString**

    사람이 읽을 수 있는 형식의 문자열로 Date 객체의 시간을 반환한다.

    ```jsx
    new Date().toTimeString(); // '02:54:01 GMT+0900 (한국 표준시)'
    ```

24. **Date.prototype.toISOString**

    **ISO 8601 형식**으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환한다.

    🪝**ISO 8601 형식**: 기본 형식은 [hh][mm][ss]이며 확장 형식은 [hh]:[mm]:[ss]다.

25. **Date.prototype.toLocaleString**

    인수로 전달한 로캘을 기준으로 Date 객체의 시간을 표현한 문자열을 반환하며 인수를 생략한 경우 브라우저가 동작 중인 시스템의 로캘을 적용한다.

26. **Date.prototype.toLocaleTimeString**

    인수로 전달한 로캘을 기준으로 Date 객체의 시간을 표현한 문자열을 반환하며 인수를 생략한 경우 브라우저가 동작 중인 시스템의 로캘을 적용한다.
