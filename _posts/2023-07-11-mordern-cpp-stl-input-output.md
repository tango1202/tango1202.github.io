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
> * (C++11~) [hexfloat(), defaultfloat(), get_money(), put_money(), get_time(), put_time()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%9E%85%EC%B6%9C%EB%A0%A5-%EC%A0%9C%EC%96%B4)이 추가되었습니다.
> * (C++11~) [예외](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#c11-%EC%98%88%EC%99%B8)가 추가되었습니다.
> * (C++14~) [quoted()](https://tango1202.github.io/mordern-cpp-stl/mordern-cpp-stl-input-output/#%EC%9E%85%EC%B6%9C%EB%A0%A5-%EC%A0%9C%EC%96%B4)가 추가되어 `stringstream` 으로 입출력시에 공백, 이스케이프 등을 유지시켜 줍니다.

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
|`printf()`<br/>`fprintf()`<br/>`sprintf()`<br/>`snprintf()` (C++11~)<br/>`wprintf()`<br/>`fwprintf()`<br/>`swprintf()`|(작성중)|
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

# (C++20~) 동기화 출력

|항목|내용|
|--|--|
|`basic_syncbuf` (C++20~)|(작성중)|
|`basic_osyncstream` (C++20~)|(작성중)|

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
|`syncbuf` (C++20~)|`basic_syncbuf<char>`|
|`wsyncbuf` (C++20~)|`basic_syncbuf<wchar_t>`|
|`osyncstream` (C++20~)|`basic_osyncstream<char>`|
|`wosyncstream` (C++20~)|`basic_osyncstream<wchar_t>`|

# 사전 정의된 표준 스트림 개체

|항목|내용|
|--|--|
|`cin, wcin`|`istream`개체로서 표준 입력을 받습니다.|
|`cout, wcout`|`ostream`개체로서 표준 출력을 합니다.|
|`cerr, wcerr`|(작성중)|
|`clog, wclog`|(작성중)|

# 입출력 제어

|항목|내용|
|--|--|
|`boolalpha()`<br/>`noboolalpha()`|(작성중)|
|`showbase()`<br/>`noshowbase()`|(작성중)|
|`showpoint()`<br/>`noshowpoint()`|(작성중)|
|`showpos()`<br/>`noshowpos()`|(작성중)|
|`skipws()`<br/>`noskipws()`|(작성중)| 
|`uppercase()`<br/>`nouppercase()`|(작성중)| 
|`unitbuf()`<br/>`nounitbuf()`|(작성중)| 
|`internal()`<br/>`left()`<br/>`right()`|(작성중)| 
|`dec()`<br/>`hex()`<br/>`oct()`|(작성중)| 
|`fixed()`<br/>`scientific()`<br/>`hexfloat()` (C++11~)<br/>`defaultfloat()` (C++11~)|(작성중)| 
|`ws()`|(작성중)|
|`ends()`|(작성중)|
|`flush()`|(작성중)|
|`endl()`|(작성중)|
|`emit_on_flush()` (C++20~)<br/>`noemit_on_flush()` (C++20~)|(작성중)|
|`flush_emit()`|(작성중)| 
|`resetiosflags()`|(작성중)| 
|`setiosflags()`|(작성중)| 
|`setbase()`|(작성중)| 
|`setfill()`|(작성중)| 
|`setprecision()`|(작성중)| 
|`setw()`|(작성중)| 
|`get_money()` (C++11~)|(작성중)| 
|`put_money()` (C++11~)|(작성중)| 
|`get_time()` (C++11~)|(작성중)| 
|`put_time()` (C++11~)|(작성중)| 
|`quoted()` (C++14~)|`stringstream` 으로 입출력시에 공백, 이스케이프 등을 유지시켜 줍니다.|   

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

# (C++23~) 프린트 함수

|항목|내용|
|--|--|
|`print()` (C++23~)|(작성중)|
|`println()` (C++23~)|(작성중)|
|`vprint_unicode()` (C++23~)|(작성중)|
|`vprint_nonunicode()` (C++23~)|(작성중)|  

