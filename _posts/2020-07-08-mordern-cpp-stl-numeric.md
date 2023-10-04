---
layout: single
title: "#8. [모던 C++ STL] 숫자"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++14~) `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표준 사용자 정의 리터럴이 제공됩니다.([표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/) 참고)

# complex

복소수를 처리하는 개체입니다.

|항목|내용|
|--|--|
|`=`|(작성중)|
|`real()`|(작성중)|
|`imag()`|(작성중)|
|`+=, -=, *=, /=`|(작성중)|
|`+, -`|(작성중)|
|`+, -, *, /`|(작성중)|
|`==`<br/>`!=` (~C++20)|(작성중)|
|`<<, >>`|(작성중)|
|`abs()`|(작성중)|
|`arg()`|(작성중)|
|`norm()`|(작성중)|
|`conj()`|(작성중)|
|`proj()` (C++11~)|(작성중)|
|`polar()`|(작성중)|

> *(C++14~) `operator ""s`, `operator ""min`, `operator ""if`, 등 문자열, 날짜 / 시간, 복소수 관련 표준 사용자 정의 리터럴이 제공됩니다.([표준 사용자 정의 리터럴](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-standard-user-literal/) 참고)*

# valarray

값의 배열을 다루는 개체 입니다.

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
|`hypot(), hypotf(), hypotl()` (C++11~)|(작성중)|

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

**에러 및 감마 함수**

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

**분류 및 비교 함수**

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

**매크로 상수**

|항목|내용|
|--|--|
|`HUGE_VALF, HUGE_VAL, HUGE_VALL` (C++11~)|(작성중)|
|`INFINITY` (C++11~)|(작성중)|
|`NAN` (C++11~)|(작성중)|
|`math_errhandling, MATH_ERRNO, MATH_ERREXCEPT` (C++11~)|(작성중)|
|`FP_NORMAL, FP_SUBNORMAL, FP_ZERO, FP_INFINITE, FP_NAN` (C++11~)|(작성중)|

# 부동 소수점 환경

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

# 특수 수학 함수

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

# 수학 상수

|항목|내용|
|--|--|
|`e_v` (C++20~)|(작성중)|
|`log2e_v` (C++20~)|(작성중)|
|`log10e_v` (C++20~)|(작성중)|
|`pi_v` (C++20~)|(작성중)|
|`inv_pi_v` (C++20~)|(작성중)|
|`inv_sqrtpi_v` (C++20~)|(작성중)|
|`ln2_v` (C++20~)|(작성중)|
|`ln10_v` (C++20~)|(작성중)|
|`sqrt2_v` (C++20~)|(작성중)|
|`sqrt3_v` (C++20~)|(작성중)|
|`inv_sqrt3_v` (C++20~)|(작성중)|
|`egamma_v` (C++20~)|(작성중)|
|`phi_v` (C++20~)|(작성중)|

# 비트 조작

|항목|내용|
|--|--|
|`bit_cast()` (C++20~)|(작성중)|
|`byteswap()` (C++20~)|(작성중)|
|`has_single_bit()` (C++20~)|(작성중)|
|`bit_ceil()` (C++20~)|(작성중)|
|`bit_floor()` (C++20~)|(작성중)| 
|`bit_width()` (C++20~)|(작성중)|
|`rotl()` (C++20~)|(작성중)|
|`rotr()` (C++20~)|(작성중)|
|`countl_zero()` (C++20~)|(작성중)| 
|`countl_one()` (C++20~)|(작성중)|
|`countr_zero()` (C++20~)|(작성중)| 
|`countr_one()` (C++20~)|(작성중)|
|`popcount()` (C++20~)|(작성중)| 
|`endian()` (C++20~)|(작성중)|

# C스타일 난수 생성기

|항목|내용|
|--|--|
|`rand()`|(작성중)|
|`srand()`|(작성중)|
|`RAND_MAX`|(작성중)|

# 난수 생성기

|항목|내용|
|--|--|
|`uniform_random_bit_generator` (C++11~)|(작성중)|
|`uniform_random_bit_generator` (C++11~)|(작성중)|
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



