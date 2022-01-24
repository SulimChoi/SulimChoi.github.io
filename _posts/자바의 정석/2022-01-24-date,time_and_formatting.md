# 1. 날짜와 시간

### 1.1 Calendar와 Date

- Date는 날짜와 시간을 다룰 목적으로 JDK1.0부터 제공된 클래스
- Date 클래스의 기능이 부족해 Calendar라는 새로운 클래스를 JDK1.1부터 제공
- Calendar 클래스의 단점을 보완하기 위해 JDK1.8부터 'java.time 패키지'로 단점들을 개선한 새로운 클래스들이 추가됨



**Calendar와 GregorianCalendar**

- Calendar는 추상클래스이기 때문에 직접 객체를 생성할 수 없고, 메서드를 통해 완전히 구현된 클래스의 인스턴스를 얻어야 함

```java
Calendar cal = new Calendar();	// 에러! 추상클래스는 인스턴스 생성 불가
Calendar cal = Calendar.getInstance();	// getInstance()는 Calendar 클래스를 구현한 클래스의 인스턴스를 반환
```

- `getInstance()` 메서드
  - 시스템의 국가와 지역설정을 확인해 태국인 경우 BuddhistCalendar의 인스턴스를 반환하고, 그 외에는 GregorianCalendar의 인스턴스를 반환
    - GregorianCalendar는 Calendar를 상속받아 전세계 공통으로 사용하고 있는 그레고리력에 맞게 구현한 것
  - static 메서드인 이유: 메서드 내의 코드에서 인스턴스 변수를 사용하거나 인스턴스 메서드를 호출하지 않기 때문 + static이 아닐 경우 객체를 생성한 다음에 호출해야하는데 Calendar는 추상클래스이기 때문에 객체를 생성할 수 없기 때문
- 메서드를 통해 인스턴스를 반환받게하는 이유: 최소한의 변경으로 프로그램이 동작할 수 있도록 하기 위한 것



**Date와 Calendar간의 변환**

- Calendar가 새로 추가되면서 Date는 대부분의 메서드가 'deprecated'되어 잘 사용되지 않음
- Date를 필요호하는 메서드들이 있는 경우 Calendar를 Date로 변환하거나 그 반대로 변환할 일이 생김

> 1. Calendar를 Date로 변환
>
>    ```java
>    Calendar cal = Calendar.getInstance();
>    	...
>    Date d = new Date(cal.getTimeInMillis());	// Date(long date)
>    ```
>
> 2. Date를 Calendar로 변환
>
>    ```java
>    Date d = new Date();
>    	...
>    Calendar cal = Calendar.getInstance();
>    cal.setTime(d);
>    ```



# 2. 형식화 클래스

- java.text 패키지에 포함되어 있음
- 숫자, 날짜, 텍스트 데이터를 일정한 형식에 맞게 표현할 수 있는 방법을 객체지향적으로 설계하여 표준화함
- 형식화 클래스는 형식화에 사용될 패턴을 정의
  - 데이터를 정의된 패턴에 맞춰 형식화할 수 있고, 형식화된 데이터에서 원래의 데이터를 얻어낼 수도 있음
  - 형식화된 데이터의 패턴만 정의해주면 복잡한 문자열에서 substring()을 사용하지 않고 원하는 값을 얻을 수 있음



### 2.1 DecimalFormat

- 숫자를 형식화하는데 사용
- 숫자 데이터를 정수, 부동소수점, 금액 등의 다양한 형식으로 표현 가능하며, 일정한 형식의 텍스트 데이터를 숫자로 쉽게 변환하는 것도 가능

|  기호  |           의미            |
| :----: | :-----------------------: |
|   0    | 10진수( 값이 없을 때는 0) |
|   #    |          10진수           |
|   .    |          소수점           |
|   -    |         음수부호          |
|   ,    |        단위 구분자        |
|   E    |         지수기호          |
|   ;    |        패턴구분자         |
|   %    |          퍼센트           |
| \u2030 |     퍼밀(퍼센트 X 10)     |
| \u00A4 |           통화            |
|   '    |        escape문자         |

- 원하는 출력형식의 패턴을 작성해 DecimalFormat 인스턴스를 생성한 다음, 출력하고자 하는 문자열로 format메서드를 호출하면 원하는 패턴에 맞게 변환된 문자열을 얻게 됨

  ```java
  double number = 1234567.89;
  DecimalFormat df = new DecimalFormat("#.#E0");
  String result = df.format(number)
  ```



### 2.2 SimpleDateFormat

- DateFormat은 추상클래스로 SimpleDateFormat의 조상
- DateFormat는 추상클래스이므로 인스턴스를 생성하기 위해서는 getDateInstance()와 같은 static 메서드를 이용해야 함
- getDateInstance()에 의해 반환되는 것은 DateFormat을 상속받아 완전하게 구현한 SimpleDateFormat 인스턴스

| 기호 |                   의미                   |
| :--: | :--------------------------------------: |
|  G   |              연대 (BC, AD)               |
|  y   |                   년도                   |
|  M   |         월 (1~12 또는 1월~12월)          |
|  w   |          년의 몇 번째 주 (1~53)          |
|  W   |          월의 몇 번째 주 (1~5)           |
|  D   |         년의 몇 번째 일 (1~366)          |
|  d   |          월의 몇 번째 일 (1~31)          |
|  F   |         월의 몇 번째 요일 (1~5)          |
|  E   |                   요일                   |
|  a   |            오전/오후 (AM, PM)            |
|  H   |               시간 (0~23)                |
|  k   |               시간 (1~24)                |
|  K   |               시간 (0~11)                |
|  h   |               시간 (1~12)                |
|  m   |                분 (0~59)                 |
|  s   |                초 (0~59)                 |
|  S   |           천분의 일초 (0~999)            |
|  z   |      Time zone (General time zone)       |
|  Z   |      Time zone (RFC 822 time zone)       |
|  '   | escape 문자 (특수문자를 표현하는데 사용) |

- 원하는 출력형식의 패턴을 작성하고 SimpleDateFormat 인스턴스를 생성한 다음, 출력하고자 하는 Date인스턴스를 가지고 format(Date d)를 호출하면 지정한 출력형식에 맞게 변환된 문자열을 얻게 됨

```java
Date today = new Date();
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");

// 오늘 날짜를 yyyy-MM-dd 형태로 변환하여 반환
String resu;t = df.format(today);
```

- Calendar 인스턴스는 format 메서드 사용 불가. Calendar 인스턴스를 Date 인스턴스로 변환한 뒤 사용해야 됨



### 2.4 MessageFormat

- 데이터를 정해진 양식에 맞게 출력할 수 있도록 도와줌
- 데이터가 들어갈 자리를 마련해 놓은 양식을 미리 작성하고 프로그램을 이용해서 다수의 데이터를 같은 양식으로 출력할 때 사용하면 좋음
  - 고객들에게 보낼 안내문을 출력할 때 같은 안내문 양식에 받는 사람의 이름과 같은 데이터만 달라지도록 출력할 때
  - 하나의 데이터를 다양한 양식으로 출력할 때

- parse를 이요하면 지정된 양식에서 필요한 데이터만 추출 가능



# 3. java.time 패키짖

|       패키지       |                             설명                             |
| :----------------: | :----------------------------------------------------------: |
|     java.time      |      날짜와 시간을 다루는데 필요한 핵심 클래스들을 제공      |
|  java.time.chrono  |     표준(ISO)이 아닌 달력 시스템을 위한 클래스들을 제공      |
|  java.time.format  |   날짜와 시간을 파싱하고, 형식화하기 위한 클래스들을 제공    |
| java.time.temporal | 날짜와 시간의 필드(field)와 단위(unit)를 위한 클래스들을 제공 |
|   java.time.zone   |          시간대(time-zone)와 관련된 클래스들을 제공          |

- 위의 패키지들에 속한 클래스들의 가장 큰 특징은 '불변(immutable)'
  - 날짜나 시간을 변경하는 메서드들은 기존의 객체를 변경하는 대신 항상 변경된 새로운 객체를 반환
- 멀티 쓰레드 환경에서는 동시에 여러 쓰레드가 같은 객체에 접근할 수 있기 때문에, 변경 가능한 객체는 데이터가 잘못될 가능성이 있음 => 쓰레드에 안전(thread-safe)하지 않다



### 3.1 java.time 패키지의 핵심 클래스

- 날짜와 시간을 별도의 클래스로 분리해 놓음
  - 시간을 표현할 때는 LocalTime 클래스를, 날짜를 표현할 때는 LocalDate 클래스를 사용
  - 날짜와 시간이 모두 필요할 때는 LocalDateTime 클래스 사용
  - 날짜와 시간, 시간대(time-zone)을 다뤄야한다면 ZonedDateTime 클래스 사용



> **Period와 Duration**
>
> - Period = 날짜 - 날짜
> - Duration = 시간 - 시간



> **객체 생성하기 - now(), of()**
>
> - `now()`: 현재 날짜와 시간을 저장하는 객체를 생성
> - `of()`: 해당 필드의 값을 순서대로 지정해주면 됨. 각 클래스마다 댜앙한 종류의 `of()`가 정의되어 있음



> **Temporal, TemporalAccessor, TemporalAdjuster를 구현한 클래스**
>
> - LocalDate, LocalTime, LocalDateTime, ZonedDateTime, Instant 등
>
> **TemporalAmount를 구현한 클래스**
>
> - Period, Duration



### 3.2 LocalDate와 LocalTime

- 객체를 생성하는 방법은 현재의 날짜와 시간을 LocalDate와 LocalTime으로 각각 반환하는 `now()`와 지정된 날짜와 시간으로 LocalDate와 LocalTime 객체를 생성하는 `of()`가 있으며, 둘 다 static 메서드



> - 특정 필드의 값 가져오기 - get(), getXXX()
> - 필드의 값 변경하기 - with(), plus(), minus()
> - 날짜와 시간 비교 - isAfter(), isBefore(), isEqual()



### 3.3 Instant

- 에포크 타임(EPOCH TIME, 1970-01-01 00:00:00 UTC)부터 경과된 시간을 나노초 단위로 표현
  - 단일 진법으로만 다루기 때문에 계산하기 쉬움
- Instant를 생성할 때는 `now()` 와 `ofEpochSecond()`를 사용
- Instant는 시간을 초 단위와 나노초 단위로 나누어 저장



### 3.6 Period와 Duration

**`between()`**

- 두 날짜의 차이를 나타내는 Period를 얻고자 할 때 사용
- date1이 date2보다 날짜 상으로 이전이면 양수, 이후면 음수로 Period에 저장

- 시간 차이를 구할 때는 Duration을 사용

**`get()`**

- 특정 필드의 값을 얻을 때
- Duration에는 `getHours()`, `getMinites()` 같은 메서드가 없음
  - `Chrono.SECONDS`, `Chrono.NANOS`를 사용해야 함
- Duration을 LocalTime으로 변환한다음, LocalTime이 가지고 있는 get메서드들을 사용하면 더 간단함



> **`between()`과 `until()`**
>
> - 같은 일을 하지만, `between()`은 static 메서드이고 `until()`은 인스턴스 메서드



### 3.7 파싱과 포맷

- 형식화와 관련된 클래스들은 java.time.format 패키지에 들어있는데 이 중 **DateTimeFormatter**가 핵심

-  날짜와 시간의 형식화에는 `format()`이 사용되는데, 이 메서드는 DateTimeFormatter뿐만 아니라 LocalDate나 LocalTime 같은 클래스에도 존재
- `ofPattern()` 으로 원하는 출력형식 직접 작성 가능



> **문자열을 날짜와 시간으로 파싱하기**
>
> - 문자열을 날짜 또는 시간으로 변환하려면 static메서드 **`parse()`**를 사용
>
> - `parse()` 오버로딩 메서드
>
>   ```java
>   static LocalDateTime parse(CharSequence text)
>   static LocalDateTime parse(CharSequence text, DateTimeFormatter formatter)
>   ```

