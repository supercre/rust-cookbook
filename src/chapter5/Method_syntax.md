# 메서드 문법

메서드는 함수와 유사하지만, 구조체(또는 열거형이나 트레이트 객체)의 컨텍스트 내에서 정의되고, 첫 번째 매개변수로 항상 `self`를 받는다는 점이 다릅니다. `self`는 메서드가 호출되는 인스턴스를 나타냅니다.

## 메서드 정의하기

`Rectangle` 구조체에 메서드를 추가하여 코드를 더 명확하게 만들어 보겠습니다. Listing 5-13에서 볼 수 있듯이, `area` 함수를 `Rectangle` 구조체의 메서드로 변경할 수 있습니다.

파일명: src/main.rs

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "직사각형의 면적은 {} 제곱 픽셀입니다.",
        rect1.area()
    );
}
```

Listing 5-13: `Rectangle` 구조체에 `area` 메서드 추가

`Rectangle` 구조체의 컨텍스트 내에서 함수를 정의하기 위해 `impl` (구현) 블록을 시작합니다. 이 블록 내의 모든 것이 `Rectangle` 타입과 연관됩니다. 그런 다음 `area` 함수를 메서드로 이동하고, 첫 번째 매개변수(이 경우에는 유일한 매개변수)를 `self`로 변경합니다. `area` 함수의 시그니처에서 `&Rectangle`을 사용한 것처럼, `self`는 `self: &Self`의 약어입니다. `impl` 블록 내에서 `Self` 타입은 `impl` 블록이 연관된 타입의 별칭입니다. 메서드는 `self`의 첫 번째 매개변수로 자동으로 가져오는 특별한 기능을 가지고 있습니다.

`main`에서는 `rect1`을 인수로 전달하는 대신 메서드 구문을 사용하여 `rect1`의 `area` 메서드를 호출합니다. 메서드 구문은 인스턴스 뒤에 점을 추가하고 그 뒤에 메서드 이름, 괄호, 그리고 인수들을 추가합니다.

`area` 함수에서 `&self`를 사용하는 이유는 메서드가 `Rectangle` 인스턴스를 빌림(borrow)하기 때문입니다. 메서드가 인스턴스를 소유하도록 하려면 `self`를 사용하고, 인스턴스를 변경 가능하게 빌리려면 `&mut self`를 사용할 수 있습니다. 단순히 `self`를 사용하는 것은 거의 없습니다. 이는 메서드가 인스턴스를 다른 것으로 변환하고 호출 후에 원래 인스턴스의 사용을 방지하려는 경우에 사용됩니다.

## 더 많은 매개변수가 있는 메서드

`Rectangle` 구조체에 두 번째 메서드를 연습해 보겠습니다. 이 메서드는 다른 `Rectangle` 인스턴스가 현재 인스턴스 내에 완전히 들어갈 수 있는지 확인합니다. Listing 5-14에서 볼 수 있듯이, `can_hold` 메서드를 추가할 수 있습니다.

파일명: src/main.rs

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };

    let rect3 = Rectangle {
        width: 60,
        height: 45,
    };

    println!("rect1은 rect2를 포함할 수 있나요? {}", rect1.can_hold(&rect2));
    println!("rect1은 rect3를 포함할 수 있나요? {}", rect1.can_hold(&rect3));
}
```

Listing 5-14: `Rectangle` 구조체에 `can_hold` 메서드 추가

메서드가 여러 매개변수를 가질 때는 첫 번째 매개변수로 `self`를 추가하고 그 뒤에 다른 매개변수들을 추가합니다. `can_hold` 메서드는 `self`의 너비와 높이가 다른 `Rectangle`의 너비와 높이보다 모두 큰지 확인합니다.

메서드 호출 시 `rect1.can_hold(&rect2)`와 같이 `&rect2`를 전달합니다. 이는 `rect2`의 참조를 `can_hold` 메서드에 전달하기 때문입니다. `can_hold` 메서드는 `rect1`의 참조를 `self`로 가져오고, `rect2`의 참조를 `other` 매개변수로 가져옵니다.

이제 이 프로그램을 `cargo run`으로 실행해보세요:

```
$ cargo run
   Compiling rectangles v0.1.0 (file:///projects/rectangles)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
     Running `target/debug/rectangles`
rect1은 rect2를 포함할 수 있나요? true
rect1은 rect3를 포함할 수 있나요? false
```

## 연관 함수

`impl` 블록 내에서 `self`를 매개변수로 사용하지 않는 함수를 정의할 수도 있습니다. 이러한 함수를 _연관 함수_라고 합니다. 이들은 구조체와 연관되어 있지만, 인스턴스에서 작동하지 않기 때문에 메서드가 아닙니다. 이미 사용한 `String::from`과 같은 함수가 연관 함수의 예입니다.

연관 함수는 종종 새 인스턴스를 반환하는 생성자로 사용됩니다. 예를 들어, 정사각형을 생성하는 연관 함수를 제공할 수 있습니다. 이 함수는 하나의 매개변수만 받아서 너비와 높이에 모두 사용합니다:

파일명: src/main.rs

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let sq = Rectangle::square(3);
}
```

이 연관 함수를 호출하려면 `let sq = Rectangle::square(3);`와 같이 구조체 이름 뒤에 `::` 구문을 사용합니다. 이 함수는 구조체의 네임스페이스에 속합니다. `::` 구문은 모듈과 타입의 네임스페이스에서 항목을 찾는 데 사용됩니다.

## 여러 impl 블록

각 구조체는 여러 개의 `impl` 블록을 가질 수 있습니다. 예를 들어, Listing 5-15는 Listing 5-13과 동일하지만 메서드들을 두 개의 `impl` 블록으로 나누어 있습니다.

파일명: src/main.rs

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };

    let rect3 = Rectangle {
        width: 60,
        height: 45,
    };

    println!("직사각형의 면적은 {} 제곱 픽셀입니다.", rect1.area());
    println!("rect1은 rect2를 포함할 수 있나요? {}", rect1.can_hold(&rect2));
    println!("rect1은 rect3를 포함할 수 있나요? {}", rect1.can_hold(&rect3));
}
```

Listing 5-15: 여러 `impl` 블록 사용

이 경우에는 메서드들을 여러 `impl` 블록으로 나눌 필요가 없지만, 이는 유효한 구문입니다. 10장에서 제네릭 타입과 트레이트를 다룰 때 여러 `impl` 블록이 유용한 경우를 볼 수 있습니다.
