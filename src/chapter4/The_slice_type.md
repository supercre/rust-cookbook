# 슬라이스 타입

슬라이스(slice)는 컬렉션의 전체가 아닌 연속된 일련의 요소를 참조할 수 있게 해줍니다. 슬라이스는 참조의 일종이므로 소유권을 가지지 않습니다.

## 문자열 슬라이스

문자열 슬라이스는 `String`의 일부를 참조하는 참조자입니다. 예를 들어:

```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```

여기서 `hello`는 `s`의 처음 5바이트를 참조하는 슬라이스이고, `world`는 `s`의 6번째 바이트부터 11번째 바이트까지의 슬라이스입니다.

슬라이스 문법은 `[starting_index..ending_index]` 형태를 사용하며, 여기서 `starting_index`는 슬라이스의 첫 번째 위치이고, `ending_index`는 슬라이스의 마지막 위치보다 1 더 큰 값입니다. 내부적으로 슬라이스 데이터 구조는 시작 위치와 슬라이스의 길이를 저장합니다.

Rust의 범위 문법에서 시작 인덱스를 생략하면 0부터 시작하는 것으로 간주하고, 끝 인덱스를 생략하면 슬라이스가 컬렉션의 끝까지 이어지는 것으로 간주합니다. 따라서 다음 두 표현은 동일합니다:

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

마찬가지로, 슬라이스가 `String`의 끝까지 포함한다면 끝 인덱스를 생략할 수 있습니다:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

두 인덱스를 모두 생략하면 전체 문자열의 슬라이스를 얻을 수 있습니다:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

## 문자열 슬라이스를 매개변수로 사용하기

문자열 슬라이스를 매개변수로 사용하면 함수가 `String`과 `&str` 둘 다를 받을 수 있게 됩니다:

```rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

이제 `first_word` 함수는 `String`과 `&str` 둘 다를 받을 수 있습니다:

```rust
fn main() {
    let my_string = String::from("hello world");

    // first_word는 String의 슬라이스에서 작동합니다.
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // first_word는 문자열 리터럴의 슬라이스에서 작동합니다.
    let word = first_word(&my_string_literal[..]);

    // 문자열 리터럴은 이미 문자열 슬라이스이므로,
    // 슬라이스 문법 없이도 작동합니다!
    let word = first_word(my_string_literal);
}
```

## 다른 슬라이스들

문자열 슬라이스는 문자열에 특화되어 있지만, 더 일반적인 슬라이스 타입도 있습니다:

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```

이 슬라이스는 `&[i32]` 타입을 가집니다. 이는 문자열 슬라이스가 문자열을 참조하는 것과 같은 방식으로 배열의 일부를 참조합니다. 모든 종류의 컬렉션에 대해 이런 종류의 슬라이스를 사용할 수 있습니다.