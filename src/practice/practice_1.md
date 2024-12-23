# 연습문제 1

Rust 학습을 위해 코드를 작성하다가, 레퍼런스와 관련된 컴파일 에러가 발생했습니다.

여기 주어진 코드에서 레퍼런스를 추가하거나, 제거하는 것을 제외하고는 기존 코드를 건드리지 않고 컴파일 에러를 한번 수정해봅시다.

```rust,editable
#![allow(clippy::ptr_arg)]

// TODO: 레퍼런스(`&` 문자)를 추가하거나 제거하는 것 외에는
// 아무것도 수정하지 않고 컴파일러 오류를 해결해야 합니다.

// TODO : Ownership을 가져와서는 안됨.
fn get_char(data: String) -> char {
    data.chars().last().unwrap()
}

// TODO : Ownership을 가져와야함.
fn string_uppercase(mut data: &String) {
    data = data.to_uppercase();

    println!("{data}");
}

fn main() {
    let data = "Rust is great!".to_string();

    get_char(data);

    string_uppercase(&data);
}
```