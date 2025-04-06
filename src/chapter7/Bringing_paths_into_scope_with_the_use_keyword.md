# use 키워드로 경로를 스코프로 가져오기

함수를 호출하기 위해 경로를 작성하는 것은 불편하고 반복적으로 느껴질 수 있습니다. 목록 7-7에서, 우리가 `add_to_waitlist` 함수에 대한 절대 경로나 상대 경로를 선택했든 상관없이, `add_to_waitlist`를 호출할 때마다 `front_of_house`와 `hosting`도 지정해야 했습니다. 다행히도, 이 과정을 단순화하는 방법이 있습니다: `use` 키워드를 사용하여 경로에 대한 단축키를 한 번만 만들고, 그런 다음 스코프 내의 다른 곳에서는 더 짧은 이름을 사용할 수 있습니다.

목록 7-11에서, 우리는 `eat_at_restaurant` 함수의 스코프로 `crate::front_of_house::hosting` 모듈을 가져와서 `eat_at_restaurant`에서 `add_to_waitlist` 함수를 호출할 때 `hosting::add_to_waitlist`만 지정하면 되도록 합니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

목록 7-11: `use`로 모듈을 스코프로 가져오기

스코프에 `use`와 경로를 추가하는 것은 파일 시스템에서 심볼릭 링크를 만드는 것과 비슷합니다. 크레이트 루트에 `use crate::front_of_house::hosting`을 추가함으로써, `hosting`은 이제 해당 스코프에서 유효한 이름이 되며, 마치 `hosting` 모듈이 크레이트 루트에 정의된 것처럼 사용할 수 있습니다. `use`로 스코프로 가져온 경로는 다른 경로와 마찬가지로 비공개성을 확인합니다.

`use`는 `use`가 발생하는 특정 스코프에 대해서만 단축키를 생성한다는 점에 유의하세요. 목록 7-12는 `eat_at_restaurant` 함수를 `customer`라는 새로운 자식 모듈로 이동시키는데, 이는 `use` 문과 다른 스코프이므로 함수 본문은 컴파일되지 않습니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

mod customer {
    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist();
    }
}
```

목록 7-12: `use` 문은 그것이 있는 스코프에만 적용됨

컴파일러 오류는 `customer` 모듈 내에서 단축키가 더 이상 적용되지 않음을 보여줍니다:

```console
$ cargo build
   Compiling restaurant v0.1.0 (file:///projects/restaurant)
error[E0433]: failed to resolve: use of undeclared crate or module `hosting`
  --> src/lib.rs:11:9
   |
11 |         hosting::add_to_waitlist();
   |         ^^^^^^^ use of undeclared crate or module `hosting`
   |
help: consider importing this module through its public re-export
   |
10 +     use crate::hosting;
   |

warning: unused import: `crate::front_of_house::hosting`
 --> src/lib.rs:7:5
  |
7 | use crate::front_of_house::hosting;
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  |
  = note: `#[warn(unused_imports)]` on by default

For more information about this error, try `rustc --explain E0433`.
warning: `restaurant` (lib) generated 1 warning
error: could not compile `restaurant` (lib) due to 1 previous error; 1 warning emitted
```

`use`가 더 이상 스코프에서 사용되지 않는다는 경고도 있습니다! 이 문제를 해결하려면 `use`를 `customer` 모듈 내로도 이동하거나, 자식 `customer` 모듈 내에서 `super::hosting`으로 부모 모듈의 단축키를 참조하세요.

### 관용적인 use 경로 만들기

목록 7-11에서, 우리가 `use crate::front_of_house::hosting`을 지정하고 `eat_at_restaurant`에서 `hosting::add_to_waitlist`를 호출한 이유가 궁금했을 수 있습니다. 목록 7-13에서 보여주는 것처럼, 동일한 결과를 얻기 위해 `add_to_waitlist` 함수까지 전체 경로를 지정하지 않았습니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting::add_to_waitlist;

pub fn eat_at_restaurant() {
    add_to_waitlist();
}
```

목록 7-13: `use`로 `add_to_waitlist` 함수를 스코프로 가져오기 (관용적이지 않음)

목록 7-11과 목록 7-13 모두 동일한 작업을 수행하지만, 목록 7-11은 `use`로 함수를 스코프로 가져오는 관용적인 방법입니다. `use`로 함수의 부모 모듈을 스코프로 가져오면 함수를 호출할 때 부모 모듈을 지정해야 합니다. 함수를 호출할 때 부모 모듈을 지정하면 함수가 로컬에서 정의되지 않았음을 명확히 하면서도 전체 경로의 반복을 최소화합니다. 목록 7-13의 코드는 `add_to_waitlist`가 어디에 정의되어 있는지 불명확합니다.

반면에, 구조체, 열거형 및 기타 항목을 `use`로 가져올 때는 전체 경로를 지정하는 것이 관용적입니다. 목록 7-14는 표준 라이브러리의 `HashMap` 구조체를 바이너리 크레이트의 스코프로 가져오는 관용적인 방법을 보여줍니다.

파일명: src/main.rs

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert(1, 2);
}
```

목록 7-14: 관용적인 방식으로 `HashMap`을 스코프로 가져오기

이 관용구에는 특별한 이유가 없습니다: 단지 이렇게 Rust 코드를 읽고 작성하는 데 익숙해진 관례일 뿐입니다.

이 관용구의 예외는 `use` 문으로 동일한 이름을 가진 두 항목을 스코프로 가져올 때입니다. Rust는 이를 허용하지 않기 때문입니다. 목록 7-15는 동일한 이름을 가지고 있지만 다른 부모 모듈을 가진 두 `Result` 타입을 스코프로 가져오는 방법과 이를 참조하는 방법을 보여줍니다.

파일명: src/lib.rs

```rust
use std::fmt;
use std::io;

fn function1() -> fmt::Result {
    // --snip--
    Ok(())
}

fn function2() -> io::Result<()> {
    // --snip--
    Ok(())
}
```

목록 7-15: 동일한 이름을 가진 두 타입을 동일한 스코프로 가져오려면 부모 모듈을 사용해야 함

보시다시피, 부모 모듈을 사용하면 두 `Result` 타입을 구분할 수 있습니다. 대신 `use std::fmt::Result`와 `use std::io::Result`를 지정했다면, 동일한 스코프에 두 개의 `Result` 타입이 있게 되고, Rust는 `Result`를 사용할 때 우리가 어떤 것을 의미하는지 알 수 없게 됩니다.

### as 키워드로 새 이름 제공하기

`use`로 동일한 이름을 가진 두 타입을 동일한 스코프로 가져오는 문제에 대한 또 다른 해결책이 있습니다: 경로 뒤에 `as`와 타입에 대한 새로운 로컬 이름(별칭)을 지정할 수 있습니다. 목록 7-16은 `as`를 사용하여 두 `Result` 타입 중 하나의 이름을 변경하여 목록 7-15의 코드를 작성하는 또 다른 방법을 보여줍니다.

파일명: src/lib.rs

```rust
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
    Ok(())
}

fn function2() -> IoResult<()> {
    // --snip--
    Ok(())
}
```

목록 7-16: `as` 키워드를 사용하여 스코프로 가져올 때 타입 이름 변경하기

두 번째 `use` 문에서, 우리는 `std::io::Result` 타입에 대해 `IoResult`라는 새 이름을 선택했으며, 이는 우리가 스코프로 가져온 `std::fmt`의 `Result`와 충돌하지 않습니다. 목록 7-15와 목록 7-16은 모두 관용적인 것으로 간주되므로, 선택은 여러분에게 달려 있습니다!

### pub use로 이름 재내보내기

`use` 키워드로 이름을 스코프로 가져올 때, 새 스코프에서 사용 가능한 이름은 비공개입니다. 우리 코드를 호출하는 코드가 해당 이름을 마치 그 코드의 스코프에서 정의된 것처럼 참조할 수 있도록 하려면, `pub`과 `use`를 결합할 수 있습니다. 이 기술을 _재내보내기(re-exporting)_라고 합니다. 우리가 항목을 스코프로 가져오지만 다른 사람들이 자신의 스코프로 가져올 수 있도록 그 항목을 사용 가능하게 만들기 때문입니다.

목록 7-17은 목록 7-11의 코드에서 루트 모듈의 `use`를 `pub use`로 변경한 것을 보여줍니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

목록 7-17: `pub use`로 새 스코프에서 모든 코드가 사용할 수 있는 이름 만들기

이 변경 전에는, 외부 코드가 `add_to_waitlist` 함수를 호출하기 위해 `restaurant::front_of_house::hosting::add_to_waitlist()` 경로를 사용해야 했으며, 이는 `front_of_house` 모듈이 `pub`으로 표시되어야 함을 의미했습니다. 이제 이 `pub use`가 루트 모듈에서 `hosting` 모듈을 재내보냈으므로, 외부 코드는 대신 `restaurant::hosting::add_to_waitlist()` 경로를 사용할 수 있습니다.

재내보내기는 코드의 내부 구조가 프로그래머가 여러분의 코드를 호출할 때 도메인에 대해 생각하는 방식과 다를 때 유용합니다. 예를 들어, 이 레스토랑 비유에서, 레스토랑을 운영하는 사람들은 "프론트 오브 하우스"와 "백 오브 하우스"에 대해 생각합니다. 하지만 레스토랑을 방문하는 고객들은 아마도 그런 용어로 레스토랑의 부분에 대해 생각하지 않을 것입니다. `pub use`를 사용하면, 우리는 한 구조로 코드를 작성하지만 다른 구조를 노출시킬 수 있습니다. 이렇게 하면 라이브러리에서 작업하는 프로그래머와 라이브러리를 호출하는 프로그래머 모두를 위해 우리의 라이브러리가 잘 구성됩니다. 14장의 "pub use로 편리한 공개 API 내보내기" 섹션에서 `pub use`의 또 다른 예와 이것이 크레이트 문서에 미치는 영향에 대해 살펴볼 것입니다.

### 외부 패키지 사용하기

2장에서, 우리는 무작위 숫자를 얻기 위해 `rand`라는 외부 패키지를 사용하는 추측 게임 프로젝트를 프로그래밍했습니다. 프로젝트에서 `rand`를 사용하려면 _Cargo.toml_에 다음 줄을 추가했습니다:

파일명: Cargo.toml

```toml
rand = "0.8.5"
```

_Cargo.toml_에 `rand`를 의존성으로 추가하면 Cargo에게 crates.io에서 `rand` 패키지와 모든 의존성을 다운로드하고 우리 프로젝트에서 `rand`를 사용 가능하게 하라고 지시합니다.

그런 다음, 패키지의 스코프로 `rand` 정의를 가져오기 위해, 크레이트 이름인 `rand`로 시작하는 `use` 줄을 추가하고 스코프로 가져오고 싶은 항목을 나열했습니다. 2장의 "무작위 숫자 생성하기" 섹션에서, 우리는 `Rng` 트레이트를 스코프로 가져오고 `rand::thread_rng` 함수를 호출했습니다:

```rust
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

Rust 커뮤니티의 구성원들은 crates.io에서 많은 패키지를 사용 가능하게 만들었으며, 그 중 하나를 패키지로 가져오는 것은 동일한 단계를 포함합니다: 패키지의 _Cargo.toml_ 파일에 나열하고 `use`를 사용하여 크레이트의 항목을 스코프로 가져옵니다.

표준 `std` 라이브러리도 우리 패키지 외부의 크레이트라는 점에 유의하세요. 표준 라이브러리는 Rust 언어와 함께 제공되기 때문에, `std`를 포함하기 위해 _Cargo.toml_을 변경할 필요가 없습니다. 하지만 우리는 패키지의 스코프로 항목을 가져오기 위해 `use`를 참조해야 합니다. 예를 들어, `HashMap`을 사용할 때는 다음 줄을 사용합니다:

```rust
#![allow(unused)]
fn main() {
use std::collections::HashMap;
}
```

이는 표준 라이브러리 크레이트의 이름인 `std`로 시작하는 절대 경로입니다.

### 중첩된 경로를 사용하여 큰 use 목록 정리하기

같은 크레이트나 같은 모듈에서 정의된 여러 항목을 사용할 때, 각 항목을 자체 줄에 나열하면 파일에서 많은 수직 공간을 차지할 수 있습니다. 예를 들어, 목록 2-4에서 추측 게임에 있던 이 두 `use` 문은 `std`에서 항목을 스코프로 가져옵니다:

파일명: src/main.rs

```rust
use rand::Rng;
// --snip--
use std::cmp::Ordering;
use std::io;
// --snip--

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

대신, 중첩된 경로를 사용하여 한 줄에 동일한 항목을 스코프로 가져올 수 있습니다. 경로의 공통 부분을 지정한 다음, 두 개의 콜론, 그리고 경로의 다른 부분을 중괄호로 둘러싸서 이렇게 합니다. 목록 7-18에서 보여주는 것처럼요.

파일명: src/main.rs

```rust
use rand::Rng;
// --snip--
use std::{cmp::Ordering, io};
// --snip--

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse().expect("Please type a number!");

    println!("You guessed: {guess}");

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

목록 7-18: 중첩된 경로를 지정하여 동일한 접두사를 가진 여러 항목을 스코프로 가져오기

더 큰 프로그램에서는, 중첩된 경로를 사용하여 같은 크레이트나 모듈에서 많은 항목을 스코프로 가져오면 필요한 별도의 `use` 문의 수를 크게 줄일 수 있습니다!

경로의 모든 레벨에서 중첩된 경로를 사용할 수 있으며, 이는 공통 하위 경로를 공유하는 두 `use` 문을 결합할 때 유용합니다. 예를 들어, 목록 7-19는 `std::io`를 스코프로 가져오는 하나의 `use` 문과 `std::io::Write`를 스코프로 가져오는 다른 `use` 문을 보여줍니다.

파일명: src/lib.rs

```rust
use std::io;
use std::io::Write;
```

목록 7-19: 하나가 다른 것의 하위 경로인 두 `use` 문

이 두 경로의 공통 부분은 `std::io`이며, 이는 첫 번째 경로의 전체입니다. 이 두 경로를 하나의 `use` 문으로 병합하려면, 목록 7-20에서 보여주는 것처럼 중첩된 경로에서 `self`를 사용할 수 있습니다.

파일명: src/lib.rs

```rust
use std::io::{self, Write};
```

목록 7-20: 목록 7-19의 경로를 하나의 `use` 문으로 결합하기

이 줄은 `std::io`와 `std::io::Write`를 스코프로 가져옵니다.

### 글로브 연산자

경로에 정의된 _모든_ 공개 항목을 스코프로 가져오려면, 해당 경로 뒤에 `*` 글로브 연산자를 지정할 수 있습니다:

```rust
#![allow(unused)]
fn main() {
use std::collections::*;
}
```

이 `use` 문은 `std::collections`에 정의된 모든 공개 항목을 현재 스코프로 가져옵니다. 글로브 연산자를 사용할 때는 주의하세요! 글로브는 어떤 이름이 스코프에 있는지, 프로그램에서 사용된 이름이 어디에 정의되어 있는지 파악하기 어렵게 만들 수 있습니다.

글로브 연산자는 종종 테스트에서 `tests` 모듈 아래의 모든 것을 스코프로 가져오기 위해 사용됩니다. 이에 대해서는 11장의 "테스트 작성 방법" 섹션에서 설명할 것입니다. 글로브 연산자는 또한 프렐루드 패턴의 일부로도 사용됩니다. 이 패턴에 대한 자세한 내용은 표준 라이브러리 문서를 참조하세요.
