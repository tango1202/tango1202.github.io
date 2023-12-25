---
layout: single
title: "#11. [모던 C++ STL] 입출력"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

> * (C++11~) [vscanf(), snprintf(), vsnprintf()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5)가 추가되었습니다.
> * (C++11~) [hexfloat(), defaultfloat(), get_money(), put_money(), get_time(), put_time()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90)이 추가되었습니다.
> * (C++11~) [예외](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c11-%EC%98%88%EC%99%B8)가 추가되었습니다.
> * (C++14~) [quoted()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90)가 추가되어 `stringstream` 으로 입출력시에 공백, 이스케이프 등을 유지시켜 줍니다.
> * (C++20~) [basic_syncbuf, basic_osyncstream, syncbuf, wsyncbuf, osyncstream, wosyncstream](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c20-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%B6%9C%EB%A0%A5)가 추가되어 쓰레드 내에서도 뒤섞이지 않도록 해줍니다. 

# C스타일 입출력

|항목|내용|
|--|--|
|`FILE`|(작성중)|
|`fpos_t`|(작성중)|
|`stdin`<br/>`stdout`<br/>`stderr`|(작성중)|
|`fopen()`|(작성중)|
|`freopen()`|(작성중)|
|`fclose()`|(작성중)|
|`fflush()`|(작성중)|
|`fwide()`|(작성중)|
|`setbuf()`|(작성중)|
|`setvbuf()`|(작성중)|
|`fread()`|(작성중)|
|`fwrite()`|(작성중)|
|`fgetc(), getc()`<br/>`fgetwc(), getwc()`|(작성중)|
|`fgets()`<br/>`fgetws()`|(작성중)|
|`fputc(), putc()`<br/>`fputwc(), putwc()`|(작성중)|
|`fputs()`<br/>`fputws()`|(작성중)|
|`getchar()`<br/>`getwchar()`|(작성중)|
|`gets()` (~C++11)|`stdin`으로부터 문자열을 읽습니다.|
|`putchar()`<br/>`putwchar()`|(작성중)|
|`puts()`|(작성중)|
|`ungetc()`<br/>`ungetwc()`|(작성중)|
|`scanf()`<br/>`fscanf()`<br/>`sscanf()`<br/>`wscanf()`<br/>`fwscanf()`<br/>`swscanf()`|(작성중)|
|`vscanf()` (C++11~)<br/>`vfscanf()` (C++11~)<br/>`vsscanf()` (C++11~)<br/>`vswscanf()` (C++11~)<br/>`vfwscanf()` (C++11~)<br/>`vsscanf()` (C++11~)|(작성중)|
|[printf()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5)<br/>`fprintf()`<br/>`sprintf()`<br/>`snprintf()` (C++11~)<br/>`wprintf()`<br/>`fwprintf()`<br/>`swprintf()`|(작성중)|
|`vprintf()`<br/>`vfprintf()`<br/>`vsprintf()`<br/>`vsnprintf()` (C++11~)<br/>`vwprintf()`<br/>`vfwprintf()`<br/>`vswprintf()`|(작성중)|
|`ftell()`|(작성중)|
|`fgetpos()`|(작성중)|
|`fseek()`|(작성중)|
|`fsetpos()`|(작성중)|
|`rewind()`|(작성중)|
|`clearerr()`|(작성중)|
|`feof()`|(작성중)|
|`ferror()`|(작성중)|
|`perror()`|(작성중)|
|`remove()`|(작성중)|
|`rename()`|(작성중)|
|`tmpfile()`|(작성중)|
|`tmpnam()`|(작성중)|
|`EOF`|(작성중)|
|`FOPEN_MAX`|(작성중)|
|`FILENAME_MAX`|(작성중)|
|`BUFSIZ`|(작성중)|
|`_IOFBF, _IOLBF, _IONBF`|(작성중)|
|`SEEK_SET, SEEK_CUR, SEEK_END`|(작성중)|
|`TMP_MAX`|(작성중)|
|`L_tmpnam`|(작성중)|

# 추상 개체

|항목|내용|
|--|--|
|`ios_base`|(작성중)|
|`basic_ios`|(작성중)|
|`basic_streambuf`|(작성중)|
|`basic_ostream`|(작성중)|
|`basic_istream`|(작성중)|
|`basic_iostream`|(작성중)|

# 파일 입출력

|항목|내용|
|--|--|
|`basic_iostream`|(작성중)|
|`basic_ifstream`|(작성중)|
|`basic_ofstream`|(작성중)|
|`basic_fstream`|(작성중)|

# 문자열 입출력

|항목|내용|
|--|--|
|`basic_stringbuf`|(작성중)|
|`basic_istringstream`|(작성중)|
|`basic_ostringstream`|(작성중)|
|`basic_stringstream`|(작성중)|

# 배열 입출력

|항목|내용|
|--|--|
|`basic_spanbuf` (C++23~)|(작성중)|
|`basic_ispanstream` (C++23~)|(작성중)|
|`basic_ospanstream` (C++23~)|(작성중)|
|`basic_spanstream` (C++23~)|(작성중)|
|`strstreambuf` (~C++98)|(작성중)|
|`istrstream` (~C++98)|(작성중)|
|`ostrstream` (~C++98)|(작성중)|
|`strstream` (~C++98)|(작성중)|

# 스트림 기반 타입

|항목|내용|
|--|--|
|`ios`|`basic_ios<char>`|
|`wios`|`basic_ios<wchar_t>`|
|`wstreambuf`|`basic_streambuf<wchar_t>`|
|`istream`|`basic_istream<char>`|
|`wistream`|`basic_istream<wchar_t>`|
|`iostream`|`basic_iostream<char>`|
|`wiostream`|`basic_iostream<wchar_t>`|
|`ostream`| `basic_ostream<char>`|
|`wostream`|`basic_ostream<wchar_t>`|
|`filebuf`|`basic_filebuf<char>`|
|`wfilebuf`|`basic_filebuf<wchar_t>`|
|`ifstream`|`basic_ifstream<char>`|
|`wifstream`|`basic_ifstream<wchar_t>`|
|`ofstream`|`basic_ofstream<char>`|
|`wofstream`|`basic_ofstream<wchar_t>`|
|`fstream`|`basic_fstream<char>`|
|`wfstream`|`basic_fstream<wchar_t>`|
|`stringbuf`|`basic_stringbuf<char>`|
|`wstringbuf`|`basic_stringbuf<wchar_t>`|
|`istringstream`|`basic_istringstream<char>`|
|`wistringstream`|`basic_istringstream<wchar_t>`|
|`ostringstream`|`basic_ostringstream<char>`|
|`wostringstream`|`basic_ostringstream<wchar_t>`|
|`stringstream`|`basic_stringstream<char>`|
|`wstringstream`|`basic_stringstream<wchar_t>`|
|`spanbuf` (C++23~)|`basic_spanbuf<char>`|
|`wspanbuf` (C++23~)|`basic_spanbuf<wchar_t>`|
|`ispanstream` (C++23~)|`basic_ispanstream<char>`|
|`wispanstream` (C++23~)|`basic_ispanstream<wchar_t>`|
|`ospanstream` (C++23~)|`basic_ospanstream<char>`|
|`wospanstream` (C++23~)|`basic_ospanstream<wchar_t>`|
|`spanstream` (C++23~)|`basic_spanstream<char>`|
|`wspanstream` (C++23~)|`basic_spanstream<wchar_t>`|

# 사전 정의된 표준 스트림 개체

|항목|내용|
|--|--|
|`cin, wcin`|`istream`개체로서 표준 입력을 받습니다.|
|`cout, wcout`|`ostream`개체로서 표준 출력을 합니다.|
|`cerr, wcerr`|(작성중)|
|`clog, wclog`|(작성중)|

# 서식 조정자

기존에는 개체의 내용을 서식화할때 [C스타일 입출력](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5)의 [printf()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9E%85%EC%B6%9C%EB%A0%A5) 계열에서 `%d, %s`등을 이용한 방법이 있었는데요, 기본 타입만 지원하는 한계가 있었습니다.

C++에서는 `<<`과 `>>`를 [연산자 오버로딩](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-operators/#%EC%97%B0%EC%82%B0%EC%9E%90-%EC%98%A4%EB%B2%84%EB%A1%9C%EB%94%A9)하여 사용자 정의 타입을 서식화 할 수 있으며, 기본 타입은 [서식 조정자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90)를 통해 서식화 합니다.

|항목|내용|서식 플래그|
|--|--|--|
|`ends()`|[널문자](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-string/#%EB%84%90%EC%A2%85%EB%A3%8C-%EB%AC%B8%EC%9E%90%EC%97%B4)를 출력합니다.|
|`endl()`|`\n`을 출력합니다.||
|`resetiosflags()`|설정값을 초기화 합니다.||
|`setiosflags()`|(작성중)||
|`boolalpha()`<br/>`noboolalpha()`|[bool](https://tango1202.github.io/legacy-cpp-guide/legacy-cpp-guide-bool/) 출력시 1, 0으로 출력(*기본값*)할지, `true`, `false`할지 지정합니다.|`boolalpha`|
|`showpoint()`<br/>`noshowpoint()`|소수점 이하 값이 없는 경우 소수점 이하 표시 여부입니다.|`showpoint`|
|`showpos()`<br/>`noshowpos()`|양수인 경우 `+`의 표시 여부입니다.|`showpos`|
|`setfill()`|빈 공간을 주어진 문자로 채웁니다.||
|`left()`<br/>`right()`<br/>`internal()`|왼쪽/오른쪽 정렬을 합니다.<br/>`internal()`은 오른쪽 정렬을 하지만, 부호 표시와 숫자 사이를 띄웁니다.|`left`, `right`, `internal`(*`left|right|internal`*)|
|`dec()`<br/>`hex()`<br/>`oct()`|10진수, 8진수, 16진수로 표시합니다.|`dec`, `oct`, `hex`, `basefield`(*`dec|oct|hex`*)|
|`showbase()`<br/>`noshowbase()`|`0`(*8진수*), `0x`(*16진수*)의 진법 접두어를 표시합니다.|`showbase`|
|`uppercase()`<br/>`nouppercase()`|16진수나 부동 소수점의 지수 표기(`e`)에서 사용하는 알파벳을 대문자로 표시합니다.|`uppercase`| 
|[setprecision()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%84%9C%EC%8B%9D-%EC%A1%B0%EC%A0%95%EC%9E%90)|소수점이하 정밀도 입니다.|| 
|`setw()`|최소 너비 입니다.|| 
|`fixed()`<br/>`scientific()`<br/>`hexfloat()` (C++11~)<br/>`defaultfloat()` (C++11~)|부동 소수점을 고정폭으로 할지, 지수로 할지, 16진수로 할지 디폴트(*상황에 따라 지수나 고정폭으로 표시합니다*)로 할지 지정합니다.|`fixed`, `scientific`, `floatfield`(*`fixed|scientific`*)|
|`skipws()`<br/>`noskipws()`|(작성중)|`skipws`| 
|`unitbuf()`<br/>`nounitbuf()`|(작성중)|`unitbuf`|  
|`ws()`|(작성중)||
|`flush()`|(작성중)||
|`emit_on_flush()` (C++20~)<br/>`noemit_on_flush()` (C++20~)|(작성중)||
|`flush_emit()`|(작성중)|| 
|`setbase()`|(작성중)|| 
|`get_money()` (C++11~)|(작성중)||
|`put_money()` (C++11~)|통화 유형에 맞춰 표시합니다.|| 
|`get_time()` (C++11~)|(작성중)|| 
|`put_time()` (C++11~)|(작성중)|| 
|`quoted()` (C++14~)|`stringstream` 으로 입출력시에 공백, 이스케이프 등을 유지시켜 줍니다.||   

서식을 변경하면 스트림에 해당 정보가 저장되어 다음번에도 서식이 적용됩니다.

따라서 다음과 같이 `resetiosflags()`를 이용하여 서식을 초기화해야 합니다. 서식 플래그가 없는 항목은 초기값을 재설정합니다.

```cpp
std::cout << 100 << std::endl; // 100 기본적으로 양수인 경우 +를 표시하지 않습니다.
std::cout << std::showpos << 100 << std::endl; // +100 showpos를 사용하여 +를 표시합니다.
std::cout << 100 << std::endl; // +100 기본 형태로 사용해도 +를 표시합니다.

std::cout << std::resetiosflags(std::ios_base::showpos); // showpos 설정을 초기화합니다.
std::cout << 100 << std::endl; // 100을 표시합니다.
```

다음은 서식화의 사용예입니다.

```cpp
std::cout << true << std::endl; // 기본적으로 0, 1로 표시합니다.
std::cout << std::boolalpha << true << std::endl; // true, flase로 표시합니다.
std::cout << std::noboolalpha << true << std::endl; // 0, 1로 표시합니다.

std::cout << std::showpoint << 3. << std::endl; // 3.00000 소수점을 표시합니다.
std::cout << std::noshowpoint << 3. << std::endl; // 3 소수점을 표시하지 않습니다.

std::cout << std::showpos << 100 << std::endl; // +100 으로 표시합니다.
std::cout << std::noshowpos << 100 << std::endl; // 100 으로 표시합니다.

std::cout << std::resetiosflags(std::ios_base::boolalpha | std::ios_base::showpoint | std::ios_base::showpos);

std::cout << std::left << std::setfill('*') << std::setw(5) << -10 << std::endl; // -10**
std::cout << std::right << std::setfill('*') << std::setw(5) << -10 << std::endl; // **-10
std::cout << std::internal << std::setfill('*') << std::setw(5) << -10 << std::endl; // -**10 

std::cout << std::resetiosflags(std::ios_base::adjustfield); 
std::cout << std::setfill(' '); // fill은 서식 플래그가 없어서 초기값을 설정합니다. 

// 0XAB를 표시합니다.
std::cout 
    << std::hex // 16진수로 표시합니다.
    << std::showbase // `0`(*8진수*), `0x`(*16진수*)의 진법 접두어를 표시합니다.
    << std::uppercase // 16진수나 부동 소수점의 지수 표기(`e`)에서 사용하는 알파벳을 대문자로 표시합니다.
    << 0xab 
<< std::endl;   

std::cout << std::resetiosflags(std::ios_base::hex | std::ios_base::showbase | std::ios_base::uppercase);
    
// [][][]3.142을 표시합니다.(앞쪽 공백 3개)
std::cout
    << std::setw(8) // 최소 너비입니다.
    << std::setprecision(4) // 소수점을 포함한 소수점 이하 자리수입니다.
    << 3.141592
<< std::endl; 

std::cout << std::setw(0) << std::setprecision(6); // setw, setprecision은 서식 플래그가 없어서 초기값을 설정합니다. 
```

> *(C++20~) [format(), format_to(), format_to_n()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-formatting/??#%EC%84%9C%EC%8B%9D%ED%99%94-%ED%95%A8%EC%88%98)이 추가되어 `%d, %s` 처럼 간편하고, `<<` 처럼 확장성 있는 서식화 방법을 지원합니다.*<br/>
> *(C++20~) [표준 서식 지정자](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-formatting/??#%ED%91%9C%EC%A4%80-%EC%84%9C%EC%8B%9D-%EC%A7%80%EC%A0%95%EC%9E%90)가 추가되어 채움, 정렬, 부호, 너비, 정밀도를 지정할 수 있습니다.*

# 타입

|항목|내용|
|--|--|
|`streamoff`|(작성중)|
|`streamsize`|(작성중)|
|`fpos`|스트림이나 파일에서의 절대 위치를 나타냅니다.<br/>* `streampos` : `fpos<char_traits<char>::state_type>`<br/>* `wstreampos` : `fpos<char_traits<wchar_t>::state_type>`<br/>* `u8streampos` (C++20~) : `fpos<char_traits<char8_t>::state_type>`<br/>* `u16streampos` (C++11~) : `fpos<char_traits<char16_t>::state_type>`<br/>* `u32streampos` (C++11~) : `fpos<char_traits<char32_t>::state_type>`|

# (C++11~) 예외

|항목|내용|
|--|--|
|`io_errc` (C++11~)|(작성중)|
|`iostream_category` (C++11~)|(작성중)|

# (C++20~) 동기화 출력

쓰레드에서 `std::cout << a << b << std::endl`와 같이 `<<`을 여러개 사용해서 출력하면, 각 쓰레드가 출력하면서 뒤섞이게 됩니다.(*[thread](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-thread-mutex/#thread) 참고*) 따라서, [mutex](??)를 사용해야 했는데요,

C++20 부터는 [basic_syncbuf, basic_osyncstream](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c20-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%B6%9C%EB%A0%A5)가 추가되어 쓰레드 내에서도 뒤섞이지 않도록 해줍니다. 

|항목|내용|
|--|--|
|[basic_syncbuf](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c20-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%B6%9C%EB%A0%A5) (C++20~)|여러개의 `<<` 출력을 단일 출력으로 변경해 줍니다.|
|[basic_osyncstream](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c20-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%B6%9C%EB%A0%A5) (C++20~)|`basic_syncbuf`에 기반하여 쓰레드에서 출력한 내용들이 뒤섞이지 않게 합니다.|
|`syncbuf` (C++20~)|`basic_syncbuf<char>`|
|`wsyncbuf` (C++20~)|`basic_syncbuf<wchar_t>`|
|`osyncstream` (C++20~)|`basic_osyncstream<char>`|
|`wosyncstream` (C++20~)|`basic_osyncstream<wchar_t>`|

다음은 사용예입니다. *(아쉽게도 제 환경에서는 `osyncstream`사용시 실행되지 않는 오류가 있습니다. MinGW-w64(GCC 13.1.0)의 문제일지 다른 문제일지는 좀더 확인해 봐야 할 듯합니다.)*

```cpp
void Message() {
    for(int i{0}; i < 10; ++i) {
        std::this_thread::sleep_for(std::chrono::milliseconds{50}); // 50밀리초 만큼 쉽니다.
        std::cout << "Message : " << i << std::endl;
    }        
}
// std::cout 시 쓰레드 경쟁에 출력이 뒤섞이지 않도록 뮤텍스를 사용합니다.
std::mutex messageMutex;
void MutexMessage() {
    std::lock_guard<std::mutex> lock(messageMutex);
    for(int i{0}; i < 10; ++i) {
        std::this_thread::sleep_for(std::chrono::milliseconds{50}); // 50밀리초 만큼 쉽니다.
        std::cout << "MutexMessage : " << i << std::endl;
    }        
}

void SyncMessage() {
    for(int i{0}; i < 10; ++i) {
        std::this_thread::sleep_for(std::chrono::milliseconds{50}); // 50밀리초 만큼 쉽니다.
        // GCC 13.1.0에서 런타임 오류가 발생합니다.
        // std::osyncstream(std::cout) << "SyncMessage : " << i << std::endl;
    }   
} 
{
    std::thread worker1{Message};
    std::thread worker2{Message};
    worker1.join(); 
    worker2.join(); 
}
{
    std::thread worker1{MutexMessage};
    std::thread worker2{MutexMessage};
    worker1.join(); 
    worker2.join(); 
}  
{
    std::thread worker1{SyncMessage};
    std::thread worker2{SyncMessage};
    worker1.join(); 
    worker2.join(); 
}      
```

# (C++23~) 프린트 함수

|항목|내용|
|--|--|
|`print()` (C++23~)|(작성중)|
|`println()` (C++23~)|(작성중)|
|`vprint_unicode()` (C++23~)|(작성중)|
|`vprint_nonunicode()` (C++23~)|(작성중)|  

