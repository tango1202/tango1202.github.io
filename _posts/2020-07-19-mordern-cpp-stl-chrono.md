---
layout: single
title: "#19. [모던 C++ STL] (C++11~) 정밀한 시간 측정(system_clock, time_point, duration)"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock), [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point), [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) 개체를 이용하여 좀더 다양한 정확도로 시간을 추적할 수 있습니다.
> * (C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.
> * (C++17~) [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)와 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)에 반올림 관련 함수인 [floor(), ceil(), round(), abs()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)함수가 추가되었습니다.

# 개요

기존에는 날짜/시간 처리를 위해 C스타일의 [time, difftime](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0)만 제공되었으나([C스타일 시간 유틸리티](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-utility/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0) 참고), 

C++11 부터 STL 에서는 좀더 다양한 정확도로 시간을 추적할 수 있는 [chrono 라이브러리](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/)가 추가되었습니다.

`<chrono>` 헤더 파일을 포함해야 하며, `std::chrono` [네임스페이스](https://tango1202.github.io/classic-cpp-guide/classic-cpp-guide-namespace/)를 사용합니다.

다음 예는 주어진 함수의 실행 시간을 측정하는 예입니다.

1. `Measure()`함수는 전달된 함수를 실행하고 실행 시간을 측정합니다. 실행시킬 함수에 인자를 전달하기 위해 [가변 템플릿](https://tango1202.github.io/mordern-cpp/mordern-cpp-variadic-template/)을 사용합니다.
2. `system_clock::now()`를 이용하여 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)를 구합니다.
3. 구해진 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)를 `microsecond` 타입으로 저장합니다.
4. 두 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)간의 차를 [duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration)으로 변환합니다. 

```cpp
template<typename Func, typename... Params>
std::chrono::microseconds Measure(Func func, Params... params) {
    // func 실행전 time_point 측정
    std::chrono::system_clock::time_point start{std::chrono::system_clock::now()};    

    // func 실행
    func(params...);

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
|`is_clock` (C++20~)<br/>`is_clock_v` (C++20~)|(작성중)|
|`utc_clock` (C++20~)|(작성중)|
|`tai_clock` (C++20~)|(작성중)|
|`gps_clock` (C++20~)|(작성중)|
|`file_clock` (C++20~)|(작성중)|
|`local_t` (C++20~)|(작성중)|

[system_clock](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#clock)은 다음의 멤버 함수가 있습니다.

|항목|내용|
|--|--|
|`now()` (C++11~)|현재 [time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)를 리턴합니다.|
|`to_time_t()` (C++11~)|`time_t`타입으로 변환합니다.|
|`from_time_t()` (C++11~)|`time_t`타입에서 가져옵니다.|

# Time Point

시작 시간 이후 경과된 값입니다.

|항목|내용|
|--|--|
|[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point) (C++11~)|특정 시점을 나타냅니다.|
|`clock_time_conversion` (C++11~)|(작성중)|
|`clock_cast` (C++11~)|(작성중)|

[time_point](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#time-point)는 다음의 멤버 함수가 있습니다.

|항목|내용|
|--|--|
|`time_since_epoch()` (C++11~)|(작성중)|
|`min()` (C++11~)|(작성중)|
|`max()` (C++11~)|(작성중)|
|`time_point_cast()`|(작성중)|
|`floor()` (C++17~)|내림합니다.|
|`ceil()` (C++17~)|올림합니다.|
|`round()` (C++17~)|반올림합니다.|
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

[duration](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/#duration) 은 다음의 멤버 함수가 있습니다.

|항목|내용|
|--|--|
|`=` (C++11~)|(작성중)|
|`count()` (C++11~)|(작성중)|
|`zero()` (C++11~)|(작성중)|
|`min()` (C++11~)|(작성중)|
|`max()` (C++11~)|(작성중)|
|`duration_cast()` (C++11~)|(작성중)|
|`floor()` (C++17~)|내림합니다.|
|`ceil()` (C++17~)|올림합니다.|
|`round()` (C++17~)|반올림합니다.|
|`abs()` (C++17~)|절대값을 구합니다.|
|`from_stream()` (C++20~)|(작성중)|
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
|`nanoseconds` (C++11~)|`duration<int64, std::nano>`|
|`microseconds` (C++11~)|`duration<int54, std::micro>`|
|`milliseconds` (C++11~)|`duration<int44, std::milli>`|
|`seconds` (C++11~)|`duration<int35>`|
|`minutes` (C++11~)|`duration<int29, std::ratio<60>>`|
|`hours` (C++11~)|`duration<int23, std::ratio<3600>>`|
|`days` (C++20~)|`duration<int25, std::ratio<86400>>`|
|`weeks` (C++20~)|`duration<int22, std::ratio<604800>>`|
|`month` (C++20~)|`duration<int20, std::ratio<2629746>>`|
|`years` (C++20~)|`duration<int17, std::ratio<31556952>>`|

다음의 유틸리티가 제공됩니다.

|항목|내용|
|--|--|
|`treat_as_floating_point` (C++11~)|(작성중)|
|`treat_as_floating_point` (C++11~)|(작성중)|
|`duration_values` (C++11~)|(작성중)|
  

# (C++14~) 날짜 / 시간 리터럴

C++14 부터는 [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 제공되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표현이 쉬워졌습니다.

# (C++20~) 시/분/초 서식

|항목|내용|
|--|--|
|`hh_mm_ss` (C++20~)|(작성중)|
|`is_am()` (C++20~)<br/>`is_pm()` (C++20~)<br/>`make12()` (C++20~)<br/>`make24()` (C++20~)|(작성중)|

# (C++20~) Calendar

|항목|내용|
|--|--|
|`last_spec` (C++20~)|(작성중)|
|`day` (C++20~)|(작성중)|
|`month` (C++20~)|(작성중)|
|`year` (C++20~)|(작성중)|
|`weekday` (C++20~)|(작성중)|
|`weekday_indexed` (C++20~)|(작성중)|
|`weekday_last` (C++20~)|(작성중)|
|`month_day` (C++20~)|(작성중)|
|`month_day_last` (C++20~)|(작성중)|
|`month_weekday` (C++20~)|(작성중)|
|`month_weekday_last` (C++20~)|(작성중)|
|`year_month` (C++20~)|(작성중)|
|`year_month_day` (C++20~)|(작성중)|
|`year_month_day_last` (C++20~)|(작성중)|
|`year_month_weekday` (C++20~)|(작성중)|
|`year_month_weekday_last` (C++20~)|(작성중)|
|`operator /` (C++20~)|(작성중)|

# (C++20~) Time Zone

|항목|내용|
|--|--|
|`tzdb` (C++20~)|(작성중)|
|`tzdb_list` (C++20~)|(작성중)|
|`get_tzdb` (C++20~)<br/>`get_tzdb_list` (C++20~)<br/>`reload_tzdb` (C++20~)<br/>`remote_version` (C++20~)|(작성중)|
|`locate_zone` (C++20~)|(작성중)|
|`current_zone` (C++20~)|(작성중)|
|`time_zone` (C++20~)|(작성중)|
|`sys_info` (C++20~)|(작성중)|
|`locale_info` (C++20~)|(작성중)|
|`choose` (C++20~)|(작성중)|
|`zoned_traits` (C++20~)|(작성중)|
|`zoned_time` (C++20~)|(작성중)|
|`leap_second` (C++20~)|(작성중)|
|`leap_second_info` (C++20~)|(작성중)|
|`get_leap_second_info` (C++20~)|(작성중)|
|`time_zone_link` (C++20~)|(작성중)|
|`nonexistent_local_time` (C++20~)|(작성중)|
|`ambiguous_local_time` (C++20~)|(작성중)|


