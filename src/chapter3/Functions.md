## 함수

함수는 Rust 코드에서 널리 사용됩니다. 이미 이 언어에서 가장 중요한 함수 중 하나인 많은 프로그램의 시작점인 `main` 함수에 대해 살펴보았습니다. 또한 새로운 함수를 선언할 수 있는 `fn` 키워드도 보셨을 것입니다.

Rust 코드는 함수 및 변수 이름에 모든 문자가 소문자이고 개별 단어에 밑줄을 긋는 _snake case_ 를 사용합니다. 아래는 함수 정의 예제가 포함된 프로그램입니다.

```rust,editable
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

Rust에서 함수를 정의하려면 `fn` 다음에 함수 이름과 괄호 세트를 입력하면 됩니다. 중괄호는 컴파일러에게 함수 본문의 시작과 끝을 알려줍니다.

정의한 함수는 이름 뒤에 괄호 세트를 입력하여 호출할 수 있습니다. `another_function`은 프로그램에 정의되어 있으므로 `main` 함수 내부에서 호출할 수 있습니다. 소스 코드에서 `main` 함수 _뒤에_ `another_function`을 정의했지만, 그 전에 정의할 수도 있었습니다. Rust는 함수를 어디에 정의하든 상관하지 않으며, 호출자가 볼 수 있는 범위의 어딘가에 정의되어 있는지만 확인합니다.

함수를 더 자세히 살펴보기 위해 _functions_ 이라는 이름의 새 바이너리 프로젝트를 시작하겠습니다. src/main.rs에 `another_function` 예제를 넣고 실행합니다. 다음과 같은 출력이 표시됩니다:

```shell
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.28s
     Running `target/debug/functions`
Hello, world!
Another function.
```

라인은 `main` 함수에 나타나는 순서대로 실행됩니다. 먼저 "Hello, world!" 메시지가 인쇄된 다음 `another_function`이 호출되고 해당 메시지가 인쇄됩니다.

### 매개변수

함수의 시그니처의 일부인 특수 변수인 _매개변수_ 를 갖도록 함수를 정의할 수 있습니다. 함수에 매개변수가 있는 경우 해당 매개변수에 대한 구체적인 값을 제공할 수 있습니다. 기술적으로는 구체적인 값을 _인자_ 라고 하지만, 일상적인 대화에서 사람들은 함수의 정의에 있는 변수나 함수를 호출할 때 전달되는 구체적인 값에 대해 매개변수와 인자를 혼용해서 사용하는 경향이 있습니다.

이 `another_function` 버전에서는 매개변수를 추가합니다:
```rust,editable
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {x}");
}
```

이 프로그램을 실행하면 다음과 같은 출력이 표시됩니다:

```shell
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 1.21s
     Running `target/debug/functions`
The value of x is: 5
```

`another_function`의 선언에는 `x`라는 매개변수가 하나 있으며, `x`의 유형은 `i32`로 지정됩니다. `another_function`에 `5`를 전달하면 `println!` 매크로는 `x`가 포함된 중괄호 쌍이 형식 문자열에 있던 곳에 `5`를 넣습니다.

함수 서명에서는 각 매개변수의 유형을 선언해야 합니다. 함수 정의에 타입 어노테이션이 필요하다는 것은 컴파일러가 코드의 다른 곳에서 사용자가 의미하는 타입을 파악하기 위해 어노테이션을 사용할 필요가 거의 없다는 것을 의미합니다. 또한 컴파일러는 함수가 예상하는 유형을 알면 더 유용한 오류 메시지를 표시할 수 있습니다.

여러 개의 매개변수를 정의할 때는 다음과 같이 쉼표로 매개변수 선언을 구분합니다:

```rust,editable
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {value}{unit_label}");
}
```

이 예에서는 두 개의 매개 변수가 있는 `print_labeled_measurement`라는 함수를 만듭니다. 첫 번째 매개변수의 이름은 `value`이고 유형은 `i32`입니다. 두 번째 매개변수의 이름은 `unit_label`이고 유형은 `char`입니다. 그러면 함수는 값과 `unit_label`을 모두 포함하는 텍스트를 출력합니다.

이 코드를 실행해 보겠습니다. 현재 함수 프로젝트의 src/main.rs 파일에 있는 프로그램을 앞의 예제로 바꾸고 cargo run을 사용하여 실행합니다:

```shell
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/functions`
The measurement is: 5h
```
`value`의 값으로 `5`를, `unit_label`의 값으로 `'h'`를 사용하여 함수를 호출했으므로 프로그램 출력에 해당 값이 포함됩니다.

### 문(Statement)과 표현식(Expression)

함수 본문은 선택적으로 표현식으로 끝나는 일련의 문으로 구성됩니다. 지금까지 살펴본 함수에는 종료 표현식이 포함되지 않았지만 표현식이 문의 일부인 것을 보았습니다. Rust는 표현식 기반 언어이므로 이는 이해해야 할 중요한 차이점입니다. 다른 언어에는 동일한 구분이 없으므로 문과 표현식이 무엇인지, 그리고 그 차이가 함수 본문에 어떤 영향을 미치는지 살펴봅시다.

* 문은 어떤 작업을 수행하고 값을 반환하지 않는 명령어입니다.
* 표현식은 결과 값으로 평가합니다. 몇 가지 예를 살펴보겠습니다.

사실 우리는 이미 문과 표현식을 사용했습니다. `let` 키워드를 사용하여 변수를 생성하고 값을 할당하는 것은 문입니다. 목록 3-1에서 `let y = 6;` 은 문입니다.

```rust,editable
fn main() {
    let y = 6;
}
```

**목록 3-1: 한 개의 문을 가진 main 함수 선언**

함수 정의도 문이며, 앞의 예제 전체가 그 자체로 문입니다. (아래에서 살펴보겠지만 함수 호출은 문이 아닙니다.)

문은 값을 반환하지 않습니다. 따라서 다음 코드에서 시도하는 것처럼 `let` 문을 다른 변수에 할당할 수 없으며 오류가 발생합니다:

```rust,editable
fn main() {
    let x = (let y = 6);
}
```
이 프로그램을 실행하면 다음과 같은 오류가 표시됩니다:
```shell
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error: expected expression, found `let` statement
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^
  |
  = note: only supported directly in conditions of `if` and `while` expressions

warning: unnecessary parentheses around assigned value
 --> src/main.rs:2:13
  |
2 |     let x = (let y = 6);
  |             ^         ^
  |
  = note: `#[warn(unused_parens)]` on by default
help: remove these parentheses
  |
2 -     let x = (let y = 6);
2 +     let x = let y = 6;
  |

warning: `functions` (bin "functions") generated 1 warning
error: could not compile `functions` (bin "functions") due to 1 previous error; 1 warning emitted

```

`let y = 6` 문은 값을 반환하지 않으므로 `x`가 바인딩할 대상이 없습니다. 이는 대입이 대입의 값을 반환하는 C나 Ruby와 같은 다른 언어의 경우와 다릅니다. 이러한 언어에서는 `x = y = 6`을 작성하여 `x`와 `y` 모두 `6`이라는 값을 가질 수 있지만, Rust에서는 그렇지 않습니다.

표현식은 값으로 평가되며 Rust에서 작성하게 될 나머지 코드의 대부분을 구성합니다. `5 + 6`과 같이 `11`이라는 값으로 평가되는 표현식인 수학 연산을 생각해 봅시다. 표현식은 문의 일부가 될 수 있습니다. 목록 3-1에서 `let y = 6;` 문의 `6`은 `6`이라는 값으로 평가되는 표현식입니다. 함수 호출은 표현식입니다. 매크로를 호출하는 것도 표현식입니다. 예를 들어 중괄호로 묶은 새 범위 블록은 표현식입니다:

```rust,editable
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {y}");
}
```
이 표현식
```rust,editable
{
    let x = 3;
    x + 1
}
```
은 이 경우 `4`로 평가되는 블록입니다. 이 값은 `let` 문의 일부로 `y`에 바인딩됩니다. `x + 1` 줄에는 지금까지 본 대부분의 줄과 달리 끝에 세미콜론이 없다는 점에 유의하세요. 표현식에는 끝 세미콜론이 포함되지 않습니다. 표현식 끝에 세미콜론을 추가하면 표현식이 문으로 바뀌고 값을 반환하지 않습니다. 다음에서 함수 반환 값과 표현식을 살펴볼 때 이 점을 염두에 두세요.

### 반환 값이 있는 함수

함수는 함수를 호출하는 코드에 값을 반환할 수 있습니다. 반환 값의 이름은 지정하지 않지만 화살표(`->`) 뒤에 반환 값의 유형을 선언해야 합니다. Rust에서 함수의 반환 값은 함수 본문의 블록에 있는 마지막 표현식의 값과 동의어입니다. 반환 키워드를 사용하고 값을 지정하여 함수에서 조기에 반환할 수 있지만 대부분의 함수는 암시적으로 마지막 표현식을 반환합니다. 다음은 값을 반환하는 함수의 예시입니다:

```rust,editable
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {x}");
}
```

`five` 함수에는 함수 호출, 매크로, 심지어 `let` 문도 없습니다. 이는 Rust에서 완벽하게 유효한 함수입니다. 함수의 반환 유형도 `-> i32`로 지정되어 있다는 점에 유의하세요. 이 코드를 실행해 보세요. 출력은 다음과 같을 것입니다:

```rust,editable
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/functions`
The value of x is: 5
```

`five`함수에서 `5`는 함수의 반환 값이므로 반환 유형이 `i32`인 이유입니다. 이를 좀 더 자세히 살펴보겠습니다. 먼저, `let x = five();` 줄은 함수의 반환값을 사용하여 변수를 초기화한다는 것을 보여줍니다. 함수 `five`는 `5`를 반환하므로 이 줄은 다음과 동일합니다:
```rust,editable
let x = 5;
```

`five` 함수는 매개 변수가 없고 반환 값의 유형을 정의하지만, 반환하려는 값이 있는 표현식이기 때문에 함수 본문은 세미콜론이 없는 외로운 5로 되어 있습니다.

다른 예를 살펴보겠습니다:

```rust,editable
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {x}");
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

이 코드를 실행하면 이와 같이 출력됩니다: `The value of x is: 6` 그러나 `x + 1`이 포함된 줄 끝에 세미콜론을 배치하여 표현식에서 문으로 변경하면 오류가 발생합니다:

```rust,editable
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {x}");
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}
```

이 코드를 컴파일하면 다음과 같은 오류가 발생합니다:

```shell
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error[E0308]: mismatched types
 --> src/main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: remove this semicolon to return this value

For more information about this error, try `rustc --explain E0308`.
error: could not compile `functions` (bin "functions") due to 1 previous error
```

주요 오류 메시지인 `mismatched types`은 이 코드의 핵심 문제를 드러냅니다. 함수 `plus_one`의 정의에는 `i32`를 반환한다고 나와 있지만 문은 단위 유형인 `()`로 표현되는 값으로 평가되지 않습니다. 따라서 아무 것도 반환되지 않으므로 함수 정의와 모순되어 오류가 발생합니다. 이 출력에서 Rust는 이 문제를 해결하는 데 도움이 될 수 있는 메시지를 제공합니다. 세미콜론을 제거하면 오류가 해결될 수 있다는 제안입니다.
