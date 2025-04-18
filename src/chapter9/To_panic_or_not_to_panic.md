## panic!이냐, panic!이 아니냐, 그것이 문제로다
  
그러면 언제 `panic!`을 써야 하고 언제 `Result`를 반환할지는 어떻게 결정해야 할까요? 코드가 패닉을 일으킬 때는 복구할 방법이 없습니다. 복구 가능한 방법이 있든 없든 간에 에러 상황에 대해 `panic!`을 호출할 수 있지만, 그렇게 되면 호출하는 코드를 대신하여 현 상황은 복구 불가능한 것이라고 결정을 내리는 꼴이 됩니다. `Result` 값을 반환하는 선택을 한다면 호출하는 쪽에게 옵션을 제공하는 것입니다. 호출하는 코드 쪽에서는 상황에 적합한 방식으로 복구를 시도할 수도 있고, 혹은 현재 상황의 `Err`은 복구 불가능하다고 결론을 내리고 `panic!`을 호출하여 복구 가능한 에러를 복구 불가능한 것으로 바꿔놓을 수도 있습니다.   
그러므로 실패할지도 모르는 함수를 정의할 때는 기본적으로 `Result`를 반환하는 것이 좋은 선택입니다.

예제, 프로토타입, 테스트 같은 상황에서는 `Result`를 반환하는 대신 패닉을 일으키는 코드가 더 적절합니다. 왜 그런지 탐구해 보고, 사람으로서의 여러분이라면 실패할 리 없는 코드라는 것을 알 수 있지만, 컴파일러는 이유를 파악할 수 없는 경우에 대해서도 논의해 봅시다. 그리고 라이브러리 코드에 패닉을 추가해야 할지 말지를 어떻게 결정할까에 대한 일반적인 가이드라인을 내림으로써 결론짓겠습니다.


### 예제, 프로토타입 코드, 그리고 테스트  
  
어떤 개념을 묘사하기 위한 예제를 작성 중이라면, 견고한 에러 처리 코드를 포함시키는 것이 오히려 예제의 명확성을 떨어트릴 수도 있습니다. 예제 코드 내에서는 `panic!`을 일으킬 수 있는 `unwrap` 같은 메서드의 호출이 애플리케이션의 에러 처리가 필요한 곳을 뜻하는 방식으로 해석될 수 있는데, 이러한 에러 처리는 코드의 나머지 부분이 하는 일에 따라 달라질 수 있습니다.

비슷한 상황으로 에러를 어떻게 처리할지 결정할 준비가 되기 전이라면, `unwrap`과 `expect` 메서드가 프로토타이핑할 때 매우 편리합니다. 이 함수들은 코드를 더 견고하게 만들 준비가 되었을 때를 위해서 명확한 표시를 남겨 둡니다.

만일 테스트 내에서 메서드 호출이 실패한다면, 해당 메서드가 테스트 중인 기능이 아니더라도 전체 테스트를 실패시키도록 합니다. `panic!`이 테스트의 실패를 표시하는 방식이므로, `unwrap`이나 `expect`의 호출이 정확히 그렇게 만들어줍니다.  
  
### 여러분이 컴파일러보다 더 많은 정보를 가지고 있을 때  
  
`Result`가 `Ok` 값을 가지고 있을 거라 확신할만한 논리적 근거가 있지만, 컴파일러가 그 논리를 이해할 수 없는 경우라면, `unwrap`혹은 `expect`를 호출하는 것이 적절할 수 있습니다. 어떤 연산이든 간에 특정한 상황에서는 논리적으로 불가능할지라도 기본적으로는 실패할 가능성을 가지고 있는 코드를 호출하는 것이므로, 처리가 필요한 `Result` 값이 나오게 됩니다. 손수 코드를 조사하여 `Err` 배리언트가 나올리 없음을 확신할 수 있다면 `unwrap`을 호출해도 아무런 문제가 없으며, `expect`의 문구에 `Err` 배리언트가 있으면 안 될 이유를 적어주는 것이 더 좋을 것입니다. 아래에 예제가 있습니다.

``` rust,editable

    use std::net::IpAddr;

    let home: IpAddr = "127.0.0.1"
        .parse()
        .expect("Hardcoded IP address should be valid");

```
여기서는 하드코딩된 문자열을 파싱하여 `IpAddr` 인스턴스를 만드는 중입니다. `127.0.0.1`이 유효한 IP 주소라는 사실을 알 수 있으므로, 여기서는 `expect`의 사용이 허용됩니다. 하지만 하드코딩된 유효한 문자열이라는 사실이 `parse` 메서드의 반환 타입을 변경해 주지는 않습니다. 여전히 `Result` 값이 나오고, 컴파일러는 마치 `Err` 배리언트가 나올 가능성이 여전히 있는 것처럼 `Result`를 처리하도록 요청할 것인데, 그 이유는 이 문자열이 항상 유효한 IP 주소라는 사실을 알 수 있을 만큼 컴파일러가 똑똑하지 않기 때문입니다. 만일 IP 주소 문자열이 프로그램에 하드코딩된 것이 아니라 사용자로부터 입력되었다면, 그래서 실패할 가능성이 생겼다면, 더 견고한 방식으로 `Result`를 처리할 필요가 분명히 있습니다. `expect`에 이 IP 주소가 하드코딩 되었다는 가정을 언급하는 것은 향후에 IP 주소가 다른 곳으로부터 가져올 필요가 생길 경우 `expect`를 더 나은 에러 처리 코드로 수정하도록 재촉할 것입니다.  
  
### 에러 처리를 위한 가이드라인
  
코드가 결국 나쁜 상태에 처하게 될 가능성이 있을 때는 코드에 `panic!`을 넣는 것이 바람직합니다. 이 글에서 말하는 *나쁜 상태*란 어떤 가정, 보장, 계약, 혹은 불변성이 깨질 때를 뜻하는 것으로, 이를테면 유효하지 않은 값이나 모순되는 값, 혹은 찾을 수 없는 값이 코드에 전달되는 경우를 말합니다 - 아래에 쓰인 상황 중 하나 혹은 그 이상일 경우라면 말이죠:

- 이 나쁜 상태란 것은 예기치 못한 무언가이며, 이는 사용자가 입력한 데이터가 잘못된 형식이라던가 하는 흔히 발생할 수 있는 것과는 반대되는 것입니다.
- 그 시점 이후의 코드는 매번 해당 문제에 대한 검사를 하는 것이 아니라, 이 나쁜 상태에 있지 않아야만 할 필요가 있습니다.
- 여러분이 사용하고 있는 타입 내에 이 정보를 집어넣을만한 뾰족한 수가 없습니다. 이러한 것의 의미에 대한 예제를 17장의 ['상태와 동작을 타입으로 인코딩하기'](https://doc.rust-kr.org/ch17-03-oo-design-patterns.html#encoding-states-and-behavior-as-types)절에서 살펴볼 것입니다.   
  
만일 어떤 사람이 여러분의 코드를 호출하고 타당하지 않은 값을 집어넣었다면, 가능한 에러를 반환하여 라이브러리의 사용자들이 이러한 경우에 대해 어떤 동작을 원하는지 결정할 수 있도록 하는 것이 가장 좋습니다. 그러나 계속 실행하는 것이 보안상 좋지 않거나 해를 끼치는 경우라면 `panic!`을 써서 여러분의 라이브러리를 사용하고 있는 사람에게 자신의 코드에 있는 버그를 알려줘서 개발 중에 이를 고칠 수 있게끔 하는 것이 최선책일 수도 있습니다. 비슷한 식으로, 여러분의 제어권에서 벗어난 외부 코드를 호출하고 있고, 이것이 고칠 방법이 없는 유효하지 않은 상태를 반환한다면, `panic!`이 종종 적절합니다.

하지만 실패가 충분히 예상되는 경우라면 `panic!`을 호출하는 것보다 `Result`를 반환하는 것이 여전히 더 적절합니다. 이에 대한 예는 잘못된 데이터가 제공된 파서나, 속도 제한에 도달했음을 나타내는 상태를 반환하는 HTTP 요청 등이 있습니다. 이러한 경우, `Result`를 반환하면 호출자가 처리 방법을 결정해야 하는 실패 가능성이 예상된다는 것을 나타냅니다.

코드가 유효하지 않은 값에 대해 호출되면 사용자를 위험에 빠뜨릴 수 있는 연산을 수행할 때, 그 코드는 해당 값이 유효한지를 먼저 검사하고, 만일 그렇지 않다면 `panic!`을 호출해야 합니다. 이는 주로 보안상의 이유입니다: 유효하지 않은 데이터에 어떤 연산을 시도하는 것은 코드를 취약점에 노출시킬 수 있습니다. 이것이 범위를 벗어난 메모리 접근을 시도했을 경우 표준 라이브러리가 `panic!`을 호출하는 주된 이유입니다: 현재 사용하는 데이터 구조가 소유하지 않은 메모리에 접근 시도하는 것은 흔한 보안 문제입니다. 종종 함수에는 입력이 특정 요구사항을 만족시킬 경우에만 함수의 행동이 보장되는 *계약*이 있습니다. 이 계약을 위반했을 때는 패닉을 발생시키는 것이 이치에 맞는데, 그 이유는 계약 위반이 항상 호출자 쪽의 버그임을 나타내고, 이는 호출하는 코드가 명시적으로 처리해야 하는 종류의 버그가 아니기 때문입니다. 사실 호출하는 쪽의 코드가 복구시킬 합리적인 방법은 존재하지 않고, 호출하는 *프로그래머*가 그 코드를 고칠 필요가 있습니다. 함수에 대한 계약은, 특히 계약 위반이 패닉의 원인이 될 때는, 그 함수에 대한 API 문서에 설명되어야 합니다.

하지만 모든 함수 내에서 수많은 에러 검사를 한다는 것은 장황하고 짜증나는 일일 것입니다. 다행히도 러스트의 타입 시스템이 (그리고 컴파일러에 의한 타입 검사 기능이) 여러분을 위해 수많은 검사를 해줄 수 있습니다. 함수에 특정한 타입의 매개변수가 있는 경우 컴파일러가 이미 유효한 값을 확인했으므로 코드 로직을 계속 진행할 수 있습니다.  
예를 들면, 만약 `Option`이 아닌 어떤 타입을 갖고 있다면, 여러분의 프로그램은 *아무것도 아닌 것*이 아닌 *무언가*를 갖고 있음을 예측합니다. 그러면 코드는 `Some`과 `None` 배리언트에 대한 두 경우를 처리하지 않아도 됩니다: 분명히 값을 가지고 있는 하나의 경우만 있을 것입니다. 함수에 아무것도 넘기지 않는 시도를 하는 코드는 컴파일조차 되지 않을 것이므로, 그 함수에서는 그런 경우에 대한 런타임 검사가 필요 없습니다. 또 다른 예로는 `u32`와 같은 부호 없는 정수의 사용이 있는데, 이는 매개변수가 절대 음수가 아님을 보장합니다.

### 유효성을 위한 커스텀 타입 생성하기  

러스트의 타입 시스템을 사용해 유효한 값을 보장하는 아이디어에서 한 발 더 나가서, 유효성 검사를 위한 커스텀 타입을 생성하는 방법을 살펴봅시다. 2장의 추리 게임을 상기해 보시면, 사용자에게 1부터 100 사이의 숫자를 추측하도록 요청했었죠. 사용자의 추릿값을 비밀 번호와 비교하기 전에 추릿값이 양수인지만 확인했을 뿐, 해당 값이 유효한지는 확인하지 않았습니다. 이 경우에는 결과가 그렇게 끔찍하지는 않았습니다: 'Too high'나 'Too low'라고 표시했던 출력이 여전히 정확했기 때문입니다. 하지만 사용자가 올바른 추측을 할 수 있도록 안내하고, 사용자가 범위를 벗어난 숫자를 입력했을 때와 사용자가 숫자가 아닌 문자 등을 입력했을 때 다른 동작을 하는 건 꽤 괜찮은 개선일 겁니다.

이를 위한 한 가지 방법은 `u32` 대신 `i32`로 추릿값을 파싱하여 음수가 입력될 가능성을 허용하고, 그리고서 숫자가 범위 내에 있는지에 대한 검사를 아래와 같이 추가하는 것입니다:  

```rust,editable  
    loop {
        // --생략--

        let guess: i32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        if guess < 1 || guess > 100 {
            println!("The secret number will be between 1 and 100.");
            continue;
        }

        match guess.cmp(&secret_number) {
            // --생략--
    }
```

`if` 표현식은 값이 범위 밖에 있는지 혹은 그렇지 않은지 검사하고, 사용자에게 문제점을 말해주고, `continue`를 호출하여 루프의 다음 반복을 시작하고 다른 추릿값을 요청해 줍니다. `if` 표현식 이후에는 `guess`가 1과 100 사이의 값임을 확인한 상태에서 `guess`와 비밀 숫자의 비교를 진행할 수 있습니다.

하지만 이는 이상적인 해결책이 아닙니다. 만약 프로그램이 오직 1과 100 사이의 값에서만 동작한다는 점이 굉장히 중요한 사항이고 많은 함수가 동일한 요구사항을 가지고 있다면, 모든 함수 내에서 이런 검사를 하는 것은 지루한 일일 겁니다. (게다가 성능에 영향을 줄지도 모릅니다.)

그대신 새로운 타입을 만들어서 그 타입의 인스턴스를 생성하는 함수에서 유효성을 확인하는 방식으로 유효성 확인을 모든 곳에서 반복하지 않게 할 수 있습니다. 이렇게 하면 함수가 새로운 타입을 시그니처에 사용하여 받은 값을 자신있게 사용할 수 있어 안전합니다. 예제 9-13은 `new` 함수가 1과 100 사이의 값을 받았을 때만 인스턴스를 생성하는 `Guess` 타입을 정의하는 한 가지 방법을 보여줍니다:  

```rust, editable  
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess { value }
    }

    pub fn value(&self) -> i32 {
        self.value
    }
}
```

##### 예제 9-13: 1과 100 사이의 값일 때만 실행을 계속하는 `Guess` 타입  
  
먼저 `i32`를 갖는 `value`라는 이름의 필드를 가진 `Guess`라는 이름의 구조체를 선언하였습니다. 이것이 숫자가 저장될 곳입니다.

그다음 `Guess` 값의 인스턴스를 생성하는 `new`라는 이름의 연관 함수를 구현하였습니다. `new` 함수는 `i32` 타입의 값인 `value`를 매개변수를 받아서 `Guess`를 반환하도록 정의되었습니다. `new` 함수의 본문에 있는 코드는 `value`가 1부터 100 사이의 값인지 확인하는 테스트를 합니다. 만일 `value`가 이 테스트에 통과하지 못하면 `panic!`을 호출하며, 이는 이 코드를 호출하는 프로그래머에게 고쳐야 할 버그가 있음을 알려주는데, 범위 밖의 `value`로 `Guess`를 생성하는 것은` Guess::new`가 요구하는 계약을 위반하기 때문입니다. `Guess::new`가 패닉을 일으킬 수 있는 조건은 공개 API 문서에서 다뤄져야 합니다. 여러분이 만드는 API 문서에서 `panic!`의 가능성을 가리키는 것에 대한 문서 관례는 14장에서 다룰 것입니다. 만일 `value`가 테스트를 통과한다면, `value` 매개변수로 `value` 필드를 설정한 새로운 `Guess`를 생성하여 이를 반환합니다.  

다음으로, `self`를 빌리고, 매개변수를 갖지 않으며, `i32`를 반환하는 `value`라는 이름의 메서드를 구현했습니다. 이러한 종류의 메서드를 종종 *게터 (getter)* 라고 부르는데, 그 이유는 이런 함수의 목적이 객체의 필드로부터 어떤 데이터를 가져와서 반환하는 것이기 때문입니다. 이 공개 메서드가 필요한 이유는 `Guess` 구조체의 `value` 필드가 비공개이기 때문입니다. `value` 필드를 비공개이기 때문에 `Guess` 구조체를 사용하는 코드는 `value`를 직접 설정할 수 없다는 것은 중요합니다. 모듈 밖의 코드는 반드시 `Guess::new` 함수로 새로운 `Guess`의 인스턴스를 생성해야 하며, 이를 통해 `Guess`가 `Guess::new` 함수의 조건에 의해 확인되지 않은 `value`를 가질 수 없음을 보장합니다.  

이제 1에서 100 사이의 숫자를 매개변수로 쓰거나 반환하는 함수에서 `i32` 대신 `Guess`를 사용하면 함수의 본문에서 추가로 확인할 필요가 없습니다.  
  
  
## 정리  

러스트의 에러 처리 기능은 여러분이 더 견고한 코드를 작성하는 데 도움을 주도록 설계되었습니다. `panic!` 매크로는 프로그램이 처리 불가능한 상태에 놓여 있음에 대한 신호를 주고 유효하지 않거나 잘못된 값으로 계속 진행을 시도하는 대신 실행을 멈추게끔 해줍니다. `Result` 열거형은 러스트의 타입 시스템을 이용하여 복구할 수 있는 방법으로 코드의 연산이 실패할 수도 있음을 알려줍니다. 또한 `Result`를 이용하면 여러분의 코드를 호출하는 코드에게 잠재적인 성공이나 실패를 처리해야 할 필요가 있음을 알려줄 수 있습니다. panic!과 `Result`를 적절한 상황에서 사용하는 것은 여러분의 코드가 불가피한 문제에 직면했을 때도 더 신뢰할 수 있도록 해줄 것입니다.

이제 `Option`과 `Result` 열거형을 가지고 표준 라이브러리의 유용한 제네릭 사용 방식들을 보았으니, 제네릭이 어떤 식으로 동작하고 여러분의 코드에 어떻게 이용할 수 있는지에 대해 이야기해 보겠습니다.






 
