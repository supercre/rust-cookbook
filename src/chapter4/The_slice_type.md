<section>
  <h2>
    <a>슬라이스 타입(The Slice Type)</a>
  </h2>
  <p>
    슬라이스 타입 슬라이스는 전체 컬렉션을 참조하는 대신 컬렉션에서 연속된 요소들의 시퀀스를 참조할 수 있게 해줍니다. 슬라이스는 일종의 참조이므로 소유권이 없습니다.
  </p>
  <p>
    다음은 공백으로 구분된 단어들의 문자열을 받아 첫 번째 단어를 반환하는 함수에 대한 예시입니다. 만약 함수가 문자열에서 공백을 찾지 못하면, 그 문자열은 하나의 단어일 것이므로 전체 문자열을 반환해야 합니다.
  </p>
  <p>
    슬라이스를 사용하지 않고 이 함수의 시그니처를 작성하는 방법을 생각해봄으로써 슬라이스가 해결할 문제를 이해해봅시다.
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; ?
  </code>
  <p>
    <code>first_word</code> 함수는 <code>&amp;String</code>을 매개변수로 받습니다. 우리는 소유권을 원하지 않기 때문에 이렇게 사용해도 무방합니다. 하지만 우리는 무엇을 반환해야 할까요? 문자열의 일부를 표현할 방법이 사실상 없습니다. 그러나 우리는 단어의 끝을 나타내는 공백의 인덱스를 반환할 수 있을지도 모릅니다. Listing 4-7에서 이를 시도해보겠습니다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; usize {
        let bytes = s.as_bytes();
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return i;
            }
        }
        s.len()
    }
    </span>
    fn main() {}
  </code>
  <p>
    <span>
      Listing 4-7: <code>first_word</code> 함수는 <code>String</code> 매개변수에 대한 바이트 인덱스 값을 반환합니다.
    </span>
  </p>
  <p>
    우리는 <code>String</code>을 요소별로 확인하여 값이 공백인지 확인해야 하므로, <code>as_bytes</code> 메서드를 사용하여 <code>String</code>을 바이트 배열로 변환할 것입니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; usize {
        let bytes = s.as_bytes();
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return i;
            }
        }
        s.len()
    }
    </span>
    fn main() {}
  </code>
  <p>
    다음으로, <code>iter</code> 메서드를 사용하여 바이트 배열에 대한 반복자를 생성합니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; usize {
        let bytes = s.as_bytes();
        </span>
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return i;
            }
        }
        </span>
        s.len()
    }
    </span>
    fn main() {}
  </code>
  <p>
    우리는 반복자에 대해 더 자세히 다룰 예정이지만, 지금은 <code>iter</code> 메서드가 컬렉션의 각 요소를 반환하는 메서드라는 점과, <code>enumerate</code>가 반환하는 튜플의 첫 번째 요소는 인덱스이고, 두 번째 요소는 해당 요소에 대한 참조라는 점을 알고 넘어가면 됩니다.</br>
    이렇게 하면 인덱스를 우리가 직접 계산하는 것보다 더 편리하게 사용할 수 있습니다.
  </p>
  <p>
    <code>enumerate</code> 메서드가 튜플을 반환하기 때문에, 우리는 패턴을 사용하여 그 튜플을 구조 분해할 수 있습니다. 패턴에 대해서는 6장에서 더 자세히 다룰 예정입니다.</br>
    <code>for</code> 루프에서, 우리는 튜플에서 인덱스를 나타내는 <code>i</code>와 단일 바이트를 나타내는 <code>&amp;item</code>으로 구성된 패턴을 지정합니다. <code>.iter().enumerate()</code>에서 요소에 대한 참조를 얻기 때문에, 패턴에서도 <code>&amp;</code>를 사용합니다.
  </p>
  <p>
    <code>for</code> 루프 내부에서 바이트 리터럴 문법을 사용하여 공백을 나타내는 바이트를 찾습니다. 만약 공백을 찾으면 그 위치를 반환합니다. 그렇지 않으면, <code>s.len()</code>을 사용하여 문자열의 길이를 반환합니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; usize {
        let bytes = s.as_bytes();
        </span>
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return i;
            }
        }
        </span>
        s.len()
    }
    </span>
    fn main() {}
  </code>
  <p>
    우리는 이제 문자열에서 첫 번째 단어의 끝 인덱스를 찾을 수 있는 방법을 갖게 되었지만, 문제가 있습니다.</br>
    현재 우리는 단독으로 <code>usize</code>를 반환하고 있는데, 이는 <code>&amp;String</code>의 문맥에서만 의미가 있는 숫자입니다. 다시 말해, 이 값은 <code>String</code>과 별개의 값이기 때문에 나중에 여전히 유효할 것이라는 보장이 없습니다.</br>
    Listing 4-7의 <code>first_word</code> 함수를 사용하는 Listing 4-8의 프로그램을 살펴봅시다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; usize {
        let bytes = s.as_bytes
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return i;
            }
        }
        </span>
        s.len()
    }
    </span>
    fn main() {
        let mut s = String::from("hello world");
        let word = first_word(&amp;s); // word 변수는 값 5를 갖게 됩니다.
        s.clear(); // 이 코드는 String을 비워서 빈 문자열 ""로 만듭니다.
                   // 하지만 word는 여전히 값 5를 가지고 있습니다.
                   // 문제는 이제 우리가 이 값 5를 의미 있게사용할 수 있는 문자열이 더 이상 존재하지 않는다는 것입니다.
                   // 결과적으로 word는 완전히 무효화 됩니다.
    }
  </code>
  <p>
    <span>
      Listing 4-8: <code>first_word</code> 함수를 호출한 결과를 저장한 뒤, <code>String</code>의내용을 변경하는 프로그램
    </span>
  </p>
  <p>
    이 프로그램은 아무런 오류 없이 컴파일되며, <code>s.clear()</code>를 호출한 후 <code>word</code>를 사용하더라도 문제 없이 컴파일 됩니다.</br>
    왜냐하면, <code>word</code>가 <code>s</code>의 상태와 전혀 연결되어 있지 않기 때문입니다. 결과적으로, <code>word</code>는 여전히 값 <code>5</code>를 갖고 있습니다. 우리는 변수 <code>s</code>와 함께 이 값 <code>5</code>를 사용하여 첫 번째 단어를 추출하려고 시도할 수 있지만, 이는 오류를 발생시킬 것입니다.</br>
    왜냐하면, <code>s</code>의 내용은 우리가 <code>word</code>에 <code>5</code>를 저장한 이후 변경되었기 때문입니다.
  </p>
  <p>
    <code>word</code>에 저장된 인덱스가 <code>s</code>의 데이터와 동기화 되지 않을 가능성을 걱정해야 하는 것은 번거로우며 오류를 유발하기 쉽습니다.</br>
    <code>second_word</code>와같은 두 번째 단어를 찾는 함수를 작성한다면 이러한 인덱스를 관리하는 것은 훨씬 더 어려워질 것이며, 다음 예제를 통해 이를 볼 수 있습니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn second_word(s: &amp;String) -&gt; (usize, usize) {
  </code>
  <p>
    이제 우리는 시작 인덱스와 끝 인덱스를 추적하고 있으며, 특정 상태의 데이터에서 계산되었지만 그 상태와 전혀 연결되지 않은 값들이 더 늘어났습니다.</br>
    결과적으로, 동기화를 유지해야 하는 세 개의 관련 없는 변수가 따로따로 존재하게 됩니다.
  </p>
  <p>
    다행히도, Rust는 이 문제에 대한 해결책을 <strong>문자열 슬라이스(string slices)</strong>를 통해 제공합니다.
  </p>
</section>

<section>
  <h3>
    <a>문자열 슬라이스(String Slices)</a>
  </h3>
  <p>
    문자열 슬라이스(string slices)는 <code>String</code>의 일부를 참조하는 것으로, 다음과 같은 형태를 가지고 있습니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn main() {
        let s = String::from("hello world");
        </span>
        let hello = &amp;s[0..5];
        let world = &amp;s[6..11];
    }
  </code>
  <p>
    전체 <code>String</code>에 대한 참조가 아니라, <code>hello</code>는 문자열의 일부를 참조하며, 추가적으로 지정된 <code>[0..5]</code> 범위에 의해 정의됩니다.</br> 
    슬라이스는 대괄호 안에 범위를 지정하여 <code>[starting_index..ending_index]</code> 형태로 생성합니다. 여기서 <code>starting_index</code>는 슬라이스의 첫 번째 위치이고, <code>ending_index</code>는 슬라이스의 마지막 위치 + 1의 값입니다.</br>
    슬라이스 데이터 구조는 내부적으로 슬라이스의 시작 위치와 슬라이스의 길이를 저장하며, 이 길이는 <code>ending_index</code>에서 <code>starting_index</code>를 뺀 값에 해당합니다.</br>
    따라서, </br>
    <code>
      let world = &s[6..11];
    </code>
    </br>
    에서 <code>world</code>는 <code>s</code>의 인덱스 6에 있는 바이트를 가리키는 포인터와 길이 5를 포함하는 슬라이스가 됩니다.</br>
    그림 4-6은 이를 도식으로 보여줍니다.
  </p>
  <p>
    <img alt="Three tables: a table representing the stack data of s, which points
    to the byte at index 0 in a table of the string data &quot;hello world&quot; on
    the heap. The third table rep-resents the stack data of the slice world, which
    has a length value of 5 and points to byte 6 of the heap data table."
    src="img/trpl04-06.svg" class="center" style="width: 50%;" />
  </p>
  <p>
    <span>
    그림 4-6: 문자열 슬라이스가 <code>String</code>의 일부를 참조하는 모습
    </span>
  </p>
  <p>
    Rust의 <code>..</code> 범위 문법을 사용하면, 두 점 앞의 값을 생략할 수 있습니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn main() {
        let s = String::from("hello");
        </span>
        let slice = &amp;s[0..2];
        let slice = &amp;s[..2];
    }
  </code>
  <p>
    마찬가지로, 만약 슬라이스가 <code>String</code>의 마지막 바이트를 포함한다면, 끝 숫자를 생략할 수 있습니다.
  </p>
  <code class="language-rust edition2021 editable">#![allow(unused)]
    fn main() {
        let s = String::from("hello");
        </span>
        let len = s.len();
        /span>
        let slice = &amp;s[3..len];
        let slice = &amp;s[3..];
    }
  </code>
  <p>
    두 값을 모두 생략하여 전체 문자열의 슬라이스를 취할 수도 있습니다. 즉, 다음 두 가지는 동일합니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn main() {
        let s = String::from("hello");
        </span>
        let len = s.len();
        </span>
        let slice = &amp;s[0..len];
        let slice = &amp;s[..];
    }
  </code>
  <pre>
    <code class="language-text">
      참고: 문자열 슬라이스의 범위 인덱스는 유효한 UTF-8 문자 경계에서 발생해야 합니다.
      만약 멀티바이트 문자의 중간에서 문자열 슬라이스를 만들려고 하면, 
      프로그램이 오류로 종료됩니다.
      문자열 슬라이스를 소개하는 이 섹션에서는 ASCII만을 가정하고 있으며, 
      UTF-8 처리에 대한 더 자세한 논의는 
      8장 "UTF-8로 인코딩된 텍스트를 문자열로 저장하기" 섹션에 나옵니다.
    </code>
  </pre>
  <p>
    이 모든 정보를 바탕으로, 이제 <code>first_word</code> 함수가 슬라이스를 반환하도록 다시 작성해봅시다. 문자열 슬라이스를 나타내는 타입은 <code>&amp;str</code>로 작성됩니다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; &amp;str {
        let bytes = s.as_bytes();
        </span>
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return &amp;s[0..i];
            }
        }
        /span>
        &amp;s[..]
    }
    </span>
    fn main() {}
  </code>
  <p>
    우리는 Listing 4-7에서 했던 것과 같은 방식으로 단어의 끝 인덱스를 찾습니다. 공백이 처음 발생하는 위치를 인덱스로 사용합니다. 공백을 찾으면 문자열 슬라이스를 반환하는데, 슬라이스의 시작은 문자열의 처음이고 끝은 공백의 인덱스입니다.
  </p>
  <p>
    이제 <code>first_word</code>를 호출하면, 기본 데이터에 연결된 단일 값을 반환합니다. 이 값은 슬라이스의 시작점을 참조하는 값과 슬라이스의 요소 개수로 구성됩니다.
  </p>
  <p>
    슬라이스를 반환하는 방식은 <code>second_word</code> 함수에도 적용할 수 있습니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn second_word(s: &amp;String) -&gt; &amp;str {
  </code>
  <p>
    이제 우리는 훨씬 더 직관적이고 실수하기 어려운 API를 갖게 되었으며, 컴파일러가 <code>String</code>에 대한 참조가 유효하게 유지되도록 보장해 줍니다.</br>
    Listing 4-8 프로그램에서 첫 번째 단어의 끝 인덱스를 얻고 나서 문자열을 비워서 인덱스가 유효하지 않게 된 버그를 기억하시죠? 그 코드는 논리적으로는 잘못되었지만 즉각적인 오류를 표시하지 않았습니다.</br>
    문제는 우리가 첫 번째 단어 인덱스를 비워진 문자열과 함께 계속 사용하려 할 때 나타날 것입니다. 슬라이스는 이 버그를 불가능하게 만들고, 코드에 문제가 있음을 훨씬 더 빨리 알려줍니다.
    <code>first_word</code> 슬라이스 버전을 사용하면 컴파일 타임 오류가 발생합니다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; &amp;str {
        let bytes = s.as_bytes();
        </span>
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return &amp;s[0..i];
            }
        }
        </span>
        &amp;s[..]
    }
    </span>
    fn main() {
        let mut s = String::from("hello world");
        let word = first_word(&amp;s);
        </span>
        s.clear(); // error!
        </span>
        println!("the first word is: {word}");
    }
  </code>
  <p>
    코드 실행 결과는 다음과 같습니다.
  </p>
  <pre>
    <code class="language-console">$ cargo run
      Compiling ownership v0.1.0 (file:///projects/ownership)
      error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
        --&gt; src/main.rs:18:5
         |
      16 |     let word = first_word(&amp;s);
         |                           -- immutable borrow occurs here
      17 |
      18 |     s.clear(); // error!
         |     ^^^^^^^^^ mutable borrow occurs here
      19 |
      20 |     println!("the first word is: {word}");
         |                                  ------ immutable borrow later used here
      </span>
      For more information about this error, try `rustc --explain E0502`.
      error: could not compile `ownership` (bin "ownership") due to 1 previous error
    </code>
  </pre>
  <p>
    빌려오기 규칙을 기억해보세요. 만약 우리가 어떤 것에 대해 불변 참조를 가지고 있다면, 그 시점에서 가변 참조를 가질 수 없습니다. <code>clear</code>는 <code>String</code>을 잘라내야 하므로 가변 참조를 필요로 합니다. <code>clear</code> 호출 후의 println!은 <code>word</code>에 있는 참조를 사용하므로, 그 시점에서 불변 참조는 여전히 활성화되어야 합니다. Rust는 <code>clear</code>의 가변 참조와 <code>word</code>의 불변 참조가 동시에 존재하는 것을 허용하지 않으며, 이로 인해 컴파일이 실패합니다. Rust는 우리의 API를 사용하기 쉽게 만들었을 뿐만 아니라, 컴파일 타임에 오류를 방지할 수 있는 오류 종류 전체를 제거해주었습니다!
  </p>
</section>

<section>
  <h4>
    <a>문자열 리터럴 슬라이스(String Literals as Slices)</a>
  </h4>
  <p>
    문자열 리터럴이 바이너리 내부에 저장된다고 이야기했던 것을 기억하시죠? 이제 슬라이스에 대해 알게 되었으므로, 문자열 리터럴을 제대로 이해할 수 있습니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn main() {
        let s = "Hello, world!";
    }
  </code>
  <p>
    여기서 <code>s</code>의 타입은 <code>&amp;str</code>입니다. 이는 바이너리의 특정 지점을 가리키는 슬라이스입니다. 이것이 문자열 리터럴이 불변인 이유이기도 합니다. <code>&amp;str</code>은 불변 참조이기 때문입니다.
  </p>
</section>

<section>
  <h4>
    <a>문자열 슬라이스를 매개변수로 사용하기(String Slices as Parameters)</a>
  </h4>
  <p>
    문자열 리터럴과 <code>String</code> 값에서 슬라이스를 취할 수 있다는 사실을 알게 되면, <code>first_word</code>의 또 다른 개선점을 알게 되며, 그것은 바로 함수의 시그니처입니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;String) -&gt; &amp;str {
  </code>
  <p>
    더 숙련된 러스트 프로그래머는 Listing 4-9에 표시된 시그니처처럼 작성할 것입니다. 이 방식은 <code>&amp;String</code> 값과 <code>&amp;str</code> 값 모두에서 동일한 함수를 사용할 수 있게 해줍니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;str) -&gt; &amp;str {
        let bytes = s.as_bytes();
        </span>
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return &amp;s[0..i];
            }
        }
        </span>
        &amp;s[..]
    }
    </span>
    fn main() {
        let my_string = String::from("hello world");
        </span>
        // `first_word`는 `String`의 슬라이스에 대해서도 부분적이거나 전체적으로 작동합니다
        let word = first_word(&amp;my_string[0..6]);
        let word = first_word(&amp;my_string[..]);
        // `first_word`는 `String`에 대한 참조에서도 작동하며, 이는 `String`의 전체 슬라이스와 동일합니다
        let word = first_word(&amp;my_string);
        </span>
        let my_string_literal = "hello world";
        </span>
        // `first_word`는 문자열 리터럴의 슬라이스에 대해서도 부분적이거나 전체적으로 작동합니다
        let word = first_word(&amp;my_string_literal[0..6]);
        let word = first_word(&amp;my_string_literal[..]);
        </span>
        // 문자열 리터럴은 이미 문자열 슬라이스이기 때문에,
        // 슬라이스 문법 없이도 이 코드가 작동합니다!
        let word = first_word(my_string_literal);
    }
  </code>
  <p>
    <span>
      Listing 4-9: 매개변수 <code>s</code>의 타입으로 문자열 슬라이스를 사용하여 <code>first_word</code> 함수 개선하기
    </span>
  </p>
  <p>
    문자열 슬라이스가 있으면 그것을 직접 전달할 수 있습니다. <code>String</code>이 있으면, <code>String</code>의 슬라이스나 <code>String</code>에 대한 참조를 전달할 수 있습니다. 이 유연성은 역참조 강제(derefcoercions)를 활용하며, 이는 15장 "함수와 메서드에서의 암시적 역참조 강제" 섹션에서 다룰 예정입니다.
  </p>
  <p>
    <code>String</code> 참조대신 문자열 슬라이스를 받는 함수를 정의하면, 기능을 잃지 않으면서 API가 더 일반적이고 유용해집니다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021 editable">
    fn first_word(s: &amp;str) -&gt; &amp;str {
        let bytes = s.as_bytes();
        </span>
        for (i, &amp;item) in bytes.iter().enumerate() {
            if item == b' ' {
                return &amp;s[0..i];
            }
        }
        </span>
        &amp;s[..]
    }
    </span>
    fn main() {
        let my_string = String::from("hello world");
        // `first_word`는 `String`의 슬라이스에 대해서도 부분적이거나 전체적으로 작동합니다
        let word = first_word(&my_string[0..6]);
        let word = first_word(&my_string[..]);
        // `first_word`는 `String`에 대한 참조에서도 작동하며, 이는 `String`의 전체 슬라이스와 동일합니다
        let word = first_word(&my_string);

        let my_string_literal = "hello world";
        // `first_word`는 문자열 리터럴의 슬라이스에 대해서도 부분적이거나 전체적으로 작동합니다
        let word = first_word(&my_string_literal[0..6]);
        let word = first_word(&my_string_literal[..]);
        // 문자열 리터럴은 이미 문자열 슬라이스이기 때문에,
        // 슬라이스 문법 없이도 이 코드가 작동합니다!
        let word = first_word(my_string_literal);
    }
  </code>
</section>

<section>
  <h3>
    <a>다른 슬라이스들(Other Slices)</a>
  </h3>
  <p>
    문자열 슬라이스는 문자열에 특화된 것입니다. 하지만 더 일반적인 슬라이스 타입도 있습니다. 이 배열을 고려해봅시다.
  </p>
  <code class="language-rust edition2021 editable">
    fn main() {
        let a = [1, 2, 3, 4, 5];
    }
  </code>
  <p>
    문자열의 일부를 참조하고 싶을 때처럼, 배열의 일부를 참조하고 싶을 수 있습니다. 그럴 땐 아래와 같이 코드를 작성할 수 있습니다.
  </p>
  <code class="language-rust edition2021 editable">
    fn main() {
        let a = [1, 2, 3, 4, 5];
        let slice = &amp;a[1..3];
        assert_eq!(slice, &amp;[2, 3]);
    }
  </code>
  <p>
    이 슬라이스의 타입은 <code>&amp;[i32]</code>입니다. 문자열 슬라이스와 동일하게 작동하며, 첫 번째 요소에 대한 참조와 길이를 저장합니다. 이 유형의 슬라이스는 다른 종류의 컬렉션에서도 사용됩니다. 이러한 컬렉션에 대해선 8장 벡터에서 더 자세히 다룰 예정입니다.
  </p>
</section>

<section>
  <h2>
    <a>요약</a>
  </h2>
  <p>
    소유권, 빌려오기, 슬라이스의 개념은 Rust 프로그램에서 컴파일 타임에 메모리 안전성을 보장합니다. Rust 언어는 다른 시스템 프로그래밍 언어들처럼 메모리 사용에 대한 제어를 제공하지만, 데이터의 소유자가 범위 밖으로 나갈 때 자동으로 데이터를 정리하는 방식 덕분에 추가적인 코드 작성과 디버깅 없이 이 제어를 얻을 수 있습니다.
  </p>
  <p>
    소유권은 Rust의 많은 다른 부분에 영향을 미치므로, 우리는 이 개념들을 책의 나머지 부분에서 계속 다룰 것입니다. 이제 5장으로 넘어가서 데이터를 구조체로 묶는 방법을 살펴보겠습니다.
  </p>