# Summary

[Rust 프로그래밍 언어 소개](README.md)

# Rust 프로그래밍 언어

- [시작하기](chapter1/Getting_started.md)
  - [설치](chapter1/Installation.md)
  - [Hello, World!](chapter1/Hello_world.md)
  - [Hello, Cargo!](chapter1/Hello_cargo.md)

- [추측 게임 만들기](chapter2/Guessing_game.md)

- [일반적인 프로그래밍 개념](chapter3/Common_Programming_Concepts.md)
  - [변수(Variable)와 가변성(Mutability)](chapter3/Variables_and_Mutablilty.md)
  - [데이터 타입(Data Type)](chapter3/Data_Types.md)
  - [함수(Function)](chapter3/Functions.md)
  - [주석(Comment)](chapter3/Comments.md)
  - [제어 흐름(Control Flow)](chapter3/Control_Flow.md)

- [소유권(Ownership) 이해하기](chapter4/Understanding_ownership.md)
  - [소유권(Ownership)이란?](chapter4/What_is_ownership.md)
  - [참조(Reference)와 빌림(Borrowing)](chapter4/References_and_borrowing.md)
  - [슬라이스(Slice) 타입](chapter4/The_slice_type.md)

- [구조체(Struct)로 관련된 데이터를 구조화하기](chapter5/Using_structs_to_structure_related_data.md)
  - [구조체(Struct) 정의하고 인스턴스화하기](chapter5/Defining_and_instantiating_structs.md)
  - [구조체(Struct)를 사용한 예제 프로그램](chapter5/An_example_program_using_structs.md)
  - [메소드(Method) 문법](chapter5/Method_syntax.md)

- [열거형(Enum)과 패턴 매칭(Pattern Matching)](chapter6/Enums_and_pattern_matching.md)
  - [열거형(Enum) 정의하기](chapter6/Defining_an_enum.md)
  - [match 제어 흐름(Control Flow) 연산자](chapter6/The_match_control_flow_construct.md)
  - [if let을 사용한 간결한 제어 흐름(Control Flow)](chapter6/Concise_control_flow_with_if_let.md)

- [패키지(Package), 크레이트(Crate), 모듈(Module)로 프로젝트 관리하기](chapter7/Packages_Crates_and_Modules.md)
  - [패키지(Package)와 크레이트(Crate)](chapter7/Packages_and_Crates.md)
  - [스코프(Scope)와 비공개성(Privacy)을 제어하기 위한 모듈(Module) 정의하기](chapter7/Defining_Modules_to_Control_Scope_and_Privacy.md)
  - [모듈(Module) 트리에서 항목(Item)을 참조하기 위한 경로(Path)](chapter7/Paths_for_referring_to_an_item_in_the_module_tree.md)
  - [use 키워드로 경로(Path)를 스코프(Scope)로 가져오기](chapter7/Bringing_paths_into_scope_with_the_use_keyword.md)
  - [모듈(Module)을 여러 파일로 분리하기](chapter7/Separating_modules_into_different_files.md)

- [일반적인 컬렉션(Collection)](chapter8/Common_Collections.md)
  - [벡터(Vector)로 값(Value)의 리스트(List) 저장하기](chapter8/Storing_Lists_of_Values_with_Vectors.md)
  - [문자열(String)로 UTF-8 텍스트(Text) 저장하기](chapter8/Storing_UTF-8_Encoded_Text_with_Strings.md)
  - [해시맵(Hash Map)으로 키(Key)와 연관된 값(Value) 저장하기](chapter8/Storing_Keys_with_Associated_Values_in_Hash_Maps.md)

- [에러 처리하기](chapter9/Error_Handling.md)
  - [panic!으로 복구 불가능한 에러 처리하기](chapter9/Unrecoverable_Errors_with_panic.md)
  - [Result로 복구 가능한 에러 처리하기](chapter9/Recoverable_Errors_with_Result.md)
  - [panic!할지 말지 결정하기](chapter9/To_panic_or_not_to_panic.md) 