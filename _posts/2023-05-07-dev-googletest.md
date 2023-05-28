TDD 빨강 초록 리팩토링

# 환경
1. mingw-w64 설치

https://sourceforge.net/projects/mingw-w64/files/ x86_64-posix-seh 설치

2. CMake 설치

# googletest 설치

복사하여 사용, 원격지 활용

1. language-test폴더 생성

2. googletest 다운로드
 https://github.com/google/googletest

3. 압축 풀기

4. 생성된 폴더구조 확인

# cmake 빌드 구성

1. cmakelist 파일 작성

2. testcase 파일 구성 작성

3. 빌드

# sample 테스트 실행

1. 소스 코드 작성
2. test 케이스 작성
3. 저장. 빌드. 런.
4. 터미널 표시 확인

# ctest 표시
1. cTest실행 : 안보이는것 확인
2. cmake 리스트 수정. 저장. 빌드.
3. ctest 실행.
4. test팔레트에 보이는 것 확인
5. 테스트 수정. 런(알아서 빌드됨). 확인

# testmate

ctest는 트리구조가 아니라 불편.

1. 설치
2. cmake 수정 - 하위파일 작성(목록표시 안되는 경우). 저장. 빌드.
3. 테스트 리프레쉬
4. testmate 화면 설명(run, goto, 소스코드 표시) 트리구조.
6. 테스트 수정. 빌드. 런. 확인
