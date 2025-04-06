# 구조체를 사용하는 예제 프로그램

구조체를 사용하는 예제 프로그램을 작성해보겠습니다. 직사각형의 면적을 계산하는 프로그램을 만들어보겠습니다. 이 프로그램은 픽셀 단위로 직사각형의 너비와 높이를 지정하고, 면적을 계산합니다.

먼저 `src/main.rs` 파일을 열고 다음과 같이 코드를 작성해보세요:

```rust
fn main() {
    let width1 = 30;
    let height1 = 50;

    println!(
        "직사각형의 면적은 {} 제곱 픽셀입니다.",
        area(width1, height1)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}
```

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
직사각형의 면적은 1500 제곱 픽셀입니다.
```

이 코드는 `area` 함수를 사용하여 직사각형의 면적을 계산하고 있으며, 각 매개변수는 관련이 있지만, 코드 어디에서도 이들이 서로 연관되어 있다는 것을 표현하지 않고 있습니다. 너비와 높이를 함께 그룹화하는 것이 더 명확하고 관리하기 쉬울 것 같습니다.

이미 3장에서 튜플을 사용하여 이 문제를 해결하는 방법을 배웠습니다. 튜플을 사용하면 다음과 같이 코드를 작성할 수 있습니다:

```rust
fn main() {
    let rect1 = (30, 50);

    println!(
        "직사각형의 면적은 {} 제곱 픽셀입니다.",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```

이 버전에서는 튜플을 사용하여 너비와 높이를 함께 그룹화했습니다. 이는 코드가 더 명확해졌지만, 튜플은 값의 의미를 전달하지 않는다는 단점이 있습니다. 코드를 읽는 사람은 튜플의 인덱스가 무엇을 의미하는지 기억해야 합니다. 또한 이 코드에서는 값들이 서로 연관되어 있다는 것을 명확하게 표현하지 못하고 있습니다.

구조체를 사용하면 이 문제를 해결할 수 있습니다. 구조체를 사용하면 데이터에 의미 있는 레이블을 부여할 수 있습니다. 코드를 다음과 같이 수정해보세요:

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "직사각형의 면적은 {} 제곱 픽셀입니다.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

여기서는 `Rectangle`이라는 구조체를 정의했습니다. 구조체 내부에서는 `width`와 `height` 필드를 정의했습니다. 이 필드들은 모두 `u32` 타입입니다. 그런 다음 `main` 함수에서 `Rectangle` 구조체의 인스턴스를 생성했습니다. 이 인스턴스는 `width`가 30이고 `height`가 50입니다.

`area` 함수는 이제 `Rectangle` 구조체의 인스턴스를 매개변수로 받습니다. 이 함수는 구조체의 `width`와 `height` 필드를 사용하여 면적을 계산합니다. 이렇게 하면 코드가 더 명확해지고, 값들이 서로 연관되어 있다는 것을 명확하게 표현할 수 있습니다.

구조체를 사용하면 코드가 더 명확해지고, 값들의 의미를 더 잘 전달할 수 있습니다. 또한 구조체를 사용하면 코드를 더 모듈화하고 재사용하기 쉽게 만들 수 있습니다.

### 유용한 파생 트레이트

이제 구조체를 사용하여 직사각형의 면적을 계산하는 프로그램을 작성했습니다. 하지만 직사각형의 면적을 계산하는 것 외에도, 직사각형의 크기를 디버깅하는 것이 유용할 수 있습니다. 예를 들어, 직사각형의 크기를 출력하여 디버깅할 수 있습니다. 하지만 `println!` 매크로를 사용하여 직사각형의 크기를 출력하려고 하면 컴파일 오류가 발생합니다. `Rectangle` 구조체는 `Display` 트레이트를 구현하지 않기 때문입니다.

`println!` 매크로는 `Display` 트레이트를 사용하여 출력을 포맷팅합니다. 이 트레이트는 `{}` 플레이스홀더를 사용하여 출력을 포맷팅합니다. 하지만 `Rectangle` 구조체는 `Display` 트레이트를 구현하지 않기 때문에, `println!` 매크로를 사용하여 직사각형의 크기를 출력할 수 없습니다.

`println!` 매크로를 사용하여 직사각형의 크기를 출력하려면 `Display` 트레이트를 구현해야 합니다. 하지만 `Display` 트레이트를 구현하는 것은 복잡할 수 있습니다. 대신, `Debug` 트레이트를 사용할 수 있습니다. `Debug` 트레이트는 `{:?}` 플레이스홀더를 사용하여 출력을 포맷팅합니다. `Debug` 트레이트를 구현하려면 `#[derive(Debug)]` 속성을 구조체 정의 위에 추가하면 됩니다.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1은 {:?}입니다.", rect1);
}
```

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
rect1은 Rectangle { width: 30, height: 50 }입니다.
```

`Debug` 트레이트를 사용하면 직사각형의 크기를 출력할 수 있습니다. 하지만 출력 형식이 예쁘지 않습니다. 더 예쁘게 출력하려면 `{:#?}` 플레이스홀더를 사용할 수 있습니다.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1은 {:#?}입니다.", rect1);
}
```

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
rect1은 Rectangle {
    width: 30,
    height: 50,
}입니다.
```

`{:#?}` 플레이스홀더를 사용하면 직사각형의 크기가 더 예쁘게 출력됩니다.

`Debug` 트레이트 외에도, `Rectangle` 구조체에 유용한 다른 트레이트를 파생시킬 수 있습니다. 예를 들어, `PartialEq` 트레이트를 파생시켜 직사각형을 비교할 수 있습니다. `PartialEq` 트레이트를 파생시키려면 `#[derive(PartialEq)]` 속성을 구조체 정의 위에 추가하면 됩니다.

```rust
#[derive(Debug, PartialEq)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    let rect2 = Rectangle {
        width: 30,
        height: 50,
    };

    let rect3 = Rectangle {
        width: 10,
        height: 40,
    };

    println!("rect1 == rect2: {}", rect1 == rect2);
    println!("rect1 == rect3: {}", rect1 == rect3);
}
```

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
rect1 == rect2: true
rect1 == rect3: false
```

`PartialEq` 트레이트를 파생시키면 직사각형을 비교할 수 있습니다. `rect1`과 `rect2`는 같은 크기를 가지고 있기 때문에 `true`를 출력하고, `rect1`과 `rect3`는 다른 크기를 가지고 있기 때문에 `false`를 출력합니다.

`Debug`와 `PartialEq` 트레이트 외에도, `Rectangle` 구조체에 유용한 다른 트레이트를 파생시킬 수 있습니다. 예를 들어, `Clone`과 `Copy` 트레이트를 파생시켜 직사각형을 복사할 수 있습니다. `Clone`과 `Copy` 트레이트를 파생시키려면 `#[derive(Clone, Copy)]` 속성을 구조체 정의 위에 추가하면 됩니다.

```rust
#[derive(Debug, PartialEq, Clone, Copy)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    let rect2 = rect1;

    println!("rect1은 {:?}입니다.", rect1);
    println!("rect2는 {:?}입니다.", rect2);
}
```

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
rect1은 Rectangle { width: 30, height: 50 }입니다.
rect2는 Rectangle { width: 30, height: 50 }입니다.
```

`Clone`과 `Copy` 트레이트를 파생시키면 직사각형을 복사할 수 있습니다. `rect1`을 `rect2`에 복사한 후에도 `rect1`을 사용할 수 있습니다. 이는 `Copy` 트레이트를 파생시켰기 때문입니다. `Copy` 트레이트를 파생시키지 않으면, `rect1`을 `rect2`에 복사한 후에는 `rect1`을 사용할 수 없습니다. 이는 `Clone` 트레이트만 파생시켰기 때문입니다.

`Debug`, `PartialEq`, `Clone`, `Copy` 트레이트 외에도, `Rectangle` 구조체에 유용한 다른 트레이트를 파생시킬 수 있습니다. 예를 들어, `Default` 트레이트를 파생시켜 직사각형의 기본값을 설정할 수 있습니다. `Default` 트레이트를 파생시키려면 `#[derive(Default)]` 속성을 구조체 정의 위에 추가하면 됩니다.

```rust
#[derive(Debug, PartialEq, Clone, Copy, Default)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle::default();

    println!("rect1은 {:?}입니다.", rect1);
}
```

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
rect1은 Rectangle { width: 0, height: 0 }입니다.
```

`Default` 트레이트를 파생시키면 직사각형의 기본값을 설정할 수 있습니다. `Rectangle::default()`를 호출하면 `width`와 `height`가 모두 `0`인 직사각형이 생성됩니다.

이제 `Rectangle` 구조체에 유용한 트레이트를 파생시켜 직사각형을 디버깅하고, 비교하고, 복사하고, 기본값을 설정할 수 있습니다. 이러한 트레이트를 파생시키면 코드가 더 유용해집니다.

### 결론

이 장에서는 구조체를 사용하여 관련된 데이터를 그룹화하는 방법을 배웠습니다. 구조체를 사용하면 코드가 더 명확해지고, 값들의 의미를 더 잘 전달할 수 있습니다. 또한 구조체를 사용하면 코드를 더 모듈화하고 재사용하기 쉽게 만들 수 있습니다.

구조체는 Rust에서 데이터를 구조화하는 강력한 도구입니다. 구조체를 사용하면 데이터의 의미를 명확하게 표현할 수 있고, 코드를 더 읽기 쉽게 만들 수 있습니다. 또한 구조체를 사용하면 코드를 더 모듈화하고 재사용하기 쉽게 만들 수 있습니다.

다음 장에서는 열거형과 패턴 매칭에 대해 배울 것입니다. 열거형은 구조체와 마찬가지로 여러 값을 그룹화하는 방법이지만, 열거형은 여러 가능한 값 중 하나를 가질 수 있습니다. 패턴 매칭은 열거형의 값을 처리하는 강력한 도구입니다.
