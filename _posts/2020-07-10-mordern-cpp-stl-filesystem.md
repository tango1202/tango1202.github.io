---
layout: single
title: "#10. [모던 C++ STL] (C++17~) 파일 시스템"
categories: "mordern-cpp-stl"
tag: ["cpp"]
author_profile: false
sidebar: 
    nav: "docs"
---

C++17 부터는 경로, 파일, 디렉토리에 대한 조작을 수행합니다.

`<filesystem>` 헤더 파일을 포함해야 하며, `std::filesystem` 네임스페이스를 사용합니다.

# 경로 문자열

|항목|내용|
|--|--|
|`path` (C++17~)|경로에서 `root_name()`, `root_directory()`, `root_path()`, `relative_path()`, `parent_path()`, `filename()`, `extension()`을 분해하거나 수정합니다.<br/> `/`의 오버로딩을 제공하여 하위 경로를 연결할 수 있습니다.<br/>`std::filesystem path = std::filesystem::current_path() / "MyData";`|
|`absolute(path)` (C++17~)|현재 경로와 `path`를 결합한 절대 경로를 리턴합니다.| 
|`canonical(path), weakly_canonical(path)` (C++17~)|주어진 `path`의 정규화된 절대 경로를 리턴합니다.| 
|`relative(path, base)` (C++17~)|`path`가 `base`의 하위 경로라면, `base`의 상대 경로를 리턴합니다.|
|`proximate(path, base)` (C++17~)|(작성중)| 

# 경로 탐색

|항목|내용|
|--|--|
|`current_path()` (C++17~)|현재 디렉토리 경로를 구합니다.|
|`temp_directory_path()` (C++17~)|임시 디렉토리 경로를 구합니다.|
|`exists()` (C++17~)|파일이 존재하는지 검사합니다.|
|`equivalent(path1, path2)` (C++17~)|`path1`과 `path2`가 동일한 경로인지 검사합니다.|
|`directory_iterator`(C++17~)|디렉토리내의 파일들을 열거합니다.|
|`recursive_directory_iterator` (C++17~)|하위 디렉토리까지 디렉토리내의 파일들을 열거합니다.|
|`directory_options` (C++17~)|`directory_iterator`에 사용되는 옵션입니다.|  

# 파일 정보

|항목|내용|
|--|--|
|`space(path)` (C++17~)|`space_info`를 구합니다.|
|`space_info` (C++17~)|파일 시스템의 총 크기, 여유 공간, 사용 가능한 공간 정보입니다.|
|`file_size(path)` (C++17~)|`path`인 파일 크기를 구합니다.|
|`file_type` (C++17~)|파일 또는 디렉토리의 종류입니다.|
|`status(path), symlink_status(path)` (C++17~)|`file_status`를 구합니다.|
|`file_status` (C++17~)|파일의 타입과 권한 정보입니다.|
|`last_write_time(path)` (C++17~)|`path`의 마지막 수정 시간을 구합니다.|
|`file_time_type` (C++17~)|파일의 시간입니다.| 
|`permissions(path, perms)` (C++17~)|접근 권한 정보를 수정합니다.|
|`perms` (C++17~)|파일 시스템의 접근 권한 정보입니다.|
|`perm_options` (C++17~)|` permissions()`함수 실행에 사용되는 옵션입니다.|
|`directory_entry` (C++17~)|디렉토리의 추가 정보를 제공합니다.|

# 파일 복사/수정/삭제

|항목|내용|
|--|--|
|`copy(from, to)` (C++17~)|`from` 경로의 파일과 디렉토리를 `to`로 복사합니다.<br/>`copy_options::recursive`를 이용하면 하위 디렉토리까지 복사합니다.| 
|`copy_file(from, to)` (C++17~)|`from` 경로의 파일을 `to`로 복사합니다.| 
|`copy_symlink(from, to)` (C++17~)|`from` 경로의 심볼릭 링크를 `to`로 복사합니다.| 
|`copy_options` (C++17~)|`copy()`함수 실행에 사용되는 옵션입니다.<br/>`none`, `skip_existing`, `overwrite_existing`, `update_existing`, `recursive copy_symlinks`, `skip_symlinks`, `directories_only`,  `create_symlinks`, `create_hard_links` 등이 있습니다.|
|`create_directory(path)` (C++17~)|`path` 디렉토리를 만듭니다. 이때 `path`의 부모 디렉토리는 존재해야 합니다.| 
|`create_directories(path)` (C++17~)|`path` 디렉토리를 만듭니다. 이때 존재하지 않는 디렉토리는 모두 생성합니다.| 
|`create_hard_link()` (C++17~)|(작성중)|
|`create_symlink(), create_directory_symlink()` (C++17~)|심볼릭 링크를 생성합니다.|
|`hard_link_count(path)` (C++17~)|(작성중)|
|`read_symlink(path)` (C++17~)|(작성중)|
|`remove(path), removeall(path)` (C++17~)|`path` 를 삭제하거나 재귀적으로 모두 삭제합니다.|
|`rename(old, new)` (C++17~)|파일의 이름을 변경합니다.|
|`resize_file(path, new_size)` (C++17~)|파일의 크기를 변경합니다.|

# 예외

|항목|내용|
|--|--|
|`filesystem_error` (C++17~)|파일 시스템 처리에서 오류가 발생했습니다.|

# 파일 타입

|항목|내용|
|--|--|
|`is_character_file()` (C++17~)|(작성중)|
|`is_directory()` (C++17~)|(작성중)|
|`is_empty()` (C++17~)|(작성중)|
|`is_fifo()` (C++17~)|(작성중)|
|`is_other()` (C++17~)|(작성중)|
|`is_regular_file()` (C++17~)|(작성중)|
|`is_socket()` (C++17~)|(작성중)|
|`is_symlink()` (C++17~)|(작성중)|
|`status_known()` (C++17~)|(작성중)|
