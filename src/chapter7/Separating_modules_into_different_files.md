# 모듈을 여러 파일로 분리하기

지금까지 이 장의 모든 예제에서는 여러 모듈을 하나의 파일에 정의했습니다. 모듈이 커지면, 코드를 더 쉽게 탐색할 수 있도록 정의를 별도의 파일로 이동하고 싶을 수 있습니다.

예를 들어, 목록 7-17의 레스토랑 코드를 여러 파일로 분리해 보겠습니다. 먼저, 모듈 트리를 다시 살펴보겠습니다:

```
restaurant
 ├── front_of_house
 │   ├── hosting
 │   │   └── add_to_waitlist
 │   └── serving
 │       └── take_order
 └── back_of_house
     ├── fix_incorrect_order
     └── cook_order
```

이 모듈 트리를 여러 파일로 분리하겠습니다:

1. 크레이트 루트 파일: _src/lib.rs_
2. 프론트 오브 하우스 모듈: _src/front_of_house.rs_
3. 프론트 오브 하우스의 호스팅 모듈: _src/front_of_house/hosting.rs_
4. 프론트 오브 하우스의 서빙 모듈: _src/front_of_house/serving.rs_
5. 백 오브 하우스 모듈: _src/back_of_house.rs_

먼저, _src/front_of_house.rs_ 파일을 생성하고 `front_of_house` 모듈의 내용을 이동시킵니다:

파일명: src/front_of_house.rs

```rust
pub mod hosting;
pub mod serving;
```

그런 다음, _src/front_of_house/hosting.rs_ 파일을 생성하고 `hosting` 모듈의 내용을 이동시킵니다:

파일명: src/front_of_house/hosting.rs

```rust
pub fn add_to_waitlist() {}
```

그리고 _src/front_of_house/serving.rs_ 파일을 생성하고 `serving` 모듈의 내용을 이동시킵니다:

파일명: src/front_of_house/serving.rs

```rust
pub fn take_order() {}
```

그런 다음, _src/back_of_house.rs_ 파일을 생성하고 `back_of_house` 모듈의 내용을 이동시킵니다:

파일명: src/back_of_house.rs

```rust
pub fn fix_incorrect_order() {
    cook_order();
    super::deliver_order();
}

fn cook_order() {}
```

마지막으로, _src/lib.rs_ 파일을 수정하여 모듈 선언을 추가합니다:

파일명: src/lib.rs

```rust
mod front_of_house;
mod back_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}

fn deliver_order() {}
```

이제 모듈이 여러 파일로 분리되었습니다. 이 구조에서는 각 모듈이 자체 파일에 있으며, 모듈 트리는 `mod` 선언으로 구성됩니다. 이렇게 하면 모듈이 커지더라도 코드를 더 쉽게 탐색할 수 있습니다.

이 구조에서 `pub use crate::front_of_house::hosting;`는 `hosting` 모듈을 크레이트 루트로 재내보냅니다. 이렇게 하면 외부 코드에서 `restaurant::hosting::add_to_waitlist()`를 사용할 수 있습니다.

이 구조에서는 `mod` 키워드를 사용하여 모듈을 선언하고, `pub` 키워드를 사용하여 모듈을 공개하며, `use` 키워드를 사용하여 모듈을 스코프로 가져옵니다. 이렇게 하면 모듈 시스템을 사용하여 코드를 구성하고, 모듈 트리를 통해 항목을 참조할 수 있습니다.

이제 모듈 시스템을 사용하여 코드를 구성하는 방법을 배웠습니다. 다음 장에서는 표준 라이브러리에서 사용할 수 있는 일부 컬렉션 데이터 구조를 살펴보겠습니다.
