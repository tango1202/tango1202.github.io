---
layout: single
title: "#18. [모던 C++ STL] (C++11~) 정밀한 시간 측정(system_clock, time_point, duration)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock), [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point), [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) 이 추가되어 좀더 다양한 정확도로 시간을 추적할 수 있습니다.
> * (C++17~) [floor(), ceil(), round(), abs()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c17-floor-ceil-round-abs?)가 추가되어 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)와 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)에 반올림 관련 처리를 할 수 있습니다.
> * (C++20~) [시/분/초 서식](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-%EC%8B%9C%EB%B6%84%EC%B4%88-%EC%84%9C%EC%8B%9D)이 추가되어 12시간/24시간의 시-분-초 서식 처리가 간편해 졌습니다.
> * (C++20~) [Calendar](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-calendar)가 추가되어 년-월-일 서식 처리가 간편해 졌습니다.
> * (C++20~) [Time Zone](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-time-zone)이 추가되어 특정 지역의 시간대로 시간을 구할 수 있습니다.
> * (C++20~) [utc_clock, tai_clock, gps_clock, file_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock)이 추가되었습니다.
> * (C++20~) [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)에 [from_stream()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)이 추가되었습니다.
> * (C++20~) [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)에 [from_stream()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)이 추가되었습니다.

# 개요

기존에는 날짜/시간 처리를 위해 C스타일의 [time, difftime](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)만 제공되었으나([C스타일 시간 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0) 참고), 

C++11 부터 STL 에서는 좀더 다양한 정확도로 시간을 추적할 수 있는 [chrono 라이브러리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/)가 추가되었습니다.

`<chrono>` 헤더 파일을 포함해야 하며, `std::chrono` [네임스페이스](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-namespace/)를 사용합니다.

다음 예는 주어진 함수의 실행 시간을 측정하는 예입니다.

1. `Measure()`함수는 전달된 함수를 실행하고 실행 시간을 측정합니다. 실행시킬 함수에 [인자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-function/#%EC%9D%B8%EC%9E%90%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98-parameter)를 전달하기 위해 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)을 사용합니다.
2. `system_clock::now()`를 이용하여 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)를 구합니다.
3. 구해진 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)를 `microsecond` 타입으로 저장합니다.
4. 두 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)간의 차를 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)으로 변환합니다. 

```cpp
template<typename Func, typename... Params>
std::chrono::microseconds Measure(Func func, Params&&... params) {
    // func 실행전 time_point 측정
    std::chrono::system_clock::time_point start{std::chrono::system_clock::now()};    

    // func 실행
    func(std::forward<Params>(params)...);

    // func 실행 후 time_point 측정
    std::chrono::system_clock::time_point end{std::chrono::system_clock::now()};

    // 두 time_point 간의 차
    std::chrono::microseconds val{std::chrono::duration_cast<std::chrono::microseconds>(end - start)};

    return val;
}
void MyFunc() {}
std::chrono::microseconds duration{Measure(MyFunc)};

std::cout << "MyFunc() : " << duration.count() << std::endl;
```

# Clock

|항목|내용|
|--|--|
|[system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock) (C++11~)|시스템 시계입니다.|
|`steady_clock` (C++11~)|조정되지 않는 시계입니다.|
|`high_resolution_clock` (C++11~)|사용 가능한 가장 짧은 틱 주기를 갖는 시계입니다.|
|`is_clock` (C++20~)<br/>`is_clock_v` (C++20~)|[Clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock)인지 검사합니다.|
|`utc_clock` (C++20~)|1970년 1월 1일 00:00:00 UTC부터 흐른 시간을 측정하는 시계입니다.|
|`tai_clock` (C++20~)|International Atomic Time을 위한 시계로서, 1958년 1월 1일 00:00:00부터 흐른 시간을 측정하는 시계입니다.|
|`gps_clock` (C++20~)|Global Positioning System을 위한 시계로서, 1980년 1월 6일 00:00:00 UTC부터 흐른 시간을 측정하는 시계입니다.|
|`file_clock` (C++20~)|파일 시간을 위한 시계입니다.|
|`local_t` (C++20~)|지역 시간을 표현하기 위한 시계입니다.|

[system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock)은 다음의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 있습니다.

|항목|내용|
|--|--|
|[now()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock) (C++11~)|현재 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)를 UTC 시간대로 리턴합니다.|
|`to_time_t()` (C++11~)|`time_t`타입으로 변환합니다.|
|`from_time_t()` (C++11~)|`time_t`타입에서 가져옵니다.|

# Time Point

시작 시간 이후 경과된 값입니다.

|항목|내용|
|--|--|
|[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point) (C++11~)|특정 시점을 나타냅니다.|
|`clock_time_conversion` (C++11~)|(작성중)|
|`clock_cast` (C++11~)|(작성중)|

[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)는 다음의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 있습니다.

|항목|내용|
|--|--|
|`time_since_epoch()` (C++11~)|(작성중)|
|`min()` (C++11~)|(작성중)|
|`max()` (C++11~)|(작성중)|
|`time_point_cast()`|(작성중)|
|`+, -` (C++11~)|(작성중)|
|`++, --` (C++11~)<br/>|(작성중)|
|`+=, -=` (C++11~)|(작성중)|
|`+=, -=, *=, /=, %=` (C++11~)|(작성중)|
|`+, -, *, /, %` (C++11~)|(작성중)|
|`==` (C++11~)<br/>`!=` (C++11~C++20)|(작성중)|
|`<, <=, >, >=` (C++11~)<br/>`<=>` (C++20~)|(작성중)|

# Duration

|항목|내용|
|--|--|
|[duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) (C++11~)|기간입니다. 두 Time Point의 차입니다.|

[duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)은 다음의 [멤버 함수](https://tango1202.github.io/legacy-cpp-oop/legacy-cpp-oop-member-function/#%EB%A9%A4%EB%B2%84-%ED%95%A8%EC%88%98)가 있습니다.

|항목|내용|
|--|--|
|`=` (C++11~)|(작성중)|
|`count()` (C++11~)|(작성중)|
|`zero()` (C++11~)|(작성중)|
|`min()` (C++11~)|(작성중)|
|`max()` (C++11~)|(작성중)|
|`duration_cast()` (C++11~)|(작성중)|
|[from_stream()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) (C++20~)|스트림으로부터 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)을 구합니다.|
|`+, -` (C++11~)|(작성중)|
|`++, --` (C++11~)<br/>|(작성중)|
|`+=, -=, *=, /=, %=` (C++11~)|(작성중)|
|`+, -, *, /, %` (C++11~)|(작성중)|
|`==` (C++11~)<br/>`!=` (C++11~C++20)|(작성중)|
|`<, <=, >, >=` (C++11~)<br/>`<=>` (C++20~)|(작성중)|
|`<<` (C++20~)|(작성중)|

다음과 같이 타입이 재정의 되어 있습니다.(int뒤 XX는 최소 필요 비트수입니다.)

|항목|내용|
|--|--|
|[nanoseconds](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) (C++11~)|`duration<int64, std::nano>`|
|`microseconds` (C++11~)|`duration<int54, std::micro>`|
|`milliseconds` (C++11~)|`duration<int44, std::milli>`|
|`seconds` (C++11~)|`duration<int35>`|
|`minutes` (C++11~)|`duration<int29, std::ratio<60>>`|
|`hours` (C++11~)|`duration<int23, std::ratio<3600>>`|
|[days](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) (C++20~)|`duration<int25, std::ratio<86400>>`|
|`weeks` (C++20~)|`duration<int22, std::ratio<604800>>`|
|`month` (C++20~)|`duration<int20, std::ratio<2629746>>`|
|`years` (C++20~)|`duration<int17, std::ratio<31556952>>`|

다음의 유틸리티가 제공됩니다.

|항목|내용|
|--|--|
|`treat_as_floating_point` (C++11~)|(작성중)|
|`treat_as_floating_point` (C++11~)|(작성중)|
|`duration_values` (C++11~)|(작성중)|
  
# (C++17~) floor(), ceil(), round(), abs()

|항목|내용|
|--|--|
|`floor()` (C++17~)|[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)와 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)을 내림합니다.|
|`ceil()` (C++17~)|[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)와 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)올림합니다.|
|`round()` (C++17~)|[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)와 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)반올림합니다.|
|`abs()` (C++17~)|[duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)의 절대값을 구합니다.|

# (C++20~) 시/분/초 서식

|항목|내용|
|--|--|
|`hh_mm_ss` (C++20~)|자정부터 지난 시간의 시/분/초를 구합니다.|
|`is_am()` (C++20~)<br/>`is_pm()` (C++20~)<br/>`make12()` (C++20~)<br/>`make24()` (C++20~)|12시간/24시간 서식을 적용합니다.|

다음은 [시/분/초 서식](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-%EC%8B%9C%EB%B6%84%EC%B4%88-%EC%84%9C%EC%8B%9D)의 사용예입니다. [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)을 사용하기 위해 `using namespace std::chrono`를 사용했습니다.

```cpp
using namespace std::chrono; // ""h, ""min, ""s사용을 위해 using namespace를 사용합니다. 
constexpr std::chrono::hh_mm_ss time{12h + 72min + 3s};

static_assert(time.hours() == 13h); // 12h에서 72min을 더해서 13h 12min이 되었습니다.
static_assert(time.minutes() == 12min);
static_assert(time.seconds() == 3s);

static_assert(std::chrono::is_am(13h) == false);
static_assert(std::chrono::is_pm(13h) == true);
static_assert(std::chrono::make12(13h) == 1h);
static_assert(std::chrono::make24(1h, true) == 13h); // pm 1h를 24시간 서식으로 변경하여 13h가 되었습니다. 
```

# (C++20~) Calendar

`auto date{year(2023)/month(12)/day(25)};`와 같이 특정 년-월-일을 표현합니다.

|항목|내용|
|--|--|
|`last_spec` (C++20~)|한달의 말일 또는 마지막 요일임을 나타냅니다. `last`가 미리 정의되어 있습니다.|
|[day](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-calendar) (C++20~)|일을 나타냅니다.|
|`month` (C++20~)|월을 나타냅니다. `January`, `February`, `March`, `April`, `May`, `June`, `July`, `August`, `September`, `October`, `November`, `December`가 미리 정의되어 있습니다.|
|[year](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-calendar) (C++20~)|년을 나타냅니다.|
|`weekday` (C++20~)|요일을 나타냅니다. `Sunday`, `Monday`, `Tuesday`, `Wednesday`, `Thursday`, `Friday`, `Satureday`가 미리 정의되어 있습니다.|
|`weekday_indexed` (C++20~)|특정월의 N번째 요일을 나타냅니다.|
|`weekday_last` (C++20~)|특정월의 마지막 요일을 나타냅니다.|
|`month_day` (C++20~)|월-일을 나타냅니다. 월일, 일월의 형태로 초기화할 수 있습니다.|
|`month_day_last` (C++20~)|월-마지막날을 나타냅니다.|
|`month_weekday` (C++20~)|월-요일을 나타냅니다.|
|`month_weekday_last` (C++20~)|월-마지막 요일를 나타냅니다.|
|`year_month` (C++20~)|년-월을 나타냅니다.|
|`year_month_day` (C++20~)|년-월-일을 나타냅니다. 년월일, 일월년, 월일년의 형태로 초기화할 수 있습니다.|
|`year_month_day_last` (C++20~)|년-월-마지막날을 나타냅니다.|
|`year_month_weekday` (C++20~)|년-월-요일을 나타냅니다.|
|`year_month_weekday_last` (C++20~)|년-월-마지막 요일을 나타냅니다.|
|`operator /` (C++20~)|년/월/일등의 형태의 표시를 지원합니다.|


요일과 월은 다음과 같이 미리 정의되어 있습니다.

```cpp
static_assert(std::chrono::Sunday == std::chrono::weekday(0)); // 각 요일은 미리 정의되었습니다.
static_assert(std::chrono::December == std::chrono::month(12)); // 각월은 미리 정의되었습니다.
```

다음과 같이`year_month_day`로 특정한 날을 표현할 수 있습니다.

```cpp
// 년-월-일
std::chrono::year_month_day yearMonthDay1{std::chrono::year{2023}/std::chrono::month{12}/std::chrono::day{25}};
std::chrono::year_month_day yearMonthDay2{std::chrono::day{25}/std::chrono::month{12}/std::chrono::year{2023}};
std::chrono::year_month_day yearMonthDay3{std::chrono::month{12}/std::chrono::day{25}/std::chrono::year{2023}};
```

`last_spec`은 마지막 날이나 마지막 요일을 나타내는 타입이며, 미리 정의된 `last` 개체를 사용합니다.

```cpp
// 2023년 12월의 마지막 날
std::chrono::year_month_day date{std::chrono::year{2023}/std::chrono::month{12}/std::chrono::last};
EXPECT_TRUE(date.day() == std::chrono::day{31});
```

다음 예는 `year_month_day`, `year_month_weekday_last`, `year_month_weekday`를 상호 변환하여 특정 요일에 해당하는 날짜나 특정일에 해당하는 요일을 구합니다.

```cpp
{
    // 2023년 11월의 마지막 일요일
    std::chrono::year_month_weekday_last date1{std::chrono::year{2023}/std::chrono::month{11}/std::chrono::Sunday[std::chrono::last]}; 
    std::chrono::year_month_day date2{date1};
    EXPECT_TRUE(date2.day() == std::chrono::day{26}); // 2023년 11월의 마지막 일요일은 26일입니다.
}
{
    // 2023년 11월의 2번째 일요일
    std::chrono::year_month_weekday date1{std::chrono::year{2023}/std::chrono::month{11}/std::chrono::Sunday[2]}; 
    std::chrono::year_month_day date2{date1};
    EXPECT_TRUE(date2.day() == std::chrono::day{12}); // 2023년 11월의 두번째 일요일은 12일입니다.
}
{
    // 2번째 일요일
    std::chrono::weekday_indexed week{std::chrono::Sunday, 2};

    // 2023년 11월의 2번째 일요일
    std::chrono::year_month_weekday date1{std::chrono::year{2023}/std::chrono::month{11}/week}; 
    std::chrono::year_month_day date2{date1};
    EXPECT_TRUE(date2.day() == std::chrono::day{12}); // 2023년 11월의 두번째 일요일은 12일입니다.            
}
{
    // 2023년 11월 21일
    std::chrono::year_month_day date1{std::chrono::year{2023}/std::chrono::month{11}/std::chrono::day{21}};
    std::chrono::year_month_weekday date2{date1};
    EXPECT_TRUE(date2.weekday() == std::chrono::Tuesday); // 목요일입니다.
    EXPECT_TRUE(date2.weekday_indexed() == std::chrono::Tuesday[3]); // 3번째 목요일입니다.
}  
```

**now()로부터 달력 날짜 초기화**

[now()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock)는 [nanoseconds](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)단위로 현재 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)구하는데요, 이를 [floor()](??)를 이용하여 [days](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)단위로 변경하면, `year_month_day`에 저장할 수 있습니다.

```cpp
std::chrono::time_point now{std::chrono::system_clock::now()}; // now()는 nanoseconds 단위로 timpoint를 구합니다.
std::chrono::year_month_day date{std::chrono::floor<std::chrono::days>(now)}; // now를 days단위로 구하여 년-월-일 단위로 저장합니다.
```

**local_days(), sys_days() 형변환 연산자**

달력 날짜인 `year_month_day`, `year_month_day_last`, `year_month_weekday`, `year_month_weekday_last`는 `local_days()`나 `local_days()` [형변환 연산자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%ED%98%95%EB%B3%80%ED%99%98-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%A0%95%EC%9D%98)를 이용하여 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)로 변환할 수 있습니다.

다음 예는 두개의 달력 날짜의 차이를 구하는 예입니다. 달력 날짜끼리 직접 `-`을 할 수 없기 때문에, [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)로 변환하여 차이를 구합니다. [days](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)기반 이기 때문에 `5d`가 출력됩니다.

```cpp
// 2023년 12월 25일
std::chrono::year_month_day date1{std::chrono::year{2023}/std::chrono::month{12}/std::chrono::day{25}};

// 2023년 12월 20일
std::chrono::year_month_day date2{std::chrono::year{2023}/std::chrono::month{12}/std::chrono::day{20}};

std::chrono::days duration{std::chrono::sys_days(date1) - std::chrono::sys_days(date2)}; // 두 달력 날짜의 차이를 구하기 위해 time_point로 변환합니다.

std::cout << duration << std::endl; // 5d 
```

**유효성 검사**

`ok()` 멤버 함수로 유효성 검사를 할 수 있습니다. 윤년이 있더라도 잘 검사해 줍니다.

```cpp
std::chrono::day day29{29};
EXPECT_TRUE(day29.ok() == true);

std::chrono::year_month_day date1{std::chrono::year{2023}/std::chrono::month{2}/day29};
EXPECT_TRUE(date1.ok() == false); // 2023/2/30은 유효하지 않습니다. 28일까지 있습니다.

std::chrono::year_month_day date2{std::chrono::year{2024}/std::chrono::month{2}/day29};
EXPECT_TRUE(date2.ok() == true); // 2023/2/29는 유효합니다. 29일까지 있습니다.
```


# (C++20~) Time Zone

|항목|내용|
|--|--|
|`tzdb` (C++20~)|시간대 데이터베이스 입니다.|
|`tzdb_list` (C++20~)|`tzdb` 목록입니다.|
|`get_tzdb()` (C++20~)<br/>`get_tzdb_list()` (C++20~)<br/>`reload_tzdb()` (C++20~)<br/>`remote_version()` (C++20~)|전역 시간대 데이터베이스를 관리합니다.|
|`locate_zone()` (C++20~)|주어진 이름의 `time_zone`을 구합니다.|
|`current_zone()` (C++20~)|현재 `time_zone`을 구합니다.|
|`time_zone` (C++20~)|시간대를 나타냅니다.|
|`sys_info` (C++20~)|(작성중)|
|`locale_info` (C++20~)|(작성중)|
|`choose` (C++20~)|(작성중)|
|`zoned_traits` (C++20~)|(작성중)|
|[zoned_time](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-time-zone) (C++20~)|지정한 시간대의 시간을 나타냅니다.|
|`leap_second` (C++20~)|(작성중)|
|`leap_second_info` (C++20~)|(작성중)|
|`get_leap_second_info` (C++20~)|(작성중)|
|`time_zone_link` (C++20~)|(작성중)|
|`nonexistent_local_time` (C++20~)|(작성중)|
|`ambiguous_local_time` (C++20~)|(작성중)|

[now()](??)는 UTC 시간대로 리턴하는데요, [zoned_time](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#c20-time-zone)를 이용하면, 특정 지역의 시간대로 변환할 수 있습니다.

다음은 [now()](??)를 이용하여 UTC 시간을 구한뒤, `America/New_York`, `Asia/Shanghai`, `Asia/Seoul`로 변환하여 [format()](??)으로 출력한 예입니다.(*시간대 이름은 [https://www.iana.org/time-zones](https://www.iana.org/time-zones)을 참고하기 바랍니다.*)

```cpp
auto utcTime{std::chrono::system_clock::now()};
std::cout << "utcTime : " << std::format("{:%Y/%m/%d %H:%M:%S}", utcTime) << std::endl; // 2023/12/17 13:25:45.131857000

std::chrono::zoned_time newyork{"America/New_York", utcTime};
std::cout << "localTime : " << std::format("{:%Y/%m/%d %H:%M:%S}", newyork) << std::endl; // 2023/12/17 08:25:45.131857000

std::chrono::zoned_time shanghai{"Asia/Shanghai", utcTime};
std::cout << "localTime : " << std::format("{:%Y/%m/%d %H:%M:%S}", shanghai) << std::endl; // 2023/12/17 21:25:45.131857000

std::chrono::zoned_time seoul{"Asia/Seoul", utcTime};
std::cout << "localTime : " << std::format("{:%Y/%m/%d %H:%M:%S}", seoul) << std::endl; // 2023/12/17 22:25:45.131857000
```

# (C++20~) parse()

(작성중)

