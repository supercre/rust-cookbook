## 참조와 빌리기(References and Borrowing)

Listing 4-5의 튜플 코드에서의 문제는 `String`이 `calculate_length` 함수로 이동되었기 때문에, 함수 호출 후 `String`을 계속해서 사용하려면 `String`을 호출 함수로 다시 반환해야 한다는 점입니다.

대신, `String` 값에 대한 **참조**를 제공할 수 있습니다. 참조는 포인터처럼 주소를 따라가서 그 주소에 저장된 데이터에 접근할 수 있게 해줍니다. 데이터는 다른 변수의 소유이며, 포인터와 달리 참조는 해당 참조가 유효한 동안특정 타입의 유요한 값을 가리키도록 보장합니다.

다음 예제에선 값의 소유권을 가져가지 않고 참조를 매개변수로받는 `calculate_length` 함수를 정의하고 사용하는 방법을 소개합니다.

**파일명: src/main.rs**

```rust
fn main() {
  let s1 = String::from("hello");
  let len = calculate_length(&s1);
  println!("The length of '{s1}' is {len}.");
}
fn calculate_length(s: &String) -> usize {
    s.len()
}
```

먼저, 변수 선언과 함수 반환 값에 있던 모든 튜플 코드가 사라졌음을 알 수 있습니다. 다음으로, 우리는 `calculate_length`에 `&s1`을 전달하고, 함수 정의에서는 `String` 대신 `&String`을 받습니다.

이 앰퍼샌드(`&`)는 **참조**를 나타내며, 이는 값을 소유하지 않고 그 값을 참조할 수 있게 해줍니다.

> Note: `&`를 사용한 참조의 반대는 역참조(dereferencing)이며, 이는 역참조 연산자 (`*`)를 사용하여 수행됩니다.
> 역참조 연산자의 사용 예시는 8장에서 다룰 예정이며, 자세한 내용은 15장에서 다룰 예정입니다.

여기서 함수 호출을 좀 더 자세히 살펴보겠습니다.

```rust
fn main() {
  let s1 = String::from("hello");
  let len = calculate_length(&s1);
  println!("The length of '{s1}' is {len}.");
}
fn calculate_length(s: &String) -> usize {
  s.len()
}
```

`&s1`구문은 `s1`의 값을 **참조**하는 참조를 생성하지만, 그것을 소유하진 않습니다. 소유하지 않기 때문에, 참조가 더 이상 사용되지 않으면 참조가 가리키는 값은 드롭되지 않습니다.

마찬가지로, 함수의 시그니처에서 사용하는 `&`는 매개변수 `s`의 타입이 참조임을 나타냅니다.

```rust
fn main() {
  let s1 = String::from("hello");
  let len = calculate_length(&s1);
  println!("The length of '{s1}' is {len}.");
}
fn calculate_length(s: &String) -> usize { // 변수 s는 String 참조입니다.
  s.len()
} // 여기서 s는 범위를 벗어나지만, s가 참조하는 값에 대한 소유권이 없기 때문에
  // s는 드롭되지 않습니다.
```

변수 `s`가 유효한 범위는 함수 매개변수의 범위와 동일하지만, 참조가 가리키는 값은 `s`가 더 이상 사용되지 않더라도 드롭되지 않습니다.

왜냐하면, `s`는 그 값에 대한 소유권을 가지고 있지 않기 때문입니다. 함수가 실제 값 대신 참조를 매개변수로 받을 때, 처음부터 소유권을 가지지 않았기 때문에 우리는 값을 반환하여 소유권을 돌려줄 필요가 없습니다.

우리는 참조를 만드는 행동을 **빌리기(borrowing)**라고 부르기로 하였습니다. 우리는 누군가가 소유한 것을 빌릴 수 있으며, 우리는 이것을 소유하지 않았기 때문에 다 사용한 후에는 돌려줘야합니다.

그렇다면, 우리가 빌려온 것을 수정하려고 하면 어떻게 될까요? Listing 4-6의 코드를 시도해보기 바랍니다.

**파일 명: src/main.rs**

```rust
fn main() {
    let s = String::from("hello");
    change(&s);
}
fn change(some_string: &String) {
    some_string.push_str(", world");
}
```

**Listing 4-6**: 빌린 값을 수정하려고 시도하기

코드 실행 시 발생하는 에러는 다음과 같습니다.

```console
$ cargo run
  Compiling ownership v0.1.0 (file:///projects/ownership)
  error[E0596]: cannot borrow `*some_string` as mutable, as it is behind a `&` reference
--> src/main.rs:8:5
  |
8 |     some_string.push_str(", world");
  |     ^^^^^^^^^^^ `some_string` is a `&` reference, so the data it refers to cannot be borrowed as mutable
  |
help: consider changing this to be a mutable reference
  |
7 | fn change(some_string: &mut String) {
  |                         +++
For more information about this error, try `rustc --explain E0596`.
error: could not compile `ownership` (bin "ownership") due to 1 previous error
```

변수는 기본적으로 불변인 것처럼, 참조도 기본적으로 불변입니다. 우리는 참조를 통해 가리키는 값을 수정할 수 없습니다.

### 가변 참조

우리는 Listing 4-6의 코드를 수정하여 빌린 값을 수정할 수 있도록 변경해볼 것입니다. 다음과 같이 몇 가지 수정을 통해 가변 참조를 사용할 수 있습니다.

**파일 명: src/main.rs**

```rust
fn main() {
    let mut s = String::from("hello");
    change(&mut s);
}
fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

먼저, `s`를 `mut`으로 변경합니다. 그런 다음 `&mut s`로 가변 참조를 생성하고, `change`함수에서 가변 참조를 받을 수 있도록 함수 시그니처를 `some_string: &mut String`으로 수정하여 `change` 함수가 빌린 값을 수정할 수 있도록 합니다.

가변 참조에는 하나의 큰 제한이 있습니다. 값에 대한 가변 참조가 존재할 경우 그 값에 대한 다른 참조는 **존재할 수 없습니다.**

다음 예제 코드는 `s`에 대해 두 개의 가변 참조를 생성하려고 시도하는 코드이며, 이 코드는 동작에 실패할 것입니다.

**파일 명: src/main.rs**

```rust
fn main() {
    let mut s = String::from("hello");
    let r1 = &mut s;
    let r2 = &mut s;
    println!("{}, {}", r1, r2);
}
```

실행 결과는 다음과 같습니다.

```console
$ cargo run
  Compiling ownership v0.1.0 (file:///projects/ownership)
  error[E0499]: cannot borrow `s` as mutable more than once at a time
  --> src/main.rs:5:14
    |
  4 |     let r1 = &mut s;
    |              ------ first mutable borrow occurs here
  5 |     let r2 = &mut s;
    |              ^^^^^^ second mutable borrow occurs here
  6 |
  7 |     println!("{}, {}", r1, r2);
    |                        -- first borrow later used here
  For more information about this error, try `rustc --explain E0499`.
  error: could not compile `ownership` (bin "ownership") due to 1 previous error
```

이 코드와 실행 결과로부터 우리는 `s`를 한번에 가변적으로 두 번 빌릴 수 없다는 것을 알 수 있습니다.

첫번째 가변 참조는 `r1`에 있으며, `println!`에서 사용될 때까지 계속 유지되어야만 합니다. 그런데, `r1`을 사용하는 동안, 우리는 `r1`과 동일한 데이터를 빌리고자 하는 두 번째 가변 참조 `r2`를 만들려고 했습니다.

같은 데이터를 동시에 여러 번 가변 참조하는 것을 방지하는 제한은 데이터를 수정할 수 있게 하면서도 매우 통제된 방식으로 이루어집니다.

이는 대부분의 언어가 언제든지 수정할 수 있도록 허용하는 것과 달리, Rust에서는 이를 허용하지 않으므로, 처음 Rust를 접하는 프로그래머가 이것을 어렵게 느낄 수 있습니다.

이러한 단점과 달리 Rust는 컴파일 단계에서 데이터 경쟁(data race)을 방지할 수 있다는 장점이 존재합니다. 데이터경쟁은 레이스 조건과 비슷하며, 다음 세 가지 행동이 발생할 때 일어납니다.

1. 두 개 이상의 포인터가 동일한 데이터에 동시 접근할 경우
2. 그 중 적어도 하나의 포인터가 데이터를 수정하려고 할 때
3. 데이터 접근을 동기화하는 메커니즘이 사용되지 않는다.

데이터 경쟁은 정의되지 않은 동작을 일으키며, 런타임에서 이를 추적하고 수정하기 어려울 수 있습니다. Rust는 데이터 경쟁이 있는 코드를 컴파일하지 않음으로써 이 문제를 방지합니다.

항상 그렇듯이, 중괄호를 사용하여 새로운 범위를 만들면 여러 가변 참조를 사용할 수 있습니다. **단, 동시에 사용할 수는 없습니다.**

```rust
fn main() {
    let mut s = String::from("hello");
    {
        let r1 = &mut s;
    } // 여기서 r1은 범위를 벗어나게 되므로, 문제 없이 새로운 참조 r2를 만들 수 있습니다.
    let r2 = &mut s;
}
```

Rust는 가변 참조와 불변 참조를 동시에 사용하는 것을 방지하는 규칙을 적용합니다.

아래의 코드를 통해 이를 알아봅시다.

```rust
fn main() {
    let mut s = String::from("hello");
    let r1 = &s; // 문제 없음
    let r2 = &s; // 문제 없음
    let r3 = &mut s; // 문제 발생 지점
    println!("{}, {}, and {}", r1, r2, r3);
}
```

코드의 실행 결과는 다음과 같습니다.

```console
$ cargo run
  Compiling ownership v0.1.0 (file:///projects/ownership)
  error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
  --> src/main.rs:6:14
    |
  4 |     let r1 = &s; // no problem
    |              -- immutable borrow occurs here
  5 |     let r2 = &s; // no problem
  6 |     let r3 = &mut s; // BIG PROBLEM
    |              ^^^^^^ mutable borrow occurs here
  7 |
  8 |     println!("{}, {}, and {}", r1, r2, r3);
    |                                -- immutable borrow later used here
  For more information about this error, try `rustc --explain E0502`.
  error: could not compile `ownership` (bin "ownership") due to 1 previous error
```

우리는 동일한 값에 대해 불변 참조가 존재할 경우 가변 참조를 가질 수 없습니다.

불변 참조를 사용하는 사람들은 값이 갑자기 변경되는 것을 예상하지 않습니다. 그러나 여러 개의 불변 참조는 허용됩니다. 왜냐하면 데이터를 단지 읽기만 할 경우 다른 데이터 읽기에 영향을 미치지 않기 때문입니다.

참조의 범위는 참조가 도입되는 지점부터 그 참조가 마지막으로 사용되는 지점까지 이어집니다.

예를 들어, 아래 코드는 정상적으로 실행되는데, 불변 참조의 마지막 사용인 `println!` 코드가 가변 참조가 도입되기 전 사용되기 때문입니다.

```rust
fn main() {
    let mut s = String::from("hello");
    let r1 = &s; // 문제 발생 X
    let r2 = &s; /// 문제 발생 X
    println!("{r1} and {r2}");
    // 변수 r1과 r2는 이 지점 이후로 사용되지 않습니다.
    let r3 = &mut s; // 문제 발생 X
    println!("{r3}");
}
```

불변 참조 `r1`과 `r2`의 범위는 마지막으로 사용된 `println!` 이후 끝나며, 이는 가변 참조 `r3`가 생성되기 전에 발생합니다.

이 범위들이 겹치지 않기 때문에 이 코드는 허용되며, 컴파일러는 참조가 더 이상 사용되지 않는 지점을 명확히 확인할 수 있으므로 문제가 발생하지 않습니다.

빌리기에 대한 오류가 때때로 실망스러울 순 있지만, 이는 Rust 컴파일러가 잠재적인 버그를 일찍(런타임이아닌 컴파일 단계에서 차단) 찾아내어 문제를 정확히 어디에서 발생하는지 알려주는 것입니다. 그렇게 되면 데이터가 예상한 대로 되지 않은 이유를 추적할 필요가 없어집니다.

### 댕글링 참조(Dangling References)

포인터가 있는 언어에서는 메모리를 해제하면서 해당 메모리를 가리키는 포인터를 그대로 두어 댕글링 포인터(다른 곳에 할당된 메모리 위치를 참조하는 포인터)를 실수로 만들기 쉽습니다. 반면 Rust에서는 컴파일러가 참조가 댕글링 참조가 되지 않도록 보장합니다. 즉, 어떤 데이터에 대한 참조가 있을 때, 컴파일러는 그 데이터가 참조보다 먼저 범위를 벗어나지 않도록 보장합니다.

아래 예제에서 댕글링 참조를 만들려고 시도해 보겠습니다. Rust가 어떻게 이를 컴파일 타임 오류로 방지하는지 확인해 봅시다.

**파일 명: src/main.rs**

```rust
fn main() {
    let reference_to_nothing = dangle();
}
fn dangle() -> &String {
    let s = String::from("hello");
    &s
}
```

코드 실행 결과는 다음과 같습니다.

```console
$ cargo run
  Compiling ownership v0.1.0 (file:///projects/ownership)
  error[E0106]: missing lifetime specifier
  --> src/main.rs:5:16
    |
  5 | fn dangle() -> &String {
    |                ^ expected named lifetime parameter
    |
    = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
  help: consider using the `'static` lifetime, but this is uncommon unless you're returning a borrowed value from a `const` or a `static`
    |
  5 | fn dangle() -> &'static String {
    |                 ++++++++
  help: instead, you are more likely to want to return an owned value
    |
  5 - fn dangle() -> &String {
  5 + fn dangle() -> String {
    |
  error[E0515]: cannot return reference to local variable `s`
  --> src/main.rs:8:5
    |
  8 |     &s
    |     ^^ returns a reference to data owned by the current function
  Some errors have detailed explanations: E0106, E0515.
  For more information about an error, try `rustc --explain E0106`.
  error: could not compile `ownership` (bin "ownership") due to 2 previous errors
```

이 오류 메세지는 우리가 아직 다루지 않은 기능인 **라이프타임(lifetimes)** 기능과 관련이 있습니다. 라이프타임에 대해선 10장에서 자세히 다룰 예정입니다.

> 이 함수의 반환 타입은 빌려온 값을 포함하고 있지만, 빌려올 값이 없습니다.

`dangle` 코드의 각 단계에서 정확히 무슨 일이 일어나는지 자세히 살펴봅시다.

**파일 명: src/main.rs**

```rust
fn main() {
    let reference_to_nothing = dangle();
}
fn dangle() -> &String { // dangle함수는 string에 대한 참조를 반환합니다.
    let s = String::from("hello"); // s는 새로운 string 타입의 변수입니다.
    &s // 우리는 String인 s에 대한 참조를 반환합니다.
} // 여기서 s는 범위를 벗어나며 드롭됩니다. 그 메모리는 사라집니다.
```

`s`는 `dangle` 함수 안에서 생성되기 때문에, `dangle` 함수의 코드가 끝나면 s는 메모리에서 해제됩니다.

하지만 우리는 그에 대한 참조를 반환하려고 했습니다. 
이는 이 참조가 유효하지 않은 `String`을 가리키게 된다는 의미입니다. 
Rust는 이를 허용하지 않습니다.

여기서 해결책은 `String`을 직접 반환하는 것입니다.

```rust
fn main() {
    let string = no_dangle();
}
fn no_dangle() -> String {
    let s = String::from("hello");
    s
}
```

위 코드는 정상 작동합니다. 소유권이 이동하고, 아무 것도 해제되지 않습니다.

### 참조의 규칙(The Rules of References)

우리가 참조에 대해 논의한 내용을 요약해 보겠습니다.

- 어떤 시점에서든, 하나의 가변 참조 또는 여러 개의 불변 참조만 가질 수 있습니다.
- 참조는 항상 유효해야 합니다.

다음으로, 다른 종류의 참조인 슬라이스를 살펴보겠습니다.