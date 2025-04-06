# 모듈(module)을 여러 파일(file)로 분리하기

이 섹션에서는 모듈(module)을 여러 파일(file)로 분리하는 방법에 대해 설명합니다.

## 모듈(module)을 여러 파일(file)로 분리하는 이유

모듈(module)을 여러 파일(file)로 분리하면 다음과 같은 장점이 있습니다:

1. 코드가 더 관리하기 쉬워집니다.
2. 각 모듈(module)이 자체 파일(file)을 가지게 되어 코드를 더 쉽게 찾고 수정할 수 있습니다.
3. 코드의 가독성이 향상됩니다.
4. 팀 작업이 더 쉬워집니다.

## 모듈(module)을 여러 파일(file)로 분리하는 방법

모듈(module)을 여러 파일(file)로 분리하려면 다음과 같은 단계를 따릅니다:

1. 모듈(module)을 선언합니다.
2. 모듈(module)의 내용을 별도의 파일(file)에 정의합니다.

다음은 모듈(module)을 여러 파일(file)로 분리하는 예입니다:

```rust
// src/lib.rs
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

```rust
// src/front_of_house.rs
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

## 모듈(module) 트리(tree)와 파일(file) 구조

모듈(module) 트리(tree)는 파일(file) 구조를 반영합니다. 예를 들어, 위의 예에서 모듈(module) 트리(tree)는 다음과 같습니다:

```
crate
 └── front_of_house
     └── hosting
         └── add_to_waitlist
```

이 구조는 파일(file) 구조와 일치합니다:

```
src
 ├── lib.rs
 └── front_of_house.rs
```

## 모듈(module)을 여러 파일(file)로 분리할 때 주의할 점

모듈(module)을 여러 파일(file)로 분리할 때 주의할 점은 다음과 같습니다:

1. 모듈(module)을 선언할 때 `mod` 키워드(keyword)를 사용합니다.
2. 모듈(module)의 내용을 별도의 파일(file)에 정의할 때 파일(file) 이름은 모듈(module) 이름과 일치해야 합니다.
3. 모듈(module)의 내용을 별도의 파일(file)에 정의할 때 파일(file)은 `src` 디렉토리(directory)에 있어야 합니다.

## 모듈(module)을 여러 파일(file)로 분리하는 예

다음은 모듈(module)을 여러 파일(file)로 분리하는 더 복잡한 예입니다:

```rust
// src/lib.rs
mod front_of_house;
mod back_of_house;

pub use crate::front_of_house::hosting;
pub use crate::back_of_house::serving;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    serving::take_order();
}
```

```rust
// src/front_of_house.rs
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

```rust
// src/back_of_house.rs
pub mod serving {
    pub fn take_order() {}
}
```

이렇게 하면 각 모듈(module)이 자체 파일(file)을 가지게 되어 코드를 더 쉽게 관리할 수 있습니다.
