## panic!으로 복구 불가능한 에러 처리하기  

가끔은 코드에서 나쁜 일이 일어나고, 이에 대해 여러분이 할 수 있는 것이 없을 수도 있습니다. 이런 경우를 위해 러스트에는 `panic!` 매크로가 있습니다. 실제로 패닉을 일으키는 두 가지 방법이 있습니다: (배열 끝부분을 넘어선 접근과 같이) 코드가 패닉을 일으킬 동작을 하는 것 혹은 `panic!` 매크로를 명시적으로 호출하는 것이죠. 두 경우 모두 프로그램에 패닉을 일으킵니다.   
기본적으로 이러한 패닉은 실패 메시지를 출력하고, 되감고 (unwind), 스택을 청소하고, 종료합니다. 패닉이 발생했을 때 그 패닉의 근원을 쉽게 추적하기 위해 환경 변수를 통하여 러스트가 호출 스택을 보여주도록 할 수 있습니다.  
  
> panic!에 대응하여 스택을 되감거나 그만두기
기본적으로, `panic!`이 발생하면, 프로그램은 *되감기 (unwinding)* 를 시작하는데, 이는 러스트가 패닉을 발생시킨 각 함수로부터 스택을 거꾸로 훑어가면서 데이터를 청소한다는 뜻입니다. 하지만 이 되감기와 청소 작업은 간단한 작업이 아닙니다. 그래서 러스트에서는 프로그램이 데이터 정리 작업 없이 즉각 종료되는 대안인 *그만두기 (aborting)* 를 선택할 수도 있습니다.
>
> 프로그램이 사용하고 있던 메모리는 운영체제가 청소해 주어야 합니다. 프로젝트 내에서 결과 바이너리를 가능한 한 작게 만들고 싶다면, *Cargo.toml* 내에서 적합한 `[profile]` 섹션에 `panic = 'abort'`를 추가하여 되감기를 그만두기로 바꿀 수 있습니다. 예를 들어, 여러분이 릴리즈 모드에서는 패닉 시 그만두기 방식을 쓰고 싶다면, 다음을 추가하세요:
>
> ``` ini
> [profile.release]
> panic = 'abort'  
> ```
  
간단한 프로그램에서 `panic!`을 호출해 봅시다:  

파일명: src/main.rs 

``` rust
fn main() {
    panic!("crash and burn");
}
```
  
프로그램을 실행하면, 다음과 같은 내용이 나타납니다:

``` console
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished dev [unoptimized + debuginfo] target(s) in 0.25s
     Running `target/debug/panic`
thread 'main' panicked at 'crash and burn', src/main.rs:2:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```  

`panic!`의 호출이 마지막 두 줄의 에러 메시지를 일으킵니다. 첫 번째 줄은 작성해둔 패닉 메시지와 패닉이 발생한 소스 코드 지점을 보여줍니다. *src/main.rs:2:5*는 *src/main.rs* 파일의 두 번째 줄 다섯 번째 문자를 나타냅니다.

이 예제에서는 표시된 줄이 직접 작성한 코드 부분이고, 해당 라인에서 `panic!` 매크로 호출을 눈으로 직접 볼 수 있습니다. 그 외의 경우, 우리가 호출한 외부 코드에서 `panic!` 호출이 있을 수도 있습니다.   
에러 메시지에 의해 보고되는 파일 이름과 라인 번호는 `panic!` 매크로가 호출된 다른 누군가의 코드일 것이며, 궁극적으로 `panic!`을 발생시킨 것이 우리 코드 라인이 아닐 것입니다. 문제를 일으킨 코드 조각을 발견하기 위해서 `panic!` 호출이 발생한 함수에 대한 백트레이스 (backtrace) 를 사용할 수 있습니다.   
백트레이스에 대해서는 뒤에 더 자세히 다룰 것입니다.  

  
### panic! 백트레이스 이용하기
  
직접 매크로를 호출하는 대신 우리 코드의 버그 때문에 라이브러리로부터 `panic!` 호출이 발생할 때는 어떻게 되는지 다른 예제를 통해서 살펴봅시다. 예제 9-1은 유효한 범위를 넘어서는 인덱스로 벡터에 접근을 시도하는 코드입니다.

파일명: src/main.rs

``` rust,editable
fn main() {
    let v = vec![1, 2, 3];

    v[99];
}
``` 
##### 예제 9-1: `panic!`을 일으키는 벡터의 끝을 넘어서는 요소에 대한 접근 시도

여기서는 벡터의 100번째 요소(0부터 시작하므로 99입니다)에 접근하기를 시도하고 있지만, 이 벡터는 단 3개의 요소만 가지고 있습니다. 이 경우 러스트는 패닉을 일으킬 것입니다. `[]`의 사용은 어떤 요소의 반환을 가정하지만, 유효하지 않은 인덱스를 넘기게 되면 러스트가 반환할 올바른 요소가 없습니다.

C에서 데이터 구조의 끝을 넘어서 읽는 시도는 정의되지 않은 동작입니다. 메모리가 해당 데이터 구조의 소유가 아닐지라도, 그 데이터 구조의 해당 요소에 상응하는 메모리 위치에 있는 모든 값을 가져올 수 있습니다. 이러한 것을 *버퍼 초과 읽기 (buffer overread)* 라 하며, 접근이 허용되어서는 안 되는 데이터를 읽기 위해 어떤 공격자가 배열 뒤에 저장된 데이터를 읽어낼 요량으로 인덱스를 다루게 된다면, 이는 보안 취약점으로 이어질 수 있습니다.  

프로그램을 이러한 취약점으로부터 보호하기 위해서, 존재하지 않는 인덱스에서의 요소를 읽으려 시도한다면, 러스트는 실행을 멈추고 계속하기를 거부할 것입니다. 한번 시도해 봅시다:  

``` console
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished dev [unoptimized + debuginfo] target(s) in 0.27s
     Running `target/debug/panic`
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 99', src/main.rs:4:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```
  
위 에러는 99 인덱스로 접근을 시도한 `main.rs` 4번째 줄을 가리키고 있습니다. 그다음 줄은 `RUST_BACKTRACE` 환경 변수를 설정하여 에러의 원인이 무엇인지 정확하게 백트레이스할 수 있다고 말해주고 있습니다.   
*백트레이스 (backtrace)* 란 어떤 지점에 도달하기까지 호출한 모든 함수의 목록을 말합니다. 러스트의 백트레이스는 다른 언어들과 마찬가지로 동작합니다: 백트레이스를 읽는 요령은 위에서부터 시작하여 여러분이 작성한 파일이 보일 때까지 읽는 것입니다.  
그곳이 바로 문제를 일으킨 지점입니다. 여러분의 파일이 나타난 줄보다 위에 있는 줄은 여러분의 코드가 호출한 코드이고, 아래의 코드는 여러분의 코드를 호출한 코드입니다. 이 전후의 줄에는 핵심 러스트 코드, 표준 라이브러리, 여러분이 이용하고 있는 크레이트가 포함될 수 있습니다. 한번 `RUST_BACKTRACE` 환경변수를 0이 아닌 값으로 설정하여 백트레이스를 얻어봅시다. 예제 9-2는 여러분이 보게 될 것과 유사한 출력을 나타냅니다.  
  
``` console
$ RUST_BACKTRACE=1 cargo run
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 99', src/main.rs:4:5
stack backtrace:
   0: rust_begin_unwind
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/std/src/panicking.rs:584:5
   1: core::panicking::panic_fmt
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/panicking.rs:142:14
   2: core::panicking::panic_bounds_check
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/panicking.rs:84:5
   3: <usize as core::slice::index::SliceIndex<[T]>>::index
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/slice/index.rs:242:10
   4: core::slice::index::<impl core::ops::index::Index<I> for [T]>::index
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/slice/index.rs:18:9
   5: <alloc::vec::Vec<T,A> as core::ops::index::Index<I>>::index
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/alloc/src/vec/mod.rs:2591:9
   6: panic::main
             at ./src/main.rs:4:5
   7: core::ops::function::FnOnce::call_once
             at /rustc/e092d0b6b43f2de967af0887873151bb1c0b18d3/library/core/src/ops/function.rs:248:5
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```  
##### 예제 9-2: 환경 변수 `RUST_BACKTRACE`가 설정되었을 때 `panic!`의 호출에 의해 발생하는 백트레이스 출력

출력이 엄청 많군요! 여러분이 보는 실제 출력값은 운영 체제 및 러스트 버전에 따라 다를 수 있습니다. 이러한 정보로 백트레이스를 얻기 위해서는 디버그 심볼이 활성화되어 있어야 합니다. 디버그 심볼은 여기서처럼 여러분이 `cargo build`나 `cargo run`을 `--release` 플래그 없이 실행했을 때 기본적으로 활성화됩니다.

예제 9-2 출력 내용에서는 백트레이스의 6번 라인이 문제를 일으킨 이 프로젝트 *src/main.rs*의 4번 줄을 가리키고 있습니다. 프로그램이 패닉에 빠지지 않도록 하려면 직접 작성한 파일이 언급된 첫 줄부터 조사해야 합니다. 고의로 패닉을 일으키도록 코드를 작성한 예제 9-1에서 패닉을 고칠 방법은 범위를 벗어난 벡터 인덱스로 요소를 요청하지 않도록 하는 것입니다. 추후 여러분의 코드에서 패닉이 발생할 때는 어떤 코드가 패닉을 일으키는지, 코드를 어떻게 고쳐야 하는지 알아야 합니다.

다음은 에러가 발생했을 때 `Result`를 이용하여 복구하는 방법을 살펴보겠습니다. 언제 `panic!`을 써야 하는지, 혹은 쓰지 말아야 하는지에 대해서는 그다음에 나올 [`‘panic!`이냐, `panic!`이 아니냐, 그것이 문제로다’](To_panic_or_Not_to_panic.md)절에서 알아볼 예정입니다.