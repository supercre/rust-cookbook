## 변수와 가변성  
  
[변수에 값 저장하기](https://doc.rust-lang.org/book/ch02-00-guessing-game-tutorial.html#storing-values-with-variables)에서 언급했듯이, 변수는 기본적으로 불변입니다.  
이것은 러스트가 제공하는 안정성과 쉬운 동시성을 활용하는 방식으로 코드를 작성할 수 있도록 하는 너지(nudge, 슬며시 선택을 유도하기) 중 하나입니다.  하지만 여전히 변수를 가변적으로 만들 수 있습니다. 
어떻게 하는지 살펴보고 왜 러스트가 불변성을 권하는지와 어떨 때 가변성을 써야 하는지 알아봅시다.  

변수가 불변일 때, 값이 이름에 바인딩되면 해당 값을 변경할 수 없습니다. 이를 설명하기 위해  `cargo new variables`을 사용하여 *project* 디렉토리 안에 *variables* 라는 새 프로젝트를 생성합니다.  

그리고, 새 *variables* 디렉트리의 *src/main.rc* 파일을 열어서 다음의 코드로 바꿔보세요. 이 코드는 아직 컴파일 되지 않습니다.  

파일 이름: src/main.rs  
  
```rust,editable
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```  

저장하고 `cargo run`을 사용하여 프로그램을 실행합니다. 다음의 출력처럼 불변성 에러에 관한 에러 메시지를 받을 것입니다.  

```shell
$ cargo run
    Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {x}");
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x = 5;
  |         +++

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` (bin "variables") due to 1 previous error

```

이 예는 컴파일러가 프로그램에서 오류를 찾는 데 어떻게 도와주는지 보여줍니다. 컴파일러 오류는 짜증스러울 수 있지만, 실제로는 여러분의 프로그램이 아직 원하는 작업을 안전하게 작동하지 않는다고 할 뿐입니다. 당신이 훌륭한 프로그래머가 아니라는 것을 의미 하지는 *않습니다! 숙련된 Rustaceans도 여전히 컴파일러 오류를 받습니다.*

