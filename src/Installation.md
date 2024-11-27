# 설치 - Rust 프로그래밍 언어
첫 번째 단계는 Rust를 설치하는 것입니다. Rust 버전 및 관련 도구를 관리하기 위한 `rustup` 명령줄 도구 를 통해 Rust를 다운로드할 것입니다. 다운로드하려면 인터넷 연결이 필요합니다.

 

참고: 어떤 이유로 `rustup` 를 쓰지 않고 설치를 하고 싶다면, [다른 Rust 설치 방법 페이지를](https://forge.rust-lang.org/infra/other-installation-methods.html) 통해 좀더 많은 옵션을 확인하세요.

다음 단계는 Rust 컴파일러의 안정적인 최신 버전을 설치합니다. Rust의 안정성 보장은 컴파일되는 책의 모든 예제가 최신 Rust 버전에서 계속 컴파일되도록 합니다. 출력은 버전마다 약간 다를 수 있는데, Rust는 종종 오류 메시지와 경고를 개선하기 때문입니다. 즉, 이 단계를 사용하여 설치하는 안정적인 최신 버전의 Rust는 이 책의 내용에서 예상대로 작동해야 합니다.

### [명령줄 표기법](#command-line-notation)

이 장과 책 전체에서 터미널에서 사용되는 몇 가지 명령을 보여줍니다. 터미널에 입력해야 하는 줄은 모두 `$` 로 시작합니다. `$` 문자를 입력할 필요가 없습니다. 각 명령의 시작을 나타내기 위해 표시되는 명령줄 프롬프트입니다. `$` 로 시작하지 않는 줄은 일반적으로 이전 명령의 출력을 표시합니다. 또한 PowerShell 관련 예제에서는 `$` 보다 `>` 로 시작합니다.

### [Installing `rustup` on Linux or macOS](#installing-rustup-on-linux-or-macos)

Linux 또는 macOS를 사용하는 경우 터미널을 열고 다음 명령을 입력합니다.

```
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh

```

`rustup` 명령은 스크립트를 다운로드하고 최신 안정 버전의 Rust를 설치하는 도구 설치를 시작합니다. 암호를 입력하라는 메시지가 표시될 수 있습니다. 설치에 성공하면 다음 줄이 나타납니다.

```
Rust is installed now. Great!

```


또한 _linker_ 가 필요한데, _linker_ 는 Rust가 컴파일된 출력을 하나의 파일로 결합하는 데 사용하는 프로그램입니다. 이미 가지고 있을 가능성이 높습니다. 링커 오류가 발생하면 일반적으로 링커를 포함하는 C 컴파일러를 설치해야 합니다. 일부 일반적인 Rust 패키지는 C 코드에 의존하고 C 컴파일러가 필요하기 때문에 C 컴파일러도 유용합니다.

macOS에서는 다음을 실행하여 C 컴파일러를 가져올 수 있습니다.

```
$ xcode-select --install

```

Linux 사용자는 일반적으로 배포판 설명서에 따라 GCC 또는 Clang을 설치해야 합니다. 예를 들어 Ubuntu를 사용하는 경우 `build-essential` 패키지를 설치할 수 있습니다.

### [Installing `rustup` on Windows](#installing-rustup-on-windows)

On Windows, go to [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install) and follow the instructions for installing Rust. At some point in the installation, you’ll be prompted to install Visual Studio. This provides a linker and the native libraries needed to compile programs. If you need more help with this step, see [https://rust-lang.github.io/rustup/installation/windows-msvc.html](https://rust-lang.github.io/rustup/installation/windows-msvc.html)

The rest of this book uses commands that work in both _cmd.exe_ and PowerShell. If there are specific differences, we’ll explain which to use.

### [Troubleshooting](#troubleshooting)

To check whether you have Rust installed correctly, open a shell and enter this line:

```
$ rustc --version

```


You should see the version number, commit hash, and commit date for the latest stable version that has been released, in the following format:

```
rustc x.y.z (abcabcabc yyyy-mm-dd)

```


If you see this information, you have installed Rust successfully! If you don’t see this information, check that Rust is in your `%PATH%` system variable as follows.

In Windows CMD, use:

```
> echo %PATH%

```


In PowerShell, use:

```
> echo $env:Path

```


In Linux and macOS, use:

```
$ echo $PATH

```


If that’s all correct and Rust still isn’t working, there are a number of places you can get help. Find out how to get in touch with other Rustaceans (a silly nickname we call ourselves) on [the community page](https://www.rust-lang.org/community).

### [Updating and Uninstalling](#updating-and-uninstalling)

Once Rust is installed via `rustup`, updating to a newly released version is easy. From your shell, run the following update script:

```
$ rustup update

```


To uninstall Rust and `rustup`, run the following uninstall script from your shell:

```
$ rustup self uninstall

```


### [Local Documentation](#local-documentation)

The installation of Rust also includes a local copy of the documentation so that you can read it offline. Run `rustup doc` to open the local documentation in your browser.

Any time a type or function is provided by the standard library and you’re not sure what it does or how to use it, use the application programming interface (API) documentation to find out!

[](https://doc.rust-lang.org/book/ch01-00-getting-started.html "Previous chapter")[](https://doc.rust-lang.org/book/ch01-02-hello-world.html "Next chapter")

[](https://doc.rust-lang.org/book/ch01-00-getting-started.html "Previous chapter")[](https://doc.rust-lang.org/book/ch01-02-hello-world.html "Next chapter")