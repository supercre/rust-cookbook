
# Rust 프로그래밍 언어 요약

Rust는 안전성(safety), 동시성(concurrency), 성능(performance)을 모두 제공하는 시스템 프로그래밍(system programming) 언어입니다. 이 요약에서는 Rust의 주요 개념(concept)과 기능(feature)을 살펴보겠습니다.

## 기본 개념

- **변수(Variable)와 가변성(Mutability)**: 기본적으로 변수(variable)는 불변(immutable)이지만, `mut` 키워드를 사용하여 가변(mutable)으로 만들 수 있습니다.
- **데이터 타입(Data Type)**: 정수(integer), 부동 소수점(floating-point), 불리언(boolean), 문자(character) 등의 기본 타입(basic type)과 사용자 정의 타입(user-defined type)을 제공합니다.
- **함수(Function)**: `fn` 키워드를 사용하여 함수(function)를 정의할 수 있습니다.
- **제어 흐름(Control Flow)**: `if`, `else`, `loop`, `while`, `for` 등의 제어 흐름(control flow) 구문을 제공합니다.

## 소유권(Ownership)과 빌림(Borrowing)

- **소유권(Ownership)**: Rust의 가장 독특한 특징으로, 메모리 안전성(memory safety)을 보장합니다.
- **이동(Move)**: 값(value)의 소유권(ownership)이 한 변수(variable)에서 다른 변수(variable)로 이동합니다.
- **클론(Clone)**: `clone` 메서드(method)를 사용하여 값(value)의 깊은 복사(deep copy)를 만들 수 있습니다.
- **빌림(Borrowing)**: `&`와 `&mut`를 사용하여 값(value)을 참조(reference)할 수 있습니다.

## 구조체(Struct)와 열거형(Enum)

- **구조체(Struct)**: 관련된 데이터(data)를 하나의 타입(type)으로 묶을 수 있습니다.
- **열거형(Enum)**: 여러 가능한 값(value) 중 하나를 가질 수 있는 타입(type)입니다.
- **패턴 매칭(Pattern Matching)**: `match` 표현식(expression)을 사용하여 값(value)에 따라 다른 코드(code)를 실행할 수 있습니다.

## 모듈(Module)과 패키지(Package)

- **모듈(Module)**: 코드(code)를 논리적으로 구성하고 프라이버시(privacy)를 제어할 수 있습니다.
- **패키지(Package)**: 하나 이상의 크레이트(crate)를 포함하는 단위(unit)입니다.
- **크레이트(Crate)**: 컴파일(compile)할 수 있는 최소한의 코드(code) 단위(unit)입니다.

## 에러 처리(Error Handling)

- **패닉(Panic)**: 복구할 수 없는 에러(error)를 처리하는 방법입니다.
- **결과(Result)**: 복구 가능한 에러(error)를 처리하는 방법입니다.
- **옵션(Option)**: 값(value)이 있거나 없을 수 있는 상황을 처리하는 방법입니다.

## 컬렉션(Collection)

- **벡터(Vector)**: 같은 타입(type)의 값(value)을 저장하는 동적 배열(dynamic array)입니다.
- **문자열(String)**: UTF-8로 인코딩된 텍스트(text)를 저장합니다.
- **해시맵(Hash Map)**: 키(key)와 값(value)의 쌍을 저장합니다.

## 트레이트(Trait)와 제네릭(Generic)

- **트레이트(Trait)**: 공유 동작(shared behavior)을 정의하는 방법입니다.
- **제네릭(Generic)**: 다양한 타입(type)에 대해 작동하는 코드(code)를 작성할 수 있습니다.

## 동시성(Concurrency)

- **스레드(Thread)**: 병렬(parallel)로 실행되는 코드(code)를 작성할 수 있습니다.
- **메시지 전달(Message Passing)**: 스레드(thread) 간에 메시지(message)를 전달할 수 있습니다.
- **공유 상태(Shared State)**: 여러 스레드(thread)가 같은 데이터(data)에 접근할 수 있습니다.

## 비동기 프로그래밍(Asynchronous Programming)

- **퓨처(Future)**: 비동기(asynchronous) 작업(task)을 나타냅니다.
- **async/await**: 비동기(asynchronous) 코드(code)를 작성하는 방법입니다.
- **실행자(Executor)**: 비동기(asynchronous) 작업(task)을 실행합니다.

## 메타프로그래밍(Metaprogramming)

- **매크로(Macro)**: 코드(code)를 생성하는 방법입니다.
- **프로시저 매크로(Procedural Macro)**: 더 복잡한 코드(code) 생성 기능을 제공합니다.

## 안전하지 않은 Rust(Unsafe Rust)

- **안전하지 않은 코드(Unsafe Code)**: 컴파일러(compiler)의 안전성(safety) 검사를 우회할 수 있습니다.
- **안전하지 않은 함수(Unsafe Function)**: 안전하지 않은 코드(unsafe code)를 포함하는 함수(function)입니다.
- **안전하지 않은 트레이트(Unsafe Trait)**: 안전하지 않은 코드(unsafe code)를 포함하는 트레이트(trait)입니다.

## 고급 주제

- **FFI(Foreign Function Interface)**: 다른 언어(language)와 상호작용할 수 있습니다.
- **임베디드(Embedded)**: 하드웨어(hardware)에 직접 접근할 수 있습니다.
- **웹어셈블리(WebAssembly)**: 웹(web) 브라우저(browser)에서 실행할 수 있습니다.
