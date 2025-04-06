# 구조체 정의하고 인스턴스 생성하기

구조체는 "튜플 타입" 섹션에서 설명한 튜플과 비슷합니다. 둘 다 여러 개의 관련된 값을 담고 있기 때문입니다. 튜플처럼 구조체의 구성 요소들은 서로 다른 타입이 될 수 있습니다. 하지만 튜플과 달리 구조체에서는 각 데이터 조각에 이름을 붙여서 값의 의미를 명확하게 합니다. 이러한 이름을 추가함으로써 구조체는 튜플보다 더 유연해집니다: 데이터의 순서에 의존하지 않고 인스턴스의 값을 지정하거나 접근할 수 있습니다.

구조체를 정의하려면 `struct` 키워드를 입력하고 구조체 전체의 이름을 지정합니다. 구조체의 이름은 함께 그룹화되는 데이터 조각들의 의미를 설명해야 합니다. 그런 다음 중괄호 안에 데이터 조각들의 이름과 타입을 정의하는데, 이를 _필드_라고 합니다. 예를 들어, Listing 5-1은 사용자 계정에 대한 정보를 저장하는 구조체를 보여줍니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {}
```

Listing 5-1: `User` 구조체 정의

구조체를 정의한 후에는 각 필드에 대한 구체적인 값을 지정하여 해당 구조체의 _인스턴스_를 생성합니다. 구조체의 이름을 말하고 그 다음에 _키: 값_ 쌍을 포함하는 중괄호를 추가하여 인스턴스를 생성합니다. 여기서 키는 필드의 이름이고 값은 해당 필드에 저장하려는 데이터입니다. 구조체에서 선언한 것과 동일한 순서로 필드를 지정할 필요는 없습니다. 다시 말해, 구조체 정의는 타입을 위한 일반적인 템플릿과 같고, 인스턴스는 해당 템플릿을 특정 데이터로 채워서 타입의 값을 생성합니다. 예를 들어, Listing 5-2와 같이 특정 사용자를 선언할 수 있습니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };
}
```

Listing 5-2: `User` 구조체의 인스턴스 생성

구조체에서 특정 값을 가져오려면 점 표기법을 사용합니다. 예를 들어, 이 사용자의 이메일 주소에 접근하려면 `user1.email`을 사용합니다. 인스턴스가 가변적이라면 점 표기법을 사용하여 특정 필드에 할당함으로써 값을 변경할 수 있습니다. Listing 5-3은 가변 `User` 인스턴스의 `email` 필드 값을 변경하는 방법을 보여줍니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let mut user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```

Listing 5-3: `User` 인스턴스의 `email` 필드 값 변경

전체 인스턴스가 가변적이어야 한다는 점에 유의하세요. Rust는 특정 필드만 가변적으로 표시하는 것을 허용하지 않습니다. 다른 표현식과 마찬가지로 함수 본문의 마지막 표현식으로 구조체의 새 인스턴스를 구성하여 암시적으로 해당 새 인스턴스를 반환할 수 있습니다.

Listing 5-4는 주어진 이메일과 사용자 이름으로 `User` 인스턴스를 반환하는 `build_user` 함수를 보여줍니다. `active` 필드는 `true` 값을 받고, `sign_in_count`는 `1` 값을 받습니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username,
        email,
        sign_in_count: 1,
    }
}

fn main() {
    let user1 = build_user(
        String::from("someone@example.com"),
        String::from("someusername123"),
    );
}
```

Listing 5-4: 이메일과 사용자 이름을 받아 `User` 인스턴스를 반환하는 `build_user` 함수

함수 매개변수의 이름을 구조체 필드와 동일하게 지정하는 것이 합리적이지만, `email`과 `username` 필드 이름과 변수를 반복해야 하는 것은 약간 지루합니다. 구조체에 더 많은 필드가 있다면, 각 이름을 반복하는 것이 더욱 성가실 것입니다. 다행히도 편리한 단축 구문이 있습니다!

### 필드 초기화 단축 구문 사용하기

Listing 5-4에서 매개변수 이름과 구조체 필드 이름이 정확히 동일하기 때문에, _필드 초기화 단축 구문_을 사용하여 `build_user`를 다시 작성할 수 있습니다. 이렇게 하면 `username`과 `email`의 반복 없이 동일하게 동작합니다. Listing 5-5에서 볼 수 있습니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username,
        email,
        sign_in_count: 1,
    }
}

fn main() {
    let user1 = build_user(
        String::from("someone@example.com"),
        String::from("someusername123"),
    );
}
```

Listing 5-5: `username`과 `email` 매개변수가 구조체 필드와 동일한 이름을 가지고 있어 필드 초기화 단축 구문을 사용하는 `build_user` 함수

여기서 우리는 `email`이라는 필드가 있는 `User` 구조체의 새 인스턴스를 생성합니다. `email` 필드의 값을 `build_user` 함수의 `email` 매개변수에 있는 값으로 설정하려고 합니다. `email` 필드와 `email` 매개변수가 동일한 이름을 가지고 있기 때문에, `email: email` 대신 `email`만 작성하면 됩니다.

### 구조체 업데이트 구문으로 다른 인스턴스에서 인스턴스 생성하기

다른 인스턴스의 대부분의 값을 포함하지만 일부를 변경하는 구조체의 새 인스턴스를 생성하는 것이 종종 유용합니다. _구조체 업데이트 구문_을 사용하여 이를 수행할 수 있습니다.

먼저, Listing 5-6에서는 업데이트 구문 없이 `user2`에 새 `User` 인스턴스를 일반적으로 생성하는 방법을 보여줍니다. `email`에 새 값을 설정하지만 그 외에는 Listing 5-2에서 생성한 `user1`의 동일한 값을 사용합니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    // --snip--

    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}
```

Listing 5-6: `user1`의 값을 하나를 제외한 모든 값을 사용하여 새 `User` 인스턴스 생성

구조체 업데이트 구문을 사용하면 더 적은 코드로 동일한 효과를 얻을 수 있습니다. Listing 5-7에서 볼 수 있습니다. `..` 구문은 명시적으로 설정되지 않은 나머지 필드가 주어진 인스턴스의 필드와 동일한 값을 가져야 함을 지정합니다.

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    // --snip--

    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

Listing 5-7: 구조체 업데이트 구문을 사용하여 `User` 인스턴스의 새 `email` 값을 설정하지만 나머지 값은 `user1`에서 사용

Listing 5-7의 코드는 `user2`에 `email`에 대해 다른 값을 가진 인스턴스를 생성하지만, `username`, `active`, `sign_in_count` 필드에 대해 `user1`의 동일한 값을 가집니다. `..user1`은 마지막에 와야 하며, 이는 나머지 필드가 `user1`의 해당 필드에서 값을 가져와야 함을 지정합니다. 하지만 구조체 정의에서 필드의 순서에 관계없이 원하는 만큼의 필드에 대해 값을 지정할 수 있습니다.

구조체 업데이트 구문은 할당처럼 `=`를 사용한다는 점에 유의하세요. 이는 "변수와 데이터가 이동과 상호작용" 섹션에서 본 것처럼 데이터를 이동시키기 때문입니다. 이 예제에서는 `user2`를 생성한 후에도 `user1`은 여전히 유효할 것입니다. `active`와 `sign_in_count`는 `Copy` 트레이트를 구현하는 타입이므로, "스택 전용 데이터: 복사" 섹션에서 논의한 동작이 적용됩니다. 이 예제에서는 `user1.email`을 여전히 사용할 수 있습니다. 그 값이 이동되지 않았기 때문입니다.

### 이름이 없는 필드로 튜플 구조체를 사용하여 다른 타입 생성하기

Rust는 튜플과 비슷해 보이는 구조체도 지원하며, 이를 _튜플 구조체_라고 합니다. 튜플 구조체는 구조체 이름이 제공하는 추가적인 의미는 있지만 필드와 연결된 이름은 없습니다. 대신 필드의 타입만 있습니다. 튜플 구조체는 전체 튜플에 이름을 부여하고 튜플을 다른 튜플과 다른 타입으로 만들고 싶을 때, 그리고 일반 구조체에서처럼 각 필드에 이름을 부여하는 것이 장황하거나 중복될 때 유용합니다.

튜플 구조체를 정의하려면 `struct` 키워드와 구조체 이름으로 시작한 다음 튜플의 타입을 지정합니다. 예를 들어, 여기서 `Color`와 `Point`라는 이름의 두 튜플 구조체를 정의하고 사용합니다:

파일명: src/main.rs

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

`black`과 `origin` 값이 서로 다른 타입이라는 점에 유의하세요. 이들은 서로 다른 튜플 구조체의 인스턴스이기 때문입니다. 정의하는 각 구조체는 자체 타입이며, 구조체 내의 필드가 동일한 타입을 가질 수 있더라도 마찬가지입니다. 예를 들어, `Color` 타입의 매개변수를 받는 함수는 `Point`를 인수로 받을 수 없습니다. 두 타입 모두 세 개의 `i32` 값으로 구성되어 있더라도 마찬가지입니다. 그 외에는 튜플 구조체 인스턴스가 튜플과 유사합니다. 개별 조각으로 구조를 분해할 수 있고, `.` 뒤에 인덱스를 사용하여 개별 값에 접근할 수 있습니다. 튜플과 달리 튜플 구조체는 구조를 분해할 때 구조체의 타입을 이름으로 지정해야 합니다. 예를 들어, `let Point(x, y, z) = point`와 같이 작성합니다.

### 필드가 없는 유닛 유사 구조체

필드가 전혀 없는 구조체도 정의할 수 있습니다! 이를 _유닛 유사 구조체_라고 합니다. 이는 "튜플 타입" 섹션에서 언급한 유닛 타입 `()`와 유사하게 동작하기 때문입니다. 유닛 유사 구조체는 어떤 타입에 대해 트레이트를 구현해야 하지만 타입 자체에 저장할 데이터가 없을 때 유용할 수 있습니다. 트레이트에 대해서는 10장에서 논의할 것입니다. 여기 `AlwaysEqual`이라는 이름의 유닛 구조체를 선언하고 인스턴스화하는 예가 있습니다:

파일명: src/main.rs

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

`AlwaysEqual`을 정의하려면 `struct` 키워드, 원하는 이름, 그리고 세미콜론을 사용합니다. 중괄호나 괄호가 필요하지 않습니다! 그런 다음 `subject` 변수에서 `AlwaysEqual`의 인스턴스를 얻을 수 있습니다. 정의한 이름을 사용하여 중괄호나 괄호 없이 유사한 방식으로 얻을 수 있습니다. 나중에 이 타입에 대한 동작을 구현하여 `AlwaysEqual`의 모든 인스턴스가 다른 타입의 모든 인스턴스와 항상 동일하다고 할 수 있을 것입니다. 아마도 테스트 목적으로 알려진 결과를 가지기 위함일 것입니다. 그 동작을 구현하기 위해 데이터가 필요하지 않을 것입니다! 10장에서 모든 타입(유닛 유사 구조체 포함)에 대해 트레이트를 정의하고 구현하는 방법을 볼 수 있습니다.

### 구조체 데이터의 소유권

Listing 5-1의 `User` 구조체 정의에서 `&str` 문자열 슬라이스 타입 대신 소유된 `String` 타입을 사용했습니다. 이는 의도적인 선택입니다. 이 구조체의 각 인스턴스가 모든 데이터를 소유하고 전체 구조체가 유효한 한 해당 데이터가 유효하기를 원하기 때문입니다.

구조체가 다른 것에 의해 소유된 데이터에 대한 참조를 저장하는 것도 가능하지만, 이를 위해서는 10장에서 논의할 Rust 기능인 _수명_을 사용해야 합니다. 수명은 구조체가 유효한 한 구조체가 참조하는 데이터가 유효함을 보장합니다. 수명을 지정하지 않고 구조체에 참조를 저장하려고 시도하면 다음과 같이 작동하지 않습니다:

파일명: src/main.rs

```rust
struct User {
    active: bool,
    username: &str,
    email: &str,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        active: true,
        username: "someusername123",
        email: "someone@example.com",
        sign_in_count: 1,
    };
}
```

컴파일러는 수명 지정자가 필요하다고 불평할 것입니다:

```console
$ cargo run
   Compiling structs v0.1.0 (file:///projects/structs)
error[E0106]: missing lifetime specifier
 --> src/main.rs:3:15
  |
3 |     username: &str,
  |               ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
  |
1 ~ struct User<'a> {
2 |     active: bool,
3 ~     username: &'a str,
  |

error[E0106]: missing lifetime specifier
 --> src/main.rs:4:12
  |
4 |     email: &str,
  |            ^ expected named lifetime parameter
  |
help: consider introducing a named lifetime parameter
  |
1 ~ struct User<'a> {
2 |     active: bool,
3 |     username: &str,
4 ~     email: &'a str,
  |

For more information about this error, try `rustc --explain E0106`.
error: could not compile `structs` (bin "structs") due to 2 previous errors

```

10장에서는 구조체에 참조를 저장할 수 있도록 이러한 오류를 수정하는 방법을 논의할 것입니다. 하지만 지금은 `&str`과 같은 참조 대신 `String`과 같은 소유된 타입을 사용하여 이러한 오류를 수정할 것입니다.
