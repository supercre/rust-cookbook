## 제어 흐름

조건의 `참` 여부에 따라 일부 코드를 실행하고 조건이 참인 동안 일부 코드를 반복적으로 실행하는 기능은 대부분의 프로그래밍 언어에서 기본 구성 요소입니다. Rust 코드의 실행 흐름을 제어할 수 있는 가장 일반적인 구조는 `if` 표현식과 루프입니다.

### if 표현식

`if` 표현식을 사용하면 조건에 따라 코드를 분기할 수 있습니다. 조건을 제공한 다음 "이 조건이 충족되면 이 코드 블록을 실행합니다."라고 명시합니다. 조건이 충족되지 않으면 이 코드 블록을 실행하지 마십시오."라고 명시합니다.

_projects_ 디렉토리에 _branch_ 라는 새 프로젝트를 만들어 `if` 표현식을 살펴봅니다. src/main.rs 파일에 다음을 입력합니다:

```rust,editable
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

모든 `if` 표현식은 키워드 `if`로 시작하고 조건이 뒤따릅니다. 이 경우 조건은 변수 `number`의 값이 5보다 작은지 여부를 확인합니다. 조건이 참일 경우 실행할 코드 블록을 조건 바로 뒤에 중괄호 안에 배치합니다. 2장의 '추측과 비밀 번호 비교하기' 섹션에서 설명한 `match` 표현식의 암과 마찬가지로 `if` 표현식의 조건과 관련된 코드 블록을 암이라고 부르기도 합니다.

선택적으로, 조건이 거짓으로 평가될 경우 프로그램이 실행할 대체 코드 블록을 제공하기 위해 여기서 선택한 `else` 표현식을 포함할 수도 있습니다. `else` 표현식을 제공하지 않고 조건이 거짓인 경우 프로그램은 `if` 블록을 건너뛰고 다음 코드 블록으로 넘어갑니다.

이 코드를 실행해 보세요. 다음과 같은 출력이 표시됩니다:

```shell
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/branches`
condition was true
```

숫자 값을 조건을 거짓으로 만드는 값으로 변경하여 어떤 일이 발생하는지 확인해 보겠습니다:

```rust,editable
let number = 7;
```

프로그램을 다시 실행하고 출력을 확인합니다:

```shell
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/branches`
condition was false
```

이 코드의 조건은 _반드시_ `bool` 이어야 한다는 점도 주목할 필요가 있습니다. 조건이 `bool` 이 아닌 경우 오류가 발생합니다. 예를 들어 다음 코드를 실행해 보세요:

```rust,editable
fn main() {
    let number = 3;

    if number {
        println!("number was three");
    }
}
```

이번에는 `if` 조건이 `3`의 값으로 평가되고 Rust가 오류를 던집니다:

```shell
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if number {
  |        ^^^^^^ expected `bool`, found integer

For more information about this error, try `rustc --explain E0308`.
error: could not compile `branches` (bin "branches") due to 1 previous error
```

이 오류는 Rust가 `bool`을 기대했지만 정수를 가져왔음을 나타냅니다. Ruby 및 JavaScript와 같은 언어와 달리 Rust는 부울이 아닌 유형을 부울로 자동 변환하려고 시도하지 않습니다. 항상 부울을 조건으로 명시적으로 `if`를 제공해야 합니다. 예를 들어 숫자가 0이 아닌 경우에만 `if` 코드 블록이 실행되도록 하려면 `if` 표현식을 다음과 같이 변경하면 됩니다:

```rust,editable
fn main() {
    let number = 3;

    if number != 0 {
        println!("number was something other than zero");
    }
}
```

이 코드를 실행하면 `number was something other than zero.` 이라고 출력합니다.

#### else if를 사용하여 여러 조건 처리하기

다른 경우 표현식에서 `if`와 `else`를 결합하여 여러 조건을 사용할 수 있습니다. 예를 들어:

```rust,editable
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

이 프로그램은 네 가지 경로로 실행할 수 있습니다. 프로그램을 실행하면 다음과 같은 출력이 표시됩니다:

```shell
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/branches`
number is divisible by 3
```

이 프로그램이 실행되면 각 `if` 표현식을 차례로 검사하고 조건이 참으로 평가되는 첫 번째 본문을 실행합니다. 6을 2로 나눌 수 있지만 출력되는 숫자가 2로 나눌 수 있는 숫자가 아니며, `else` 블록에서 4, 3 또는 2로 나눌 수 없는 텍스트도 볼 수 없습니다. 이는 Rust가 첫 번째 참 조건에 대해서만 블록을 실행하고, 일단 하나를 찾으면 나머지는 확인하지 않기 때문입니다.

표현식을 너무 많이 사용하면 코드가 복잡해질 수 있으므로 표현식이 두 개 이상 있는 경우 코드를 리팩토링하는 것이 좋습니다. 6장에서는 이러한 경우에 사용할 수 있는 강력한 Rust 분기 구조인 `match`에 대해 설명합니다.

#### if 안에서 let 문 사용하기

`if`는 표현식이므로 목록 3-2에서와 같이 `let` 문의 오른쪽에 사용하여 결과를 변수에 할당할 수 있습니다.

```rust,editable
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {number}");
}
```

**목록 3-2: if 표현식의 결과를 변수에 할당하기**

`number` 변수는 `if` 표현식의 결과에 따라 값에 바인딩됩니다. 이 코드를 실행하여 어떤 일이 발생하는지 확인하세요:

```shell
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/branches`
The value of number is: 5
```

코드 블록은 그 안의 마지막 표현식까지 평가되며 숫자 자체도 표현식이라는 점을 기억하세요. 이 경우 전체 `if` 표현식의 값은 어떤 코드 블록이 실행되는지에 따라 달라집니다. 즉, if의 각 암에서 결과가 될 수 있는 값은 동일한 유형이어야 하며, 목록 3-2에서는 `if` 암과 `else` 암의 결과가 모두 `i32` 정수입니다. 다음 예제에서와 같이 유형이 일치하지 않으면 오류가 발생합니다:

```rust,editable
fn main() {
    let condition = true;

    let number = if condition { 5 } else { "six" };

    println!("The value of number is: {number}");
}
```

이 코드를 컴파일하려고 하면 오류가 발생합니다. `if` 및 `else` 암에는 호환되지 않는 값 유형이 있으며 Rust는 프로그램에서 문제를 찾을 수 있는 위치를 정확히 알려줍니다:

```shell
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
error[E0308]: `if` and `else` have incompatible types
 --> src/main.rs:4:44
  |
4 |     let number = if condition { 5 } else { "six" };
  |                                 -          ^^^^^ expected integer, found `&str`
  |                                 |
  |                                 expected because of this

For more information about this error, try `rustc --explain E0308`.
error: could not compile `branches` (bin "branches") due to 1 previous error
```

`if` 블록의 표현식은 정수로 평가되고 `else` 블록의 표현식은 문자열로 평가됩니다. 변수는 단일 타입을 가져야 하고 Rust는 컴파일 시 숫자 변수가 어떤 타입인지 확실히 알아야 하기 때문에 이 방식은 작동하지 않습니다. 숫자 유형을 알면 컴파일러는 `number`를 사용하는 모든 곳에서 해당 유형이 유효한지 확인할 수 있습니다. `number` 유형이 런타임에만 결정되는 경우 컴파일러는 더 복잡해지고 변수에 대한 여러 가상의 유형을 추적해야 하는 경우 코드에 대한 보증이 줄어들기 때문에 Rust는 이를 수행할 수 없습니다.

### 루프를 사용한 반복

코드 블록을 두 번 이상 실행하는 것이 유용한 경우가 많습니다. 이 작업을 위해 Rust는 루프 본문 내부의 코드를 끝까지 실행한 다음 바로 처음부터 다시 시작하는 여러 개의 루프를 제공합니다. 루프를 실험하기 위해 _loops_ 라는 새 프로젝트를 만들어 보겠습니다.

Rust에는 `loop`, `while`, `for`의 세 가지 루프가 있습니다. 각각을 사용해 보겠습니다.

#### 루프가 있는 코드 반복

`loop` 키워드는 사용자가 명시적으로 중지하라고 지시할 때까지 코드 블록을 영원히 또는 반복해서 실행하도록 Rust에 지시합니다.

루프의 디렉토리에 있는 src/main.rs 파일을 다음과 같이 변경합니다:

```rust,editable
fn main() {
    loop {
        println!("again!");
    }
}
```

이 프로그램을 실행하면 프로그램을 수동으로 중지할 때까지 계속 반복해서 "again!"가 출력됩니다. 대부분의 단말기는 키보드 단축키 `ctrl`-`c`를 지원하여 계속 반복되는 프로그램을 중단할 수 있습니다. 한 번 시도해 보세요:

```rust,editable
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29s
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```

기호 `^C`는 `ctrl`-`c`를 누른 위치를 나타냅니다. 인터럽트 신호를 받았을 때 코드가 루프에서 어디에 있었는지에 따라 `^C` 뒤에 `again!` 라는 단어가 표시될 수도 있고 표시되지 않을 수도 있습니다.

다행히도 Rust는 코드를 사용하여 루프에서 벗어날 수 있는 방법도 제공합니다. 루프 내에 `break` 키워드를 배치하여 루프 실행을 중단할 시기를 프로그램에 알려줄 수 있습니다. 2장의 "정답을 맞힌 후 종료하기" 섹션의 추측 게임에서 사용자가 정답을 맞혀 게임에서 이겼을 때 프로그램을 종료하기 위해 이 작업을 수행했음을 기억하세요.

또한 추측 게임에서 `continue`를 사용했는데, 이는 루프에서 루프 반복의 나머지 코드를 건너뛰고 다음 반복으로 이동하도록 프로그램에 지시합니다.

#### loop 에서 값 반환하기

루프의 용도 중 하나는 스레드가 작업을 완료했는지 확인하는 등 실패할 수 있는 연산을 다시 시도하는 것입니다. 또한 해당 작업의 결과를 루프에서 나머지 코드에 전달해야 할 수도 있습니다. 이렇게 하려면 여기에 표시된 것처럼 루프를 중지하는 데 사용하는 중단 표현식 뒤에 반환할 값을 추가하면 해당 값이 루프 밖으로 반환되어 사용할 수 있습니다:

```rust,editable
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}
```

루프 전에 `counter`라는 변수를 선언하고 0으로 초기화합니다. 그런 다음 결과라는 변수를 선언하여 루프에서 반환된 값을 보관합니다. 루프를 반복할 때마다 `counter` 변수에 1을 더한 다음 `counter`가 10과 같은지 확인합니다. 10이면 `counter * 2` 값과 함께 `break` 키워드를 사용합니다. 루프 뒤에는 세미콜론을 사용하여 결과값을 할당하는 문을 끝냅니다. 마지막으로 결과의 값(이 경우 `20`)을 출력합니다.

루프 내부에서 반환할 수도 있습니다. `break`는 현재 루프만 종료하는 반면, `return`은 항상 현재 함수를 종료합니다.

#### 여러 루프를 명확히 구분하는 루프 레이블

루프 안에 루프가 있는 경우 해당 지점에서 가장 안쪽 루프에 `break`와 `continue`를 적용합니다. 선택적으로 루프에 루프 레이블을 지정한 다음 `break`와 함께 사용하거나 해당 키워드가 가장 안쪽 루프 대신 레이블이 지정된 루프에 적용되도록 `continue`를 지정할 수 있습니다. 루프 레이블은 작은따옴표로 시작해야 합니다. 다음은 두 개의 중첩된 루프가 있는 예제입니다:

```rust,editable
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

외부 루프에는 `'counting_up`이라는 레이블이 있으며 0에서 2까지 카운트업됩니다. 레이블이 없는 내부 루프는 10에서 9까지 카운트다운합니다. 레이블을 지정하지 않는 첫 번째 `break`는 내부 루프만 종료합니다. `break 'counting_up;` 문은 외부 루프를 종료합니다. 이 코드가 출력됩니다:

```shell
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.58s
     Running `target/debug/loops`
count = 0
remaining = 10
remaining = 9
count = 1
remaining = 10
remaining = 9
count = 2
remaining = 10
End count = 2
```

#### while을 사용한 조건부 루프

프로그램은 종종 루프 내에서 조건을 평가해야 할 때가 있습니다. 조건이 참이면 루프가 실행됩니다. 조건이 참이 아니게 되면 프로그램은 `break`를 호출하여 루프를 중지합니다. `loop`, `if`, `else`, `break`를 조합하여 이와 같은 동작을 구현할 수 있으며, 원한다면 지금 프로그램에서 이를 시도해 볼 수 있습니다. 그러나 이 패턴은 매우 일반적이기 때문에 Rust에는 이를 위한 언어 구조인 `while` 루프가 내장되어 있습니다. 목록 3-3에서는 `while`을 사용하여 프로그램을 세 번 반복하고 매번 카운트다운한 다음, 루프가 끝나면 메시지를 출력하고 종료합니다.

```rust,editable
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

**목록 3-3: while 루프를 사용하여 조건이 참인 while 코드를 실행하기**

이 구조는 `loop`, `if`, `else`, `break`를 사용할 때 필요한 많은 중첩을 제거하며 더 명확합니다. 조건이 참으로 평가되는 동안에는 코드가 실행되고, 그렇지 않으면 루프가 종료됩니다.

#### for를 사용한 컬랙션 반복

배열과 같은 컬렉션의 요소를 반복하는 데 `while` 구문을 사용할 수도 있습니다. 예를 들어 목록 3-4의 루프는 배열 `a`의 각 요소를 출력합니다.

```rust,editable
fn main() {
    let a = [10, 20, 30, 40, 50];
    let mut index = 0;

    while index < 5 {
        println!("the value is: {}", a[index]);

        index += 1;
    }
}
```

**목록 3-4: for 루프를 사용하여 컬렉션의 각 요소 반복하기**

여기서 코드는 배열의 요소를 통해 카운트업합니다. 인덱스 0에서 시작하여 배열의 최종 인덱스에 도달할 때까지(즉, `index < 5`가 더 이상 참이 아닐 때) 반복합니다. 이 코드를 실행하면 배열의 모든 요소가 인쇄됩니다:

```shell
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.32s
     Running `target/debug/loops`
the value is: 10
the value is: 20
the value is: 30
the value is: 40
the value is: 50
```

예상대로 다섯 개의 배열 값이 모두 터미널에 나타납니다. 인덱스가 어느 시점에서 5 값에 도달하더라도 배열에서 여섯 번째 값을 가져오기 전에 루프가 실행을 중지합니다.

그러나 이 접근 방식은 오류가 발생하기 쉬우므로 인덱스 값이나 테스트 조건이 잘못되면 프로그램이 패닉 상태에 빠질 수 있습니다. 예를 들어, 요소가 4개인 배열의 정의를 변경했지만 인덱스가 4 미만인 동안으로 조건을 업데이트하는 것을 잊어버린 경우 코드가 패닉에 빠질 수 있습니다. 또한 컴파일러가 루프를 반복할 때마다 인덱스가 배열의 범위 내에 있는지 조건부 검사를 수행하기 위해 런타임 코드를 추가하기 때문에 속도가 느려집니다.

보다 간결한 대안으로 `for` 루프를 사용하여 컬렉션의 각 항목에 대해 몇 가지 코드를 실행할 수 있습니다. `for` 루프는 목록 3-5의 코드와 비슷합니다.

```rust,editable
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```
**목록 3-5: for 루프를 사용하여 컬렉션의 각 요소 반복하기**

이 코드를 실행하면 목록 3-4와 동일한 출력을 볼 수 있습니다. 더 중요한 것은 이제 코드의 안전성이 향상되어 배열의 끝을 넘어가거나 충분히 멀리 가지 않아 일부 항목이 누락되는 버그가 발생할 가능성이 없어졌다는 점입니다.

`for` 루프를 사용하면 목록 3-4에서 사용한 방법과 마찬가지로 배열의 값 수를 변경하는 경우 다른 코드를 변경하는 것을 기억할 필요가 없습니다.

`for` 루프는 안전성과 간결성 때문에 Rust에서 가장 일반적으로 사용되는 루프 구조입니다. 목록 3-3의 `while` 루프를 사용한 카운트다운 예제에서처럼 특정 코드를 특정 횟수만큼 실행하려는 상황에서도 대부분의 Rust 사용자는 `for` 루프를 사용합니다. 이를 수행하는 방법은 표준 라이브러리에서 제공하는 `Range`를 사용하여 한 숫자에서 시작하여 다른 숫자보다 먼저 끝나는 순서대로 모든 숫자를 생성하는 것입니다.

다음은 범위 반전을 위해 `for` 루프와 아직 설명하지 않은 다른 방법인 `rev`를 사용한 카운트다운의 모습입니다:

```rust,editable
fn main() {
    for number in (1..4).rev() {
        println!("{number}!");
    }
    println!("LIFTOFF!!!");
}
```

이 코드가 좀 더 멋지지 않나요?

### 정리

해냈습니다! 변수, 스칼라 및 복합 데이터 유형, 함수, 주석, if 표현식, 루프에 대해 배웠습니다! 이 장에서 설명한 개념을 연습해 보려면 다음 작업을 수행하는 프로그램을 만들어 보세요:

* 화씨와 섭씨 사이의 온도 변환하기
* n번째 피보나치 수 생성하기
* 노래의 반복을 활용하여 크리스마스 캐롤 '크리스마스의 열두 날'의 가사 출력하기

이제 다른 프로그래밍 언어에는 일반적으로 존재하지 않는 Rust의 개념인 소유권에 대해 이야기해 보겠습니다.