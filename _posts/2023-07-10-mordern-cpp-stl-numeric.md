---
layout: single
title: "#10. [모던 C++ STL] 숫자"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [complex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex)에 `proj()`이 추가되었습니다.
> * (C++11~) [공통 수학 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#%EA%B3%B5%ED%86%B5-%EC%88%98%ED%95%99-%ED%95%A8%EC%88%98)가 보강되었습니다.
> * (C++11~) [부동 소수점 환경](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c11-%EB%B6%80%EB%8F%99-%EC%86%8C%EC%88%98%EC%A0%90-%ED%99%98%EA%B2%BD)이 추가되었습니다.
> * (C++11~) [난수 생성기](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c11-%EB%82%9C%EC%88%98-%EC%83%9D%EC%84%B1%EA%B8%B0)가 추가되어 다양한 형태의 난수를 만들 수 있습니다.
> * (C++17~) [특수 수학 함수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c17-%ED%8A%B9%EC%88%98-%EC%88%98%ED%95%99-%ED%95%A8%EC%88%98)가 추가되었습니다.
> * (C++20~) [수학 상수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98)(*[e_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98), [log2e_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98), [log10e_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98), [pi_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98) 등*)가 추가되었습니다.
> * (C++20~) [비트 조작](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91) 관련 함수(*[has_single_bit(), bit_ceil(), bit_floor(), bit_width(), rotl(), rotr(), countl_zero(), countl_one(), countr_zero(), countr_one()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91)등*)와 [endian](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91)처리가 추가되었습니다.
> * (C++20~) [complex](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex)의 [constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/) 지원이 개선되었습니다.

# complex

복소수를 처리하는 개체입니다.

|항목|내용|[constexpr](https://tango1202.github.io/mordern-cpp/mordern-cpp-constexpr/)|
|--|--|--|
|`=`|(작성중)|(C++20~)|
|`real()`|(작성중)|(C++11~)<br/>(C++20~)|
|`imag()`|(작성중)|(C++11~)<br/>(C++20~)|
|`+=, -=, *=, /=`|(작성중)|(C++20~)|
|`+, -`|(작성중)|(C++20~)|
|`+, -, *, /`|(작성중)|(C++20~)|
|`==`<br/>`!=` (~C++20)|(작성중)|(C++14~)|
|`<<, >>`|(작성중)||
|`abs()`|(작성중)||
|`arg()`|(작성중)||
|`norm()`|(작성중)|(C++20~)|
|`conj()`|(작성중)|(C++20~)|
|`proj()` (C++11~)|(작성중)||
|`polar()`|(작성중)||

> *(C++14~) [표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/)이 추가되어 `operator ""s`, `operator ""min`, `operator ""if`, 등 [문자열](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-string/), [날짜 / 시간](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-chrono/), [복소수](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#complex) 관련 표현이 간편해 졌습니다.*

# valarray

값의 집합을 다루는 개체 입니다.

|항목|내용|
|--|--|
|`=`|(작성중)|
|`[]`|(작성중)|
|`+, -, ~, !`|(작성중)|
|`+=, -=, *=, /=, %=`|(작성중)|
|`&=, |=, ^=`|(작성중)|
|`<<, >>`|(작성중)|
|`swap()`|(작성중)|
|`size()`|(작성중)|
|`resize()`|(작성중)|
|`sum()`|(작성중)|
|`min()`|(작성중)|
|`max()`|(작성중)|
|`shift()`|(작성중)|
|`cshift()`|(작성중)|
|`apply()`|(작성중)|

다음의 유틸리티 개체가 있습니다.

|항목|내용|
|--|--|
|`slice`|(작성중)|
|`slice_array`|(작성중)|
|`gslice`|(작성중)|
|`gslice_array`|(작성중)|
|`mask_array`|(작성중)|
|`indirect_array`|(작성중)|
|`slice`|(작성중)|

# 공통 수학 함수

|항목|내용|
|--|--|
|`abs()`<br/>`labs()`<br/>`llabs()` (C++11~)|(작성중)|
|`div()`<br/>`ldiv()`<br/>`lldev()` (C++11~)|(작성중)|
|`imaxabs()` (C++11~)|(작성중)|
|`imaxdiv()` (C++11~)|(작성중)|
|`fabs()`<br/>`fabsf(), fabsl` (C++11~)|(작성중)|
|`fmod()`<br/>`fmodf(), fmodl()` (C++11~)|(작성중)|
|`remainder(), remainderf(), remainderl()` (C++11~)|(작성중)|
|`remquo(), remquof(), remquol()` (C++11~)|(작성중)|
|`fma(), fmaf(), fmal()` (C++11~)|(작성중)|
|`fmax(), fmaxf(), fmaxl()` (C++11~)|(작성중)|
|`fmin(), fminf(), fminl()` (C++11~)|(작성중)|
|`fdim(), fdimf(), fdiml()` (C++11~)|(작성중)|
|`nan(), nanf(), nanl()` (C++11~)|(작성중)|

**지수 함수**

|항목|내용|
|--|--|
|`exp()`<br/>`expf(), expl()` (C++11~)|(작성중)|
|`exp2(), exp2f(), exp2l()` (C++11~)|(작성중)|
|`expm1(), expm1f(), expm1l()` (C++11~)|(작성중)|
|`log()`<br/>`logf(), logl()` (C++11~)|(작성중)|
|`log10()`<br/>`log10f(), log10l()` (C++11~)|(작성중)|
|`log2(), log2f(), log2l()` (C++11~)|(작성중)|
|`log1p(), log1pf(), log1pl()` (C++11~)|(작성중)|

**거듭 제곱 함수**

|항목|내용|
|--|--|
|`pow()`<br/>`powf(), powl()` (C++11~)|(작성중)|
|`sqrt()`<br/>`sqrtf(), sqrtl()` (C++11~)|(작성중)|
|`cbrt(), cbrtf(), cbrtl()` (C++11~)|(작성중)|
|`hypot(x, y), hypotf(), hypotl()` (C++11~)|`sqrt(x^2 + y^2)`을 계산합니다.|

**삼각 함수**

|항목|내용|
|--|--|
|`sin()`<br/>`sinf(), sinl()` (C++11~)|(작성중)|
|`cos()`<br/>`cosf(), cosl()` (C++11~)|(작성중)|
|`tan()`<br/>`tanf(), tanl()` (C++11~)|(작성중)|
|`asin()`<br/>`asinf(), asinl()` (C++11~)|(작성중)|
|`acos()`<br/>`acosf(), acosl()` (C++11~)|(작성중)|
|`atan()`<br/>`atanf(), atanl()` (C++11~)|(작성중)|
|`atan2()`<br/>`atan2f(), atan2l()` (C++11~)|(작성중)|
|`sinh()`<br/>`sinhf(), sinhl()` (C++11~)|(작성중)|
|`cosh()`<br/>`coshf(), coshl()` (C++11~)|(작성중)|
|`tanh()`<br/>`tanhf(), tanhl()` (C++11~)|(작성중)|
|`asinh(), asinhf(), asinhl()` (C++11~)|(작성중)|
|`acosh(), acoshf(), acoshl()` (C++11~)|(작성중)|
|`atanh(), atanhf(), atanhl()` (C++11~)|(작성중)|

**(C++11~) 에러 및 감마 함수**

|항목|내용|
|--|--|
|`erf(), erff(), erfl()` (C++11~)|(작성중)|
|`erfc(), erfcf(), erfcl()` (C++11~)|(작성중)|
|`tgamma(), tgammaf(), tgammal()` (C++11~)|(작성중)|
|`lgamma(), lgammaf(), lgammal()` (C++11~)|(작성중)|

**부동 소수점 함수**

|항목|내용|
|--|--|
|`ceil()`<br/>`ceilf(), ceill()` (C++11~)|(작성중)|
|`floor()`<br/>`floorf(), floorl()` (C++11~)|(작성중)|
|`trunc(), truncf(), truncl()` (C++11~)|(작성중)|
|`round(), roundf(), roundl(), lround(), lroundf(), lroundl(), llround(), llroundf(), llroundl()` (C++11~)|(작성중)|
|`nearbyint(), nearbyintf(), nearbyintl()` (C++11~)|(작성중)|
|`rint(), rintf(), rintl(), lrint(), lrintf(), lrintl(), llrint(), llrintf(), llrintl()` (C++11~)|(작성중)|
|`frexp()`<br/>`frexpf(), frexpl()` (C++11~)|(작성중)|
|`ldexp()`<br/>`ldexpf(), ldexpl()` (C++11~)|(작성중)|
|`modf()`<br/>`modff(), modfl()` (C++11~)|(작성중)|
|`scalbn(), scalbnf(), scalbnl(), scalbln(), scalblnf(), scalblnl()` (C++11~)|(작성중)|
|`ilogb(), ilogbf(), ilogbl()` (C++11~)|(작성중)|
|`logb(), logbf(), logbl()` (C++11~)|(작성중)|
|`nextafter(), nextafterf(), nextafterl(),nexttoward(), nexttowardf(), nexttowardl()` (C++11~)|(작성중)| 
|`copysign(), copysignf(), copysignl()` (C++11~)|(작성중)|

**(C++11~) 분류 및 비교 함수**

|항목|내용|
|--|--|
|`fpclassify()` (C++11~)|(작성중)|
|`isfinite()` (C++11~)|(작성중)|
|`isinf()` (C++11~)|(작성중)|
|`isnan()` (C++11~)|(작성중)|
|`signbit()` (C++11~)|(작성중)|
|`isgreater()` (C++11~)|(작성중)|
|`isgreaterequal()` (C++11~)|(작성중)|
|`isless()` (C++11~)|(작성중)|
|`islessequal()` (C++11~)|(작성중)|
|`islessgreater()` (C++11~)|(작성중)|
|`isunordered()` (C++11~)|(작성중)| 
|`islessgreater()` (C++11~)|(작성중)|

**(C++11~) 매크로 상수**

|항목|내용|
|--|--|
|`HUGE_VALF, HUGE_VAL, HUGE_VALL` (C++11~)|(작성중)|
|`INFINITY` (C++11~)|(작성중)|
|`NAN` (C++11~)|(작성중)|
|`math_errhandling, MATH_ERRNO, MATH_ERREXCEPT` (C++11~)|(작성중)|
|`FP_NORMAL, FP_SUBNORMAL, FP_ZERO, FP_INFINITE, FP_NAN` (C++11~)|(작성중)|

# (C++11~) 부동 소수점 환경

|항목|내용|
|--|--|
|`feclearexcept()` (C++11~)|(작성중)|
|`fetestexcept()` (C++11~)|(작성중)|
|`feraiseexcept()` (C++11~)|(작성중)|
|`fegetexceptflag(), fesetexceptflag()` (C++11~)|(작성중)|
|`fegetround(), fesetround()` (C++11~)|(작성중)|
|`fegetenv(), fesetenv()` (C++11~)|(작성중)|
|`FE_ALL_EXCEPT, FE_DIVBYZERO, FE_INEXACT, FE_INVALID, FE_OVERFLOW, FE_UNDERFLOW` (C++11~)|(작성중)|
|`FE_DOWNWARD, FE_TONEAREST, FE_TOWARDZERO, FE_UPWARD` (C++11~)|(작성중)|
|`FE_DFL_ENV` (C++11~)|(작성중)|

# (C++17~) 특수 수학 함수

|항목|내용|
|--|--|
|`assoc_laguerre(), assoc_laguerref(), assoc_laguerrel()` (C++17~)|(작성중)|
|`assoc_legendre(), assoc_legendref(), assoc_legendrel()` (C++17~)|(작성중)|
|`beta, betaf, betal` (C++17~)|(작성중)|
|`comp_ellint_1(), comp_ellint_1f(), comp_ellint_1l()` (C++17~)|(작성중)|
|`comp_ellint_2(), comp_ellint_2f(), comp_ellint_2l()` (C++17~)|(작성중)|
|`comp_ellint_3(), comp_ellint_3f(), comp_ellint_3l()` (C++17~)|(작성중)|
|`cyl_bessel_i(), cyl_bessel_if(), cyl_bessel_il()` (C++17~)|(작성중)|
|`cyl_bessel_j(), cyl_bessel_jf(), cyl_bessel_jl()` (C++17~)|(작성중)|
|`cyl_bessel_k(), cyl_bessel_kf(), cyl_bessel_kl()` (C++17~)|(작성중)|
|`cyl_neumann(), cyl_neumannf(), cyl_neumannl()` (C++17~)|(작성중)|
|`ellint_1(), ellint_1f(), ellint_1l()` (C++17~)|(작성중)|   
|`ellint_2(), ellint_2f(), ellint_2l()` (C++17~)|(작성중)|   
|`ellint_3(), ellint_3f(), ellint_3l()` (C++17~)|(작성중)|  
|`expint(), expintf(), expintl()` (C++17~)|(작성중)| 
|`hermite(), hermitef(), hermitel()` (C++17~)|(작성중)| 
|`legendre(), legendref(), legendrel()` (C++17~)|(작성중)|
|`laguerre(), laguerref(), laguerrel()` (C++17~)|(작성중)|
|`riemann_zeta(), riemann_zetaf(), riemann_zetal()` (C++17~)|(작성중)|
|`sph_bessel(), sph_besself(), sph_bessell()` (C++17~)|(작성중)|
|`sph_legendre(), sph_legendref(), sph_legendrel()` (C++17~)|(작성중)|
|`sph_neumann(), sph_neumannf(), sph_neumannl()` (C++17~)|(작성중)|

# (C++20~) 수학 상수

|항목|내용|
|--|--|
|[e_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98) (C++20~)|[오일러 수](https://en.wikipedia.org/wiki/E_(mathematical_constant))<br/><img width="245" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/90f2077e-ffea-4cd5-bd6e-e036d3a36a08">|
|[log2e_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98) (C++20~)|<img width="43" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/1e32dbaa-d134-4e3d-9aac-1b2ccc0fcb84">|
|[log10e_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98) (C++20~)|<img width="52" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/aa6ab086-9f26-4b2b-a218-94a880fc2680">|
|[pi_v](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EC%88%98%ED%95%99-%EC%83%81%EC%88%98) (C++20~)|[파이](https://en.wikipedia.org/wiki/Pi)<br/><img width="55" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/e3f23723-32fe-4943-a87d-ef470de616c1">|
|`inv_pi_v` (C++20~)|<img width="18" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/ae7aef0f-ba24-4f34-b1c1-cd9137a6c630">|
|`inv_sqrtpi_v` (C++20~)|<img width="28" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/40059883-c445-4beb-8fa2-455ed02653d3">|
|`ln2_v` (C++20~)|<img width="31" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/cefb7d4e-34f4-4de5-9e82-8154e43ed516">|
|`ln10_v` (C++20~)|<img width="39" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/330ebfe7-b977-4b10-97fa-9deafb18e02b">|
|`sqrt2_v` (C++20~)|<img width="30" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/a81004ed-e999-455d-a54b-df50fdb30a51">|
|`sqrt3_v` (C++20~)|<img width="30" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/796a3056-289e-4f03-ba37-d689e4b08e9b">|
|`inv_sqrt3_v` (C++20~)|<img width="26" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/9fac8348-91c1-4e72-a173-156784491bba">|
|`egamma_v` (C++20~)|[오일러 상수](https://en.wikipedia.org/wiki/Euler%27s_constant)<br/><img width="162" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/3b2da0ae-a9b3-447e-8a40-9c3c0f6c1d22">|
|`phi_v` (C++20~)|[황금 비율](https://en.wikipedia.org/wiki/Golden_ratio)<br/><img width="210" alt="image" src="https://github.com/tango1202/tango1202.github.io/assets/133472501/bab09abb-629f-4f5c-9b51-81b19cd8cb04">|

# (C++20~) 비트 조작

|항목|내용|
|--|--|
|`bit_cast()` (C++20~)|비트 단위로 [reinterpret_cast](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-conversions/#%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98)를 합니다.|
|`has_single_bit()` (C++20~)|2의 거듭제곱인지 검사합니다.|
|`bit_ceil()` (C++20~)|주어진 수를 올림한 2의 거듭 제곱입니다.|
|`bit_floor()` (C++20~)|주어진 수를 내림한 2의 거듭 제곱입니다.| 
|`bit_width()` (C++20~)|주어진 수를 저장할 수 있는 비트 개수입니다.|
|`rotl()` (C++20~)|비트를 왼쪽으로 회전합니다.|
|`rotr()` (C++20~)|비트를 오른쪽으로 회전합니다.|
|`countl_zero()` (C++20~)|왼쪽 비트에서 연속된 `0`의 갯수입니다.| 
|`countl_one()` (C++20~)|왼쪽 비트에서 연속된 `1`의 갯수입니다.|
|`countr_zero()` (C++20~)|오른쪽 비트에서 연속된 `0`의 갯수입니다.| 
|`countr_one()` (C++20~)|오른쪽 비트에서 연속된 `1`의 갯수입니다.|
|`popcount()` (C++20~)|`1`의 갯수 입니다.| 
|`endian` (C++20~)|현 플랫폼의 바이트 저장 순서입니다.<br/>* `little` : 리틀 엔디안 입니다. 낮은 주소에 하위 바이트를 저장합니다.(*Windows*)<br/>* `big` : 빅 엔디안 입니다. 낮은 주소에 상위 바이트를 저장합니다.(*Linux 등*)<br/>* `native` : 플랫폼이 리틀 엔디안을 사용하면 `litte`이고, 빅 엔디안을 사용하면 `big`입니다.|
|`byteswap()` (C++23~)|주어진 정수값의 바이트를 뒤집습니다.|

다음은 [비트 조작](?https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91)의 예입니다. 입력값으로 부호 없는 정수값을 사용하여야 합니다.

```cpp
// 2의 거듭 제곱인지 검사합니다. 이때 부호없는 정수를 사용해야 합니다.
static_assert(std::has_single_bit(0u) == false);
static_assert(std::has_single_bit(1u) == true); // 2^0
static_assert(std::has_single_bit(2u) == true); // 2^1
static_assert(std::has_single_bit(3u) == false); 
static_assert(std::has_single_bit(4u) == true); // 2^2 

static_assert(std::bit_ceil(3u) == 4); // 주어진 수를 올림한 2의 거듭 제곱입니다.
static_assert(std::bit_floor(3u) == 2); // 주어진 수를 내림한 2의 거듭 제곱입니다.
static_assert(std::bit_width(0b00000011u) == 2); // 주어진 수를 저장할 수 있는 비트 개수입니다.

static_assert(std::rotl(static_cast<std::uint8_t>(0b10000110u), 2) == 0b00011010); // 비트를 왼쪽으로 회전합니다.
static_assert(std::rotr(static_cast<std::uint8_t>(0b10000110u), 2) == 0b10100001); // 비트를 오른쪽으로 회전합니다.

static_assert(std::countl_zero(static_cast<std::uint8_t>(0b00110000u)) == 2); // 왼쪽 비트에서 연속된 0의 갯수입니다.
static_assert(std::countl_one(static_cast<std::uint8_t>(0b11110000u)) == 4); // 왼쪽 비트에서 연속된 1의 갯수입니다.
static_assert(std::countr_zero(static_cast<std::uint8_t>(0b00110000u)) == 4); // 오른쪽 비트에서 연속된 0의 갯수입니다.
static_assert(std::countr_one(static_cast<std::uint8_t>(0b00110011u)) == 2); // 오른쪽 비트에서 연속된 1의 갯수입니다.

static_assert(std::popcount(0b00110011u) == 4); // 1의 갯수 입니다.
```

다음은 4byte인 [uint32_t](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#c11-%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%A0%95%EC%88%98-%ED%83%80%EC%9E%85)의 [endian](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91)에 따른 저장 방식을 확인한 예입니다. 리틀 엔디안의 경우 4byte가 반전되어 저장된 것을 확인할 수 있습니다.

```cpp
union Test {    
    std::uint32_t byte4; // 4byte 정수
    std::uint8_t byte[4]; // 1byte
};

Test test;
test.byte4 = 0x48abcdef; // 4바이트로 저장시

EXPECT_TRUE(test.byte4 == 0x48abcdef);
if constexpr(std::endian::native == std::endian::little) {
    EXPECT_TRUE(test.byte[0] == 0xef); // 낮은 주소에 4바이트중 하위 바이트를 저장합니다.
    EXPECT_TRUE(test.byte[1] == 0xcd);
    EXPECT_TRUE(test.byte[2] == 0xab);
    EXPECT_TRUE(test.byte[3] == 0x48);            
}
else {
    EXPECT_TRUE(test.byte[0] == 0x48); // 낮은 주소에 상위 바이트를 저장합니다.
    EXPECT_TRUE(test.byte[1] == 0xab);
    EXPECT_TRUE(test.byte[2] == 0xcd);
    EXPECT_TRUE(test.byte[3] == 0xef);    
}
```

다음은 2byte인 [uint16_t](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-type/#c11-%EA%B3%A0%EC%A0%95-%EB%84%88%EB%B9%84-%EC%A0%95%EC%88%98-%ED%83%80%EC%9E%85)의 [endian](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-numeric/#c20-%EB%B9%84%ED%8A%B8-%EC%A1%B0%EC%9E%91)에 따른 저장 방식을 확인한 예입니다. 리틀 엔디안의 경우 2byte가 반전되어 저장된 것을 확인할 수 있습니다.

```cpp
union Test {    
    std::uint16_t byte2[2]; // 2byte 정수
    std::uint8_t byte[4]; // 1byte
};
Test test;
test.byte2[0] = 0x48ab; // 2바이트로 저장시
test.byte2[1] = 0xcdef;

EXPECT_TRUE(test.byte2[0] == 0x48ab);
EXPECT_TRUE(test.byte2[1] == 0xcdef);
if constexpr(std::endian::native == std::endian::little) {

    EXPECT_TRUE(test.byte[0] == 0xab); // 낮은 주소에 2바이트중 하위 바이트를 저장합니다.
    EXPECT_TRUE(test.byte[1] == 0x48);
    EXPECT_TRUE(test.byte[2] == 0xef); // 낮은 주소에 2바이트중 하위 바이트를 저장합니다.
    EXPECT_TRUE(test.byte[3] == 0xcd);            
}
else {
    EXPECT_TRUE(test.byte[0] == 0x48); // 낮은 주소에 상위 바이트를 저장합니다.
    EXPECT_TRUE(test.byte[1] == 0xab);
    EXPECT_TRUE(test.byte[2] == 0xcd);
    EXPECT_TRUE(test.byte[3] == 0xef);    
}  
```

# C스타일 난수 생성기

|항목|내용|
|--|--|
|`rand()`|(작성중)|
|`srand()`|(작성중)|
|`RAND_MAX`|(작성중)|

# (C++11~) 난수 생성기

|항목|내용|
|--|--|
|`uniform_random_bit_generator` (C++11~)|(작성중)|
|`linear_congruential_engine` (C++11~)|(작성중)|
|`mersenne_twister_engine` (C++11~)|(작성중)|
|`subtract_with_carry_engine` (C++11~)|(작성중)|
|`discard_block_engine` (C++11~)|(작성중)|
|`independent_bits_engine` (C++11~)|(작성중)|
|`shuffle_order_engine` (C++11~)|(작성중)|
|`random_device` (C++11~)|(작성중)|

다음 알고리즘은 미리 정의되었습니다.

|항목|내용|
|--|--|
|`minstd_rand0` (C++11~)|(작성중)|
|`minstd_rand` (C++11~)|(작성중)|
|`mt19937` (C++11~)|(작성중)|
|`mt19937_64` (C++11~)|(작성중)|
|`ranlux24_base` (C++11~)|(작성중)|
|`ranlux48_base` (C++11~)|(작성중)|
|`ranlux24` (C++11~)|(작성중)|
|`ranlux48` (C++11~)|(작성중)|
|`knuth_b` (C++11~)|(작성중)|
|`default_random_engine` (C++11~)|(작성중)|

**유틸리티**

|항목|내용|
|--|--|
|`generate_canonical` (C++11~)|(작성중)|
|`seed_seq` (C++11~)|(작성중)|

**균일 분포**

|항목|내용|
|--|--|
|`uniform_int_distribution` (C++11~)|(작성중)|
|`uniform_real_distribution` (C++11~)|(작성중)|

**베르누이 분포**

|항목|내용|
|--|--|
|`bernoulli_distribution` (C++11~)|(작성중)|
|`binomial_distribution` (C++11~)|(작성중)|
|`negative_binomial_distribution` (C++11~)|(작성중)|
|`geometric_distribution` (C++11~)|(작성중)|
  
**포아송 분포**

|항목|내용|
|--|--|
|`poisson_distribution` (C++11~)|(작성중)|
|`exponential_distribution` (C++11~)|(작성중)|
|`gamma_distribution` (C++11~)|(작성중)|
|`weibull_distribution` (C++11~)|(작성중)|
|`extreme_value_distribution` (C++11~)|(작성중)|
  
 **정규 분포**

|항목|내용|
|--|--|
|`normal_distribution` (C++11~)|(작성중)|
|`lognormal_distribution` (C++11~)|(작성중)|
|`chi_squared_distribution` (C++11~)|(작성중)|
|`cauchy_distribution` (C++11~)|(작성중)|
|`fisher_f_distribution` (C++11~)|(작성중)|
|`student_t_distribution` (C++11~)|(작성중)|

**표본 분포**

|항목|내용|
|--|--|
|`discrete_distribution` (C++11~)|(작성중)|
|`piecewise_constant_distribution` (C++11~)|(작성중)|
|`piecewise_linear_distribution` (C++11~)|(작성중)|



