# 모듈 트리에서 항목을 참조하기 위한 경로

Rust에게 모듈 트리에서 항목을 찾을 위치를 알려주기 위해, 우리는 파일시스템을 탐색할 때 경로를 사용하는 것과 같은 방식으로 경로를 사용합니다. 함수를 호출하려면 그 경로를 알아야 합니다.

경로는 두 가지 형태를 취할 수 있습니다:

* _절대 경로(absolute path)_는 크레이트 루트에서 시작하는 전체 경로입니다; 외부 크레이트의 코드의 경우 절대 경로는 크레이트 이름으로 시작하고, 현재 크레이트의 코드의 경우 `crate`라는 리터럴로 시작합니다.
* _상대 경로(relative path)_는 현재 모듈에서 시작하여 `self`, `super`, 또는 현재 모듈의 식별자를 사용합니다.

절대 경로와 상대 경로 모두 이중 콜론(`::`)으로 구분된 하나 이상의 식별자가 뒤따라옵니다.

목록 7-1로 돌아가서, `add_to_waitlist` 함수를 호출하고 싶다고 가정해 보겠습니다. 이는 `add_to_waitlist` 함수의 경로가 무엇인지 묻는 것과 같습니다. 목록 7-3은 일부 모듈과 함수가 제거된 목록 7-1을 포함합니다.

크레이트 루트에 정의된 새로운 함수 `eat_at_restaurant`에서 `add_to_waitlist` 함수를 호출하는 두 가지 방법을 보여드리겠습니다. 이 경로들은 올바르지만, 이 예제가 그대로 컴파일되는 것을 막는 또 다른 문제가 남아있습니다. 잠시 후에 그 이유를 설명하겠습니다.

`eat_at_restaurant` 함수는 우리 라이브러리 크레이트의 공개 API의 일부이므로 `pub` 키워드로 표시합니다. "pub 키워드로 경로 노출하기" 섹션에서 `pub`에 대해 더 자세히 설명할 것입니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // 절대 경로
    crate::front_of_house::hosting::add_to_waitlist();

    // 상대 경로
    front_of_house::hosting::add_to_waitlist();
}
```

목록 7-3: 절대 경로와 상대 경로를 사용하여 `add_to_waitlist` 함수 호출하기

`eat_at_restaurant`에서 `add_to_waitlist` 함수를 처음 호출할 때, 우리는 절대 경로를 사용합니다. `add_to_waitlist` 함수는 `eat_at_restaurant`와 같은 크레이트에 정의되어 있으며, 이는 우리가 절대 경로를 시작하기 위해 `crate` 키워드를 사용할 수 있다는 것을 의미합니다. 그런 다음 `add_to_waitlist`에 도달할 때까지 각각의 연속적인 모듈을 포함합니다. 같은 구조를 가진 파일시스템을 상상할 수 있습니다: `add_to_waitlist` 프로그램을 실행하기 위해 `/front_of_house/hosting/add_to_waitlist` 경로를 지정할 것입니다; 크레이트 루트에서 시작하기 위해 `crate` 이름을 사용하는 것은 셸에서 파일시스템 루트에서 시작하기 위해 `/`를 사용하는 것과 같습니다.

`eat_at_restaurant`에서 `add_to_waitlist`를 두 번째로 호출할 때, 우리는 상대 경로를 사용합니다. 경로는 `eat_at_restaurant`와 모듈 트리의 같은 레벨에 정의된 모듈의 이름인 `front_of_house`로 시작합니다. 여기서 파일시스템에 해당하는 것은 `front_of_house/hosting/add_to_waitlist` 경로를 사용하는 것입니다. 모듈 이름으로 시작한다는 것은 경로가 상대적이라는 것을 의미합니다.

상대 경로를 사용할지 절대 경로를 사용할지는 프로젝트에 따라 결정하는 것이며, 항목 정의 코드를 항목을 사용하는 코드와 함께 또는 별도로 이동할 가능성이 더 높은지에 따라 달라집니다. 예를 들어, `front_of_house` 모듈과 `eat_at_restaurant` 함수를 `customer_experience`라는 이름의 모듈로 이동했다면, `add_to_waitlist`에 대한 절대 경로를 업데이트해야 하지만, 상대 경로는 여전히 유효할 것입니다. 그러나 `eat_at_restaurant` 함수를 별도로 `dining`이라는 이름의 모듈로 이동했다면, `add_to_waitlist` 호출에 대한 절대 경로는 그대로 유지되지만, 상대 경로를 업데이트해야 합니다. 일반적으로 우리는 코드 정의와 항목 호출을 서로 독립적으로 이동할 가능성이 더 높기 때문에 절대 경로를 지정하는 것을 선호합니다.

목록 7-3을 컴파일해보고 아직 컴파일되지 않는 이유를 알아보겠습니다! 우리가 받는 오류는 목록 7-4에 나와 있습니다.

```console
$ cargo build
   Compiling restaurant v0.1.0 (file:///projects/restaurant)
error[E0603]: module `hosting` is private
 --> src/lib.rs:9:28
  |
9 |     crate::front_of_house::hosting::add_to_waitlist();
  |                            ^^^^^^^  --------------- function `add_to_waitlist` is not publicly re-exported
  |                            |
  |                            private module
  |
note: the module `hosting` is defined here
 --> src/lib.rs:2:5
  |
2 |     mod hosting {
  |     ^^^^^^^^^^^

error[E0603]: module `hosting` is private
  --> src/lib.rs:12:21
   |
12 |     front_of_house::hosting::add_to_waitlist();
  |                     ^^^^^^^  --------------- function `add_to_waitlist` is not publicly re-exported
  |                     |
  |                     private module
  |
note: the module `hosting` is defined here
  --> src/lib.rs:2:5
  |
2 |     mod hosting {
  |     ^^^^^^^^^^^

For more information about this error, try `rustc --explain E0603`.
error: could not compile `restaurant` (lib) due to 2 previous errors
```

목록 7-4: 목록 7-3의 코드를 빌드할 때 발생하는 컴파일러 오류

오류 메시지는 `hosting` 모듈이 비공개라고 말합니다. 다시 말해, 우리는 `hosting` 모듈과 `add_to_waitlist` 함수에 대한 올바른 경로를 가지고 있지만, Rust는 비공개 섹션에 접근할 수 없기 때문에 우리가 그것들을 사용할 수 없게 합니다. Rust에서 모든 항목(함수, 메서드, 구조체, 열거형, 모듈, 상수)은 기본적으로 부모 모듈에 대해 비공개입니다. 함수나 구조체와 같은 항목을 비공개로 만들려면 그것을 모듈에 넣으세요.

부모 모듈의 항목들은 자식 모듈 내부의 비공개 항목들을 사용할 수 없지만, 자식 모듈의 항목들은 그들의 조상 모듈의 항목들을 사용할 수 있습니다. 이는 자식 모듈이 그들이 정의된 맥락을 볼 수 있기 때문입니다. 우리의 비유를 계속하자면, 비공개성 규칙을 레스토랑의 백오피스와 같다고 생각하세요: 거기서 일어나는 일은 레스토랑 고객에게는 비공개이지만, 오피스 관리자는 그들이 운영하는 레스토랑의 모든 것을 보고 할 수 있습니다.

Rust는 내부 구현 세부사항을 숨기는 것이 기본이 되도록 모듈 시스템이 작동하도록 선택했습니다. 그렇게 하면 외부 코드를 깨뜨리지 않고 내부 코드의 어떤 부분을 변경할 수 있는지 알 수 있습니다. 그러나 Rust는 `pub` 키워드를 사용하여 항목을 공개함으로써 자식 모듈의 코드의 내부 부분을 외부 조상 모듈에 노출시키는 옵션을 제공합니다.

## pub 키워드로 경로 노출하기

목록 7-4에서 `hosting` 모듈이 비공개라고 말한 오류로 돌아가 보겠습니다. 우리는 부모 모듈의 `eat_at_restaurant` 함수가 자식 모듈의 `add_to_waitlist` 함수에 접근할 수 있기를 원하므로, 목록 7-5에서 보여주는 것처럼 `hosting` 모듈을 `pub` 키워드로 표시합니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // 절대 경로
    crate::front_of_house::hosting::add_to_waitlist();

    // 상대 경로
    front_of_house::hosting::add_to_waitlist();
}
```

목록 7-5: `hosting` 모듈을 `pub`으로 선언하여 `eat_at_restaurant`에서 사용하기

불행히도, 목록 7-5의 코드는 여전히 목록 7-6에 나와 있는 것처럼 컴파일러 오류를 발생시킵니다.

```console
$ cargo build
   Compiling restaurant v0.1.0 (file:///projects/restaurant)
error[E0603]: function `add_to_waitlist` is private
 --> src/lib.rs:9:37
  |
9 |     crate::front_of_house::hosting::add_to_waitlist();
  |                                     ^^^^^^^^^^^^^^^ private function
  |
note: the function `add_to_waitlist` is defined here
 --> src/lib.rs:3:9
  |
3 |         fn add_to_waitlist() {}
  |         ^^^^^^^^^^^^^^^^^^^^

error[E0603]: function `add_to_waitlist` is private
  --> src/lib.rs:12:30
   |
12 |     front_of_house::hosting::add_to_waitlist();
  |                              ^^^^^^^^^^^^^^^ private function
  |
note: the function `add_to_waitlist` is defined here
  --> src/lib.rs:3:9
  |
3 |         fn add_to_waitlist() {}
  |         ^^^^^^^^^^^^^^^^^^^^

For more information about this error, try `rustc --explain E0603`.
error: could not compile `restaurant` (lib) due to 2 previous errors
```

목록 7-6: `hosting` 모듈을 `pub`으로 선언한 후에도 발생하는 컴파일러 오류

오류는 `add_to_waitlist` 함수가 비공개라고 말합니다. 모듈을 `pub`으로 표시하는 것은 모듈의 코드를 외부에서 접근할 수 있게 하지만, 모듈의 내용은 여전히 비공개입니다. 모듈의 내용을 공개하려면 `pub` 키워드를 각 항목 앞에 추가해야 합니다.

목록 7-7에서 `add_to_waitlist` 함수 앞에 `pub` 키워드를 추가하여 이 문제를 해결합니다.

파일명: src/lib.rs

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // 절대 경로
    crate::front_of_house::hosting::add_to_waitlist();

    // 상대 경로
    front_of_house::hosting::add_to_waitlist();
}
```

목록 7-7: `add_to_waitlist` 함수를 `pub`으로 선언하여 `eat_at_restaurant`에서 사용하기

이제 코드가 컴파일됩니다! 경로와 접근성 규칙을 살펴보겠습니다:

1. `pub` 키워드를 사용하여 모듈을 공개하면, 다른 코드가 해당 모듈을 참조할 수 있습니다.
2. 모듈 내의 항목을 공개하려면 항목 앞에 `pub` 키워드를 추가해야 합니다.
3. 공개 모듈 내의 항목에 접근하려면 해당 항목도 공개되어야 합니다.

절대 경로에서는 `crate`(우리 크레이트의 모듈 트리의 루트)로 시작합니다. `front_of_house` 모듈은 크레이트 루트에 정의되어 있습니다. `front_of_house`가 공개되지 않았지만, `eat_at_restaurant` 함수가 `front_of_house`와 같은 모듈에 정의되어 있기 때문에(즉, `eat_at_restaurant`와 `front_of_house`는 형제 관계입니다), `eat_at_restaurant`에서 `front_of_house`를 참조할 수 있습니다. 다음은 `pub`으로 표시된 `hosting` 모듈입니다. 우리는 `hosting`의 부모 모듈에 접근할 수 있으므로 `hosting`에 접근할 수 있습니다. 마지막으로, `add_to_waitlist` 함수는 `pub`으로 표시되어 있고 우리는 그 부모 모듈에 접근할 수 있으므로, 이 함수 호출이 작동합니다!

상대 경로에서는 첫 번째 단계를 제외하고 절대 경로와 동일한 논리가 적용됩니다: 크레이트 루트에서 시작하는 대신, 경로는 `front_of_house`에서 시작합니다. `front_of_house` 모듈은 `eat_at_restaurant`와 같은 모듈 내에 정의되어 있으므로, `eat_at_restaurant`가 정의된 모듈에서 시작하는 상대 경로가 작동합니다. 그런 다음, `hosting`과 `add_to_waitlist`가 `pub`으로 표시되어 있으므로 경로의 나머지 부분이 작동하고, 이 함수 호출이 유효합니다!

라이브러리 크레이트를 공유하여 다른 프로젝트가 귀하의 코드를 사용할 수 있도록 계획한다면, 공개 API는 크레이트 사용자와의 계약으로, 그들이 귀하의 코드와 어떻게 상호작용할 수 있는지 결정합니다. 사람들이 귀하의 크레이트에 의존하기 쉽게 하기 위해 공개 API의 변경을 관리하는 데 있어 많은 고려사항이 있습니다. 이러한 고려사항은 이 책의 범위를 벗어납니다; 이 주제에 관심이 있으시면 The Rust API Guidelines를 참조하세요.

#### 바이너리와 라이브러리를 가진 패키지에 대한 모범 사례

우리는 패키지가 _src/main.rs_ 바이너리 크레이트 루트와 _src/lib.rs_ 라이브러리 크레이트 루트를 모두 포함할 수 있으며, 두 크레이트 모두 기본적으로 패키지 이름을 가진다고 언급했습니다. 일반적으로 라이브러리와 바이너리 크레이트를 모두 포함하는 이 패턴의 패키지는 라이브러리 크레이트 내의 코드를 호출하는 실행 파일을 시작하기에 충분한 코드만 바이너리 크레이트에 가집니다. 이는 라이브러리 크레이트의 코드가 공유될 수 있기 때문에 다른 프로젝트가 패키지가 제공하는 대부분의 기능을 활용할 수 있게 합니다.

모듈 트리는 _src/lib.rs_에 정의되어야 합니다. 그런 다음, 패키지 이름으로 경로를 시작하여 바이너리 크레이트에서 공개 항목을 사용할 수 있습니다. 바이너리 크레이트는 완전히 외부 크레이트가 라이브러리 크레이트를 사용하는 것과 같은 방식으로 라이브러리 크레이트의 사용자가 됩니다: 공개 API만 사용할 수 있습니다. 이는 좋은 API를 설계하는 데 도움이 됩니다; 당신은 작성자일 뿐만 아니라 클라이언트이기도 합니다!

12장에서는 바이너리 크레이트와 라이브러리 크레이트를 모두 포함하는 명령줄 프로그램으로 이 구성 관행을 보여줄 것입니다.

### super로 상대 경로 시작하기

경로의 시작 부분에 `super`를 사용하여 현재 모듈이나 크레이트 루트가 아닌 부모 모듈에서 시작하는 상대 경로를 구성할 수 있습니다. 이는 파일시스템 경로를 `..` 구문으로 시작하는 것과 같습니다. `super`를 사용하면 부모 모듈에 있는 항목을 참조할 수 있으며, 이는 모듈이 부모와 밀접하게 관련되어 있지만 부모가 언젠가 모듈 트리의 다른 곳으로 이동할 수 있을 때 모듈 트리를 재배열하기 쉽게 만들 수 있습니다.

목록 7-8의 코드는 셰프가 잘못된 주문을 수정하고 개인적으로 고객에게 가져다 주는 상황을 모델링합니다. `back_of_house` 모듈에 정의된 `fix_incorrect_order` 함수는 `super`로 시작하는 경로를 지정하여 부모 모듈에 정의된 `deliver_order` 함수를 호출합니다.

파일명: src/lib.rs

```rust
fn deliver_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::deliver_order();
    }

    fn cook_order() {}
}
```

목록 7-8: `super`로 시작하는 상대 경로를 사용하여 함수 호출하기

`fix_incorrect_order` 함수는 `back_of_house` 모듈에 있으므로, `super`를 사용하여 `back_of_house`의 부모 모듈로 이동할 수 있으며, 이 경우에는 루트인 `crate`입니다. 거기서부터 `deliver_order`를 찾고 찾습니다. 성공입니다! 우리는 `back_of_house` 모듈과 `deliver_order` 함수가 서로에 대한 관계를 유지하고 크레이트의 모듈 트리를 재구성하기로 결정하면 함께 이동할 가능성이 높다고 생각합니다. 따라서, 이 코드가 다른 모듈로 이동하면 나중에 코드를 업데이트할 곳이 더 적도록 `super`를 사용했습니다.

### 구조체와 열거형을 공개하기

우리는 또한 `pub`을 사용하여 구조체와 열거형을 공개로 지정할 수 있지만, 구조체와 열거형에 `pub`을 사용하는 데는 몇 가지 추가 세부사항이 있습니다. 구조체 정의 앞에 `pub`을 사용하면 구조체를 공개하게 되지만, 구조체의 필드는 여전히 비공개입니다. 각 필드를 케이스별로 공개하거나 비공개로 만들 수 있습니다. 목록 7-9에서, 우리는 공개 `toast` 필드와 비공개 `seasonal_fruit` 필드를 가진 공개 `back_of_house::Breakfast` 구조체를 정의했습니다. 이는 레스토랑에서 고객이 식사와 함께 제공되는 빵의 종류를 선택할 수 있지만, 셰프가 계절과 재고에 따라 식사와 함께 제공되는 과일을 결정하는 경우를 모델링합니다. 사용 가능한 과일은 빠르게 변경되므로, 고객은 과일을 선택하거나 심지어 어떤 과일을 받게 될지 볼 수 없습니다.

파일명: src/lib.rs

```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // 여름에 호밀 토스트로 아침 식사 주문
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // 우리가 원하는 빵에 대한 생각을 바꿈
    meal.toast = String::from("Wheat");
    println!("{} 토스트를 주세요", meal.toast);

    // 다음 줄은 주석을 해제하면 컴파일되지 않습니다; 우리는
    // 식사와 함께 제공되는 계절 과일을 보거나 수정할 수 없습니다
    // meal.seasonal_fruit = String::from("blueberries");
}
```

목록 7-9: 일부 공개 필드와 일부 비공개 필드를 가진 구조체

`back_of_house::Breakfast` 구조체의 `toast` 필드는 공개이므로, `eat_at_restaurant`에서 점 표기법을 사용하여 `toast` 필드를 읽고 쓸 수 있습니다. `seasonal_fruit` 필드는 비공개이므로 `eat_at_restaurant`에서 사용할 수 없다는 점에 주목하세요. `seasonal_fruit` 필드 값을 수정하는 줄의 주석을 해제하여 어떤 오류가 발생하는지 확인해 보세요!

또한, `back_of_house::Breakfast`에 비공개 필드가 있기 때문에, 구조체는 `Breakfast`의 인스턴스를 구성하는 공개 연관 함수를 제공해야 합니다(여기서는 `summer`라는 이름을 지정했습니다). `Breakfast`에 이러한 함수가 없다면, 비공개 `seasonal_fruit` 필드의 값을 `eat_at_restaurant`에서 설정할 수 없기 때문에 `eat_at_restaurant`에서 `Breakfast`의 인스턴스를 생성할 수 없습니다.

대조적으로, 열거형을 공개하면 모든 변형이 공개됩니다. 목록 7-10에서 보여주는 것처럼 `enum` 키워드 앞에 `pub`만 필요합니다.

파일명: src/lib.rs

```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}
```

목록 7-10: 열거형을 공개로 지정하면 모든 변형이 공개됨

`Appetizer` 열거형을 공개했기 때문에, `eat_at_restaurant`에서 `Soup`과 `Salad` 변형을 사용할 수 있습니다.

열거형은 변형이 공개되지 않으면 매우 유용하지 않습니다; 모든 열거형 변형에 모든 경우에 `pub`을 주석 처리해야 하는 것은 번거로울 것이므로, 열거형 변형의 기본값은 공개입니다. 구조체는 필드가 공개되지 않아도 종종 유용하므로, 구조체 필드는 `pub`으로 주석 처리되지 않은 한 기본적으로 모든 것이 비공개라는 일반적인 규칙을 따릅니다.

우리가 아직 다루지 않은 `pub`과 관련된 한 가지 상황이 더 있으며, 그것이 우리의 마지막 모듈 시스템 기능입니다: `use` 키워드. 먼저 `use`를 자체적으로 다루고, 그런 다음 `pub`과 `use`를 어떻게 결합하는지 보여드리겠습니다.
