## CGDK 3.0 (2022.07.10)
CGD.buffer에서 CGDK.buffer로 명칭 변경
   ### (C++)
   - CGDK::buffer_view, CGDK::buffer, CGDK::shared_buffer로 buffer 세분화
     CGDK::buffer_view는 extract<T>와 front<T> 기능만 가지며 append<T>는 사용 불가능함.
     CGDK::buffer는 CGDK::buffer_view를 상속받아 append<T> 기능이 추가됨. 
     CGDK::shared_bufer는 스마트 포인터를 사용한 버퍼가 구현되어 메모리의 해제를 자동 관리함.
   - 복합형 멤버를 가진 구조체 직렬화 지원
     std:string, std::vector<T> 등등을 멤버로 가진 구조체(struct 혹은 class)도 append<T>, extract<T>로 바로 직렬화 가능
   - get_size_of<T>() 직렬화될 크기 업는 함수 추가.
   - C++17 지원 => std::string_view 등등 ...
   - C++20 지원 => std::span<T> 등등...
   - unreal 자료구조 지원 추가 (FString, FStringView, TAraay<T>, TLinkedListBase<T>. TMapBase<K,V> ... 등등
   - 형식 문자열을 기존 sprintf에서 fmt::format 혹은 std::format 으로 변경
   - std::tuple<T...>, std::tie<T...> 지원
   - CGDK::buffer의 append, extract 지원
   - 문자열 변환 기능을 가진 appened_text<T> 추가

  ### (C#) 
   - get_size_of<T> 지원
   - .NET, .NET.core, .NET.framework 등등 다양한 플랫폼 지원.

-------------------------------------------------------------------------------------
## 2.0.3pre (2015.7.3)
   ### (C++)
   - CGD::buffer::extract(SKIP) 함수 추가.
   - CGD::buffer::operator>>(SKIP) 추가.
   - CGD::buffer::_prepend_tuple 함수 오타 수정
   - gcc용 C++11버전 지원
     gcc에서도 C++11지원을 위해 config 수정
     gcc warning disable 추가.
     gcc일 경우 _aligned_malloc(msc용) 대신 malloc(stadard c++)으로 사용
     is_memcopy_able gcc 지원용으로 수정
   - CGD::TIME 관련 오류 수정
   - std::tuple<T...> 지원 오류 수정
   - _Xappend_string_format()함수 리펙토링

## 2.0.2pre (2015.6.26)
   ### (C++)
   - Linux 지원. (Linux GCC, cygwin, MinGW, Solaris,  C++11이상 지원 필요)

   - 버그 수정
     CGD::buffer::_append_array 함수: _Count가 0개일 경우 _Data가 nullptr라도 허용하도록 수정.
     CGD::buffer::_append_array 함수: _Count가 0개일 경우 _Data가 nullptr라도 허용하도록 수정.
     CGD::buffer::_extract_string_copy: _LengthInWords 변수명 오류 수정
     string 처리 과정 중 NULL이 char형으로 캐티팅되지 않아 뜨는 warning 수정.
     struct CGD::text<T>: operator 정의 오류 수정
     CGD::_Xextract_container_CGPTR_list 함수 오류 수정
     CGD::append_string(const const_string<T,N>& _String): 컴파일 오류 수정
     CGD::ptr::_prepend_string_format() 함수 오타로 인한 컴파일 오류 수정.
     CGD::ptr::_append_string_format 스트링 길이 계산 오류 수정
     CGD::ptr::_extract_string_copy()함수 파라미터 이름 오류 수정.

## 2.0.1pre (2015.2.26)
   - visual studio 2010용 Project 추가
   - C++용 CGD::buffer Example01 내용 보강
   - C++용 CGD::ptr 오타로 인한 오류 수정
   - C++용 vs2013 컴파일시 Warning 수정

## 2.0pre (2015.1.5) 
   - CGCII 프로젝트에서 독립 프로젝트로 분리
   - CGDK 8.0부터 포함되는 CCGBuffer의 기반
   - 클래스명 변경
     CGBUF     -> CGD::buffer 
     CCGMemPtr -> CGD::ptr

   - 함수명이 전부 소문자로 바뀌었습니다.(예, Append -> append, Extract -> extract)
   - 함수명이 대폭 변경되었습니다.
       Append<T>      -> append<T>
       ExtractHead<T> -> extract<T>
       ExtractTail<T> -> subtract<T>
       Head<T>        -> front<T>
       Tail<T>        -> back<T>

   - C++11 기준의 template meta programming이 적용되었습니다.
   - meta programming이 적용되어 vector/list 등의 복합형을 동일이름으로 동작가능합니다.
   - meta programming이 적용되어 array, vector<T>와 같은 복합형은 
     Block copy를 지원해 성능을 향상시켰습니다.
   - std::string/std::wstring뿐만 아니라 char16_t, char32_t 등 다양한
     문자열을 지원합니다
   - literal로 작성된 문자열은 실시간으로 문자열을 길이를 계산하는 것이
     아니라 컴파일 타임에 계산되어 적용됩니다.(constexpr 일부 지원)
   - vector/array/string 등의 복합형 객체의 다계층을 지원합니다.
   - C#은 struct를 통해 Heterogenous형을 지원합니다.
   - C++버전은 std::tuple을 통해 Heterogenous 복합형을 지원합니다.
   - std::initializer_list를 지원합니다.
   - C++버전은 prepend 기능이 추가되었습니다.
   - C++버전은 배열형 extract 기능도 추가되었습니다.
   - 웹서버 통신에 필요한 형식 추가('\r\n'을 사용하는 문자열 처리 추가)
   - r-value 관련 오류 가능성 수정

## 1.0   CGDK 7.0까지 포함되어 있던 CCGBuffer