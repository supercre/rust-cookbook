<section>
  <h2>
    <a>소유권(Ownership)이란 무엇인가?</a>
  </h2>
  <p>
    <strong>소유권(Ownership)</strong>이란 Rust 프로그램이 메모리를 관리하는 방식을 규정하는 일련의 규칙입니다.
  </p>
  <p>
    <ul>
      <li>
        모든 프로그램은 실행 중에 컴퓨터의 메모리를 관리해야 합니다.
      </li>
      <li>
        일부 언어는 가비지 컬렉션(garbage collection)을 사용하여 프로그램 실행 중 정기적으로 더 이상 사용되지 않는 메모리를 찾습니다.
      </li>
      <li>
        다른 언어에서는 프로그래머가 메모리를 명시적으로 할당하고 해제해야 합니다.
      </li>
    </ul>
    Rust는 위에서 소개한 방식 중 세 번째 접근 방식을 사용합니다. 메모리는 컴파일러가 확인하는 일련의 규칙을 가진 소유권 시스템을 통해 관리됩니다.</br>
    만약 이 규칙 중 하나라도 위반되면 프로그램은 컴파일되지 않으며, 소유권의 이점 중 어느 것도 프로그램 실행 중 성능을 저하시키지 않습니다.
  </p>
  <p>
    소유권(Ownership)은 많은 프로그래머에게 새로운 개념이기 때문에 익숙해지는 데 시간이 걸릴 수 있습니다.
    하지만 Rust와 소유권 시스템의 규칙에 익숙해질수록 안전하고 효율적인 코드를 자연스럽게 작성하는 것이 점점 더 쉬워지기 때문에 꾸준한 연습이 필요합니다.
  </p>
  <p>
    소유권(Ownership)을 이해하면 Rust를 독특하게 만드는 기능들을 이해하는 데 튼튼한 기반을 갖추게 됩니다.
    이 장에서는 <strong>문자열(String)</strong>을 중심으로 한 몇 가지 예제를 통해 소유권 개념을 배울 것입니다.
  </p>
</section>

<section>
  <h3>
    <a>스택과 힙(Stack and Heap)</a>
  </h3>
  <p>
    많은 프로그래밍 언어는 스택과 힙에 대해 자주 생각할 필요가 없습니다.<br/>
    그러나 Rust와 같은 시스템 프로그래밍 언어에서는 값이 스택에 있는지 힙에 있는지가 언어의 동작 방식에 영향을 미치며, 특정 결정을 내려야 하는 이유를 이해하는 데 중요합니다.<br/>
    소유권의 일부는 이후 장에서 스택과 힙과 관련하여 설명될 예정이므로, 이를 준비하기 위해 간단히 설명하겠습니다.
  </p>
  <p>
    <strong>스택과 힙</strong>은 프로그램 실행 시 코드가 사용할 수 있는 메모리의 일부이지만, 구조가 서로 다릅니다.<br/>
    스택은 데이터를 받은 순서대로 저장하고, 반대 순서로 제거합니다. 이를 후입선출(LIFO, Last In, First Out)이라고 부릅니다.
  </p>
  <p>
    이 과정에서, 데이터를 추가하는 것을 스택에 푸시(push), 데이터를 제거하는 것을 스택에서 팝(pop)이라고 합니다.
    스택에 저장되는 모든 데이터는 반드시 고정된 크기를 가져야 하며, 컴파일 시간에 크기를 알 수 없거나 크기가 변할 가능성이 있는 데이터는 대신 힙에 저장되어야 합니다.
  </p>
  <p>
    힙은 스택보다 덜 조직적입니다. 데이터를 힙에 저장할 때는 특정 크기의 공간을 요청합니다.</br>
    메모리 할당기(memory allocator)는 힙에서 충분히 큰 빈 공간을 찾아 해당 공간을 사용 중으로 표시하고, 그 위치의 <strong>포인터(pointer)</strong>, 즉 해당 주소를 반환합니다.</br>
    이 과정을 힙에 할당(allocating on the heap)이라고 하며, 간단히 할당(allocating)이라고도 부릅니다.</br>
    (스택에 값을 푸시하는 것은 일반적으로 할당으로 간주되지 않습니다.)</br>
    </br>
    힙의 포인터는 고정된 크기로 알려져 있기 때문에 스택에 저장할 수 있지만, 실제 데이터를 사용하려면 해당 포인터를 따라가야 합니다.
    이 과정을 식당에서 자리에 앉는 상황으로 비유할 수 있습니다. 식당에 들어갈 때 일행의 인원수를 말하면, 직원이 모두 앉을 수 있는 빈 테이블을 찾아 안내합니다.
    만약 일행 중 누군가 늦게 온다면, 직원에게 이미 앉아 있는 테이블의 위치를 물어 그 자리를 찾을 수 있습니다.
  </p>
  <p>
    스택에 데이터를 푸시하는 것은 힙에 데이터를 할당하는 것보다 빠릅니다.
    스택에서 저장 위치는 항상 맨 위에 있기 때문에, 할당기(allocator)가 새 데이터를 저장할 위치를 찾을 필요가 없기 때문입니다.<br>
    반면, 힙에선 공간을 할당하려면 할당기(allocator)가 데이터를 저장할 수 있을 만큼 충분히 큰 공간을 먼저 찾아야 하고,
    그다음 할당 준비를 위한 작업(기록 관리 등)을 수행해야 하므로 더 많은 작업이 필요합니다.
  </p>
  <p>
    힙의 데이터에 접근하는 것은 스택의 데이터에 접근하는 것보다 느린데, 힙의 데이터에 접근하려면 포인터를 따라가야 하기 때문입니다.
  </p>
  <p>
    이를 식당의 서버가 여러 테이블의 주문을 받을 때의 상황으로 비유할 수 있습니다.
    한 테이블의 모든 주문을 한꺼번에 받고 나서 다음 테이블로 이동하는 것이 가장 효율적입니다.
    테이블 A에서 주문을 하나 받은 뒤, 테이블 B로 이동해 주문을 받고 다시 A로 돌아가는 식이라면 훨씬 느려질 것입니다.
    마찬가지로, 프로세서는 스택처럼 데이터가 서로 가까이 있는 경우(연속적인 경우)에 더 효율적으로 작업할 수 있고,
    힙처럼 데이터가 떨어져 있는 경우에는 속도가 느려집니다.
  </p>
  <p>
    코드가 함수를 호출할 때, 함수로 전달되는 값(힙 데이터의 포인터도 포함될 수 있음)과 함수의 지역 변수는 스택에 푸시됩니다.
    함수 실행이 종료되면 이러한 값들은 스택에서 팝됩니다.
  </p>
  <p>
    힙에서 어떤 코드 부분이 어떤 데이터를 사용하는지 추적하고, 힙에 있는 중복 데이터를 최소화하며,
    사용하지 않는 데이터를 정리하여 메모리 부족 상황을 방지하는 문제는 <strong>소유권(ownership)</strong>이 해결합니다.
    소유권을 이해하면 스택과 힙에 대해 자주 생각할 필요는 없겠지만,
    소유권의 주요 목적이 힙 데이터를 관리하는 것임을 이해하면 소유권의 작동 방식이 더 잘 이해될 것입니다.
  </p>
</section>

<section>
  <h3>
    <a>소유권 규칙(Ownership Rules)</a>
  </h3>
  <p>
    먼저, <strong>소유권(ownership)의 규칙</strong>을 살펴보겠습니다.
    이 규칙들을 염두에 두고, 설명하는 예제를 함께 살펴보겠습니다.
  </p>
  <ul>
    <li>
      Rust에서 각각의 값들은 <strong>소유자(owner)</strong>를 가집니다.
    </li>
    <li>
      소유자(owner)는 한번에 하나만 존재할 수 있습니다.
    </li>
    <li>
      소유자(owner)가 범위를 벗어나면, 그 값은 <strong>삭제(dropped)</strong>됩니다.
    </li>
  </ul>
</section>

<section>
  <h3>
    <a>변수 범위(Variable Scope)</a>
  </h3>
  <p>
    지금까지 Rust의 소유권에 대한 내용을 이론적으로 배웠으므로, 아래 예제를 통해 실습하는 시간을 가져보겠습니다.
  </p>
  <p>
    우리는 기본적인 Rust 문법을 알고 있으므로, 더 이상 예제에서 <code>fn main()</code>과 같은 코드를 모두 포함하지 않겠습니다. 따라서, 예제를 실행할 때, 아래 예제 들을 직접 <code>fn main()</code>과 같이 함수 안에 넣어서 실행부탁드립니다.
  </p>
  <p>
    소유권(ownership)의 첫 번째 예제로, 몇 가지 변수의 범위를 살펴보겠습니다.</br>
    변수 범위란 프로그램 내에서 변수가 유효한 범위를 나타냅니다. 다음 예시들을 통해 변수 범위에 대해 알아봅시다.
  </p>
  <p>
    <code class="language-rust edition2021">
      let s = "hello";
    </code>
  </p>
  <p>
    위 예제에서 변수 <code>s</code>는 문자열 리터럴을 참조하며, "hello"라는 값으로 하드코딩되어 있습니다.</br>
    이 변수는 선언된 시점부터 현재 <strong>범위(scope)</strong>의 끝까지 유효합니다.</br>
    예제 4-1에서는 변수 <code>s</code>가 유효한 위치를 주석으로 표시하여 변수 범위를 표현합니다.
  </p>
  <p>
    <code class="language-rust edition2021">
      {                      // 변수 s는 아직 정의되지 않았기에 유효하지 않습니다.
        let s = "hello";     // 변수 s는 이 시점부터 유효합니다.
                             // 변수 s와 함께 작업을 진행합니다.
      }                      // 이 범위를 벗어나면, s는 더 이상 유효하지 않습니다.
    </code>
  </p>
  <span>
    Listing 4-1: 변수와 변수가 유효한 범위
  </span>
  <p>
    다시 말해, 여기에는 두 가지 중요한 내용이 존재합니다.
  </p>
  <ul>
    <li>
      변수 <code>s</code>가 <strong>범위에 들어올 때,</strong> 유효합니다.
    </li>
    <li>
      이것은 변수 <code>s</code>가 <strong>범위를 벗어날 때</strong>까지 유효함을 의미합니다.
    </li>
  </ul>
  <p>
    이 시점에서, 변수의 유효성과 범위의 관계는 다른 프로그래밍 언어들과 비슷합니다.</br>
    이제 우리는 이 내용을 바탕으로 <code>String</code>타입을 소개하며 진행하겠습니다.
  </p>
</section>

<section>
  <h3>
    <a><code>String</code> 타입</a>
  </h3>
  <!-- 나중에 "데이터 타입" 문자에 3장 데이터 타입을 링크 걸어둘 것 -->
  <!-- 나중에 "Chapter 8" 문자에 8장 링크 걸어둘 것 -->
  <p>
    소유권 규칙을 설명하기 위해서는 3장에서 다룬 <a href="ch03-02-data-types.html#data-types">데이터 타입”</a>의 내용보다 더 복잡한 데이터 타입이 필요합니다.</br>
    이전에 다룬 타입들은 크기가 정해져 있고, 스택에 저장되어 범위가 끝나면 스택에서 팝될 수 있습니다.<br> 
    따라서, 다른 코드에서 같은 값을 다른 범위에서 사용할 필요가 있을 때 새로운 독립적인 인스턴스를 빠르고 쉽게 복사할 수 있습니다.</br>
    하지만, 우리는 힙에 저장되는 데이터를 살펴보고, Rust가 그 데이터를 언제 정리하는지에 대한 내용을 다루고자 합니다.
  </p>
  <p>
    우리는 <string>소유권(ownership)</string>과 관련된 내용을 보다 깊이 이해하기 위해 <code>String</code> 변수를 집중적으로 사용할 예정입니다.</br>
    <code>String</code>에 대해서는 <a href="ch08-02-strings.html">8장”</a>에서 더 깊이 다룰 예정입니다.
  </p>
  <p>
    우리는 이미 문자열 리터럴을 본 적이 있습니다. 문자열 리터럴은 문자열 값이 프로그램에 하드코딩되어 있는 형태입니다. 문자열 리터럴은 편리하지만, 모든 상황에서 사용할 수 있는 것은 아닙니다.</br>
    그 이유 중 하나는 문자열 리터럴이 <strong>불변(immutable)</strong>이라는 점 때문입니다. 또 다른 이유로 모든 문자열 값이코드를 작성할 때 알 수 있는 값이 <strong>아니라는 점</strong>입니다.</br>
    예를 들어, 사용자 입력을 받아서 저장하고 싶을 때, 우린 어떻게 해야 할까요?</br>
    이러한 상황을 위해 Rust에는 두 번째 문자열 타입인 <code>String</code> 타입이 있습니다! 이 타입은 힙에 할당된 데이터를 관리하며, 컴파일 타임에 우리가 알 수 없는 크기의 텍스트를 저장할 수 있습니다. 문자열 리터럴에서 <code>String</code>을 생성하려면 <code>from</code> 함수를 사용하면 됩니다.
  </p>
  <p>
    <code class="language-rust edition2021">
      fn main() {
        let s = String::from("hello");
      }
    </code>
  </p>
  <p>
    더블 콜론 <code>::</code> 연산자는 특정 <code>from</code> 함수를 <code>String</code> 타입의 네임스페이스 아래에 두는 역할을 합니다. 이렇게 함으로써 <code>string_from</code>과 같은 이름을 사용하는 대신에, 타입에 속한 함수로 명확하게 구분할 수 있습니다.
  </p>
  <p>
    이 구문에 대해선 5장의 <a href="ch05-03-method-syntax.html#method-syntax">“메소드 문법(Method Syntax)”</a> 섹션에서 더 자세히 다룰 것이며, 7장의 <a href="ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html">“모듈 트리에서 항목을 참조하는 경로”</a>에서 모듈을 통한 네임스페이싱에 대해 설명할 때도 다룰 것입니다.
  </p>
  <p>
    아래 예제에선 <string>변경</string>이 가능한 문자열 예시를 볼 수 있습니다.
  </p>
  <p>
    <code class="language-rust edition2021">
      fn main() {
        let mut s = String::from("hello");
        s.push_str(", world!"); // push_str() 문자열 리터럴을 String에 추가합니다.
        println!("{s}"); // "hello, world!"가 출력됩니다.
      }
    </code>
  </p>
  <p>
    여기서 차이점은 무엇일까요? 왜 <code>String</code>은 변경할 수 있지만, 리터럴은 변경할 수 없는 걸까요? 그 차이점은 두 타입이 메모리를 처리하는 방식에 있습니다.
  </p>
</section>

<section>
  <h3>
    <a>메모리와 할당(Memory and Allocation)</a>
  </h3>
  <p>
    문자열 리터럴의 경우, 컴파일 시 내용을 알 수 있기 때문에 텍스트가 최종 실행 파일에 하드코딩됩니다.</br>
    이 때문에 문자열 리터럴은 빠르고 효율적입니다. 하지만 이러한 특성은 문자열 리터럴이 <strong>불변(immutable)</strong>이기에 가능한 것입니다. 
    불행히도 컴파일 타임에 크기를 알 수 없고, 실행 중 크기가 변경될 수 있는 각 텍스트 조각에 대해 메모리 블롭을 바이너리에 넣을 순 없습니다.
  </p>
  <p>
    <code>String</code> 타입의 경우, 변경 가능하고 크기가 늘어날 수 있는 텍스트를 지원하려면 컴파일 시 크기가 알려지지 않은 힙에 메모리를 할당해야만 합니다.
    즉, 다음과 같은 사항이 필요합니다.
  </p>
  <ul>
    <li>
      메모리는 실행 중 메모리 할당자에게 요청해야 합니다.
    </li>
    <li>
      더 이상 사용하지 않는 <code>String</code> 타입의 메모리를 할당자(allocator)에게 반환하는 방법이 필요합니다.
    </li>
  </ul>
  <p>
    첫 번째 <strong>"메모리는 실행 중 메모리 할당자에게 요청해야 합니다."</strong> 부분은 프로그래머가 처리하는 부분으로, <code>String::from</code>을 호출하여 메모리를 요청합니다. 이는 대부분의 프로그래밍 언어에서 보편적인 방식입ㄴ다.
  </p>
  <p>
    하지만, 두번째 <strong>"더 이상 사용하지 않는 <code>String</code> 타입의 메모리를 할당자(allocator)에게 반환하는 방법이 필요합니다."</strong> 부분은 다릅니다.</br>
    가비지 컬렉터(GC)가 있는 언어에서는 GC가 더 이상 사용되지 않는 메모리를 추적하고 정리해주기 때문에 프로그래머는 이를 신경 쓸 필요가 없습니다.</br>
    대부분의 GC가 없는 언어에서는, 메모리가 더 이상 사용되지 않는 시점을 프로그래머가 식별하고, 메모리를 명시적으로 해제하는 코드를 호출해야만 합니다.</br>
    이 작업을 정확하게 수행하는 것은 역사적으로 어려운 프로그래밍 문제였습니다. 만약 이를 잊으면 메모리가 낭비되고, 너무 일찍해제하면 유효하지 않은 변수가 생기는 등의 문제가 발생합니다.</br>
    두 번 해제할 경우 버그가 발생하며, 정확히 하나의 <code>allocate</code>와 <code>free</code>를 맞춰야 합니다.
  </p>
  <p>
    Rust는 이 문제에 대해 다른 방식으로 접근합니다. 메모리는 변수를 소유한 값이 범위를 벗어날 경우 자동으로 반환됩니다. 여기 <code>String</code>을 사용한 예제의 범위 예시를 다시 보여드리겠습니다.
  </p>
  <p>
    <code class="language-rust edition2021">
      fn main(){
        let s = String::from("hello");  // 변수 s는 이 시점부터 유효합니다.
                                        // 변수 s를 활용하여 몇몇 작업을 진행합니다.
      }                                 // 이 시점부터 변수 s는 더 이상 유효하지 않습니다.
    </code>
  </p>
  <p>
    우리는 이 예제를 통해 <code>String</code>의 메모리 반환이 자연스럽게 이루어지는 시점을 알 수 있습니다.</br>
    바로, 변수 <code>s</code>가 범위를 벗어날 때 입니다. 변수가 범위를 벗어나면 Rust는 특별한 함수를 호출하는데, 이 함수가 바로 <code>drop</code>이며, 이곳에서 <code>String</code>의 메모리를 반환하는 코드를 넣을 수 있습니다.
  </p>
  <p>
    <i class="fa fa-info-circle" aria-hidden="true">
      Note: C++에서는 항목의 수명이 끝날 때 자원을 해제하는 이 패턴을 Resource Acquisition Is Initialization (RAII)라고 부르기도 합니다. Rust의 <code>drop</code> 함수는 RAII 패턴을 사용해본 사람에게는 익숙할 것입니다.
    </i>
  <p>
    이 패턴은 Rust 코드 작성 방식에 깊은 영향을 미칩니다. 지금은 간단해 보일 수 있지만, 힙에 할당한 데이터를 여러 변수가 사용할 때와 같은 복잡한 상황에서는 코드의 동작이 예상치 못하게 변할 수 있습니다. 이제 이러한 복잡한 상황들을 살펴보고 다뤄보는 시간을 가지겠습니다.
  </p>
</section>

<section>
  <h4>
    <a>Move를 사용한 변수와 데이터 간 상호작용</a>
  </h4>
  <p>
    Rust에서 변수와 데이터가 상호작용하는 방식에는 여러 가지가 있습니다. Listing 4-2를 통해 정수를 사용하는 예시를 보겠습니다.
  </p>
    <code class="language-rust edition2021">
      let x = 5;
      let y = x;
    </code>
  <p>
    <span>
      Listing 4-2: 정수 <code>x</code>의 값을 <code>y</code>에 할당하기
    </span>
  </p>
  <p>
    우리는 아마도 이것이 값 <code>5</code>를 변수 <code>x</code>에 바인딩한 후, <code>x</code>의 값을 복사해서 <code>y</code>에 바인딩한다는 의미라고 추측할 수 있습니다.</br>
    실제로 <code>x</code>와 <code>y</code>라는 두 변수는 모두 5와 같은 값을 가지게 됩니다. 정수는 고정된 크기와 알려진 크기를 가진 간단한 값이기 때문에, 이 두 개의 값은 스택에 푸시됩니다. 
  </p>
  <p>
    이제 <code>String</code> 타입을 예로 들어보겠습니다.
  </p>
  <code class="language-rust edition2021">
    let s1 = String::from("hello");
    let s2 = s1;
  </code>
  <p>
    이 코드도 방금 전 정수 타입을 설명한 것과 비슷해 보이므로, 우리는 코드 두 번째 줄에서 <code>s1</code>의 값을 복사하여, <code>s2</code>로 바인딩 될 것이라고 생각할 수 있습니다. 하지만 우리가 생각한 것은 실제로 이루어지지 않습니다.
  </p>
</section>

<section>
  <h4>
    <a><code>String</code>내부에서 일어나는 일</a>
  </h4>
  <p>
    아래 그림 4-1을 보면 <code>String</code>이 내부에서 어떻게 동작하는지 알 수 있습니다. 그림 왼쪽에서 볼 수 있듯이, <code>String</code>은 문자열 내용이 저장된 메모리를가리키는 포인터, 길이 그리고 용량으로 구성됩니다. 이 데이터들은 스택에 저장되며, 그림 오른쪽의 문자열은 실제 내용이 저장된 힙 메모리입니다.
  </p>
  <p>
    <img alt="Two tables: the first table contains the representation of s1 on the
    stack, consisting of its length (5), capacity (5), and a pointer to the first
    value in the second table. The second table contains the representation of the
    string data on the heap, byte by byte." src="img/trpl04-01.svg" class="center" style="width: 50%;"/>
  </p>
  <p>
    <span>
      그림 4-1: <code>"hello"</code>라는 값을 가지는 <code>String</code> 타입을 <code>s1</code> 변수에 바인딩한 메모리를 표현
    </span>
  </p>
  <p>
    길이는 문자열이 현재 몇 바이트의 메모리를 사용하고 있는지를 나타내고, 용량은 <code>String</code>이 할당받은 총 메모리 크기깁니다. 길이와 용량의 차이는 중요한 요소이지만, 이 예시에서는 무시해도 괜찮습니다.
  </p>
</section>

<section>
  <h4>
    <a><code>String</code>의 할당</a>
  </h4>
  <p>
    <code>s1</code>을 <code>s2</code>에 할당할 때, 실제 데이터는 복사되지 않고, 스택에 있는 포인터, 길이, 용량만 복사됩니다.</br>
    즉, 힙에 있는 문자열 데이터는 복사되지 않으며, 이 상황은 그림 4-2처럼 메모리에서 표현됩니다.
  </p>
  <p>
    <img alt="Three tables: tables s1 and s2 representing those strings on the
    stack, respectively, and both pointing to the same string data on the heap." src="img/trpl04-02.svg" class="center" style="width: 50%;"/>
  </p>
  <p>
    <span>
      그림 4-2: 변수 <code>s2</code>가 변수 <code>s1</code>의 포인터, 길이 그리고 용량을 복사한 후의 메모리 표현
    </span>
  </p>
  <p>
    이러한 표현은 그림 4-3과 같지 않습니다. 그림 4-3은 만약 Rust가 힙 데이터까지 복사했다면, 메모리가 어떻게 보일지를 설명합니다. 만약 Rust가 이처럼 데이터를 복사했다면, <code>s2 = s1</code>의 작업이 매우 비쌀 수 있습니다. 왜냐하면, 힙에 있는 데이터가 매우 클 경우 성능에 큰 영향을 줄 수 있기 때문입니다.
  </p>
  <p>
    <img alt="Four tables: two tables representing the stack data for s1 and s2,
    and each points to its own copy of string data on the heap." src="img/trpl04-03.svg" class="center" style="width: 50%;"/>
  </p>
  <p>
    <span>
      그림 4-3: Rust가 힙 데이터를 복사할경우 <code>s2 = s1</code>이 수행할 수 있는 또 다른 가능성
    </span>
  </p>
  <p>
    앞서 말했듯이, 변수의 범위(scope)가 끝날 때, Rust는 자동으로 <code>drop</code>함수를 호출하여 힙 메모리를 정리합니다.</br>
    그러나 그림 4-2에서 보여지는 것처럼, 두 개의 포인터가 같은 위치를 가리키고 있을 때 <code>s1</code>과 <code>s2</code> 모두 범위를 벗어나게 된다면, 두 변수는 같은 메모리를 해제하려고 시도하게 될 것입니다.</br>
    이를 <strong>더블 프리(double free)</strong> 오류라고 하며, 메모리 손상을 초래할수 있고, 보안 취약점을 초래할 수도 있습니다.
  </p>
  <p>
    메모리 안정성을 보장하기 위해 Rust는 <code>let s2 = s1;</code> 라인이 실행된 이후 <code>s1</code> 을 더 이상 유효하지 않은 변수로 간주합니다. 따라서, <code>s1</code>이 범위를 벗어나도 Rust는 아무것도 해제할 필요가 없습니다.</br>
    <code>s2</code>가 생성된 이후 <code>s1</code>를 사용하려고 시도하면 어떤 문제가 발생하는지 다음 코드를 통해 실험해보세요.
  </p>
  <code class="language-rust edition2021">
    let s1 = String::from("hello");
    let s2 = s1;
    println!("{s1}, world!");
  </code>

  <p>
    위 코드를 실행하면, 아래와 같은 오류가 발생할 것입니다. 이는 Rust가 무효화된 참조를 사용하는 것을 방지하기 때문입니다.
  </p>
  <pre>
    <code class="language-console">$ cargo run
      Compiling ownership v0.1.0 (file:///projects/ownership)
      error[E0382]: borrow of moved value: `s1`
      --&gt;src/main.rs:5:15
      |
      2 |     let s1 = String::from("hello");
      |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
      3 |     let s2 = s1;
      |              -- value moved here
      4 |
      5 |     println!("{s1}, world!");
      |               ^^^^ value borrowed here after move
      |
      = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
      help: consider cloning the value if the performance cost is acceptable
      |
      3 |     let s2 = s1.clone();
      |                ++++++++
      For more information about this error, try `rustc --explain E0382`.
      error: could not compile `ownership` (bin "ownership") due to 1 previous error
    </code>
  </pre>
  <p>
    I다른 언어에서 얕은 복사와 깊은 복사라는 용어를 들어본 적이 있다면, 데이터를 복사하지 않고 포인터, 길이, 용량만 복사하는 개념은 아마 얕은 복사를 만드는 것처럼 들릴 것입니다.</br>
    그러나 Rust는 첫 번째 변수를 무효화하기 때문에, 이는 얕은 복사가 아닌 <strong>이동(move)</strong>이라고 불립니다.</br>
    이 예시에서는 <code>s1</code>이 <code>s2</code>로 이동되었다고 말할 수 있습니다. 실제로 일어나는 일은 그림 4-4에 나와있습니다.
  </p>
  <p>
    <img alt="Three tables: tables s1 and s2 representing those strings on the
    stack, respectively, and both pointing to the same string data on the heap.
    Table s1 is grayed out be-cause s1 is no longer valid; only s2 can be used to
    access the heap data." src="img/trpl04-04.svg" class="center" style="width:
    50%;"/>
  </p>
  <p>
    <span>
      그림 4-4: <code>s1</code>이 무효화된 후 메모리에서의 표현
    </span>
  </p>
  <p>
    이제 문제를 해결했습니다! <code>s2</code>만 유효하므로, 범위를 벗어날 때 메모리를 해제하고 끝이 납니다.
  </p>
  <p>
    또한, 이로 인해 암묵적으로 내포된 디자인 선택이 있습니다. <strong>Rust는 데이터를 자동으로 깊은 복사하지 않습니다.</strong></br>
    따라서, 모든 자동 복사는 실행시간 성능 측면에서 비용이 적다고 가정할 수 있습니다.
  </p>
</section>

<section>
  <h4>
    <a><code>Clone</code>을 사용한 변수와 데이터 간 상호작용</a>
  </h4>
  <p>
    만약 <code>String</code>의 스택 데이터뿐만 아니라 힙 데이터를 깊게 복사하고 싶다면, <code>clone</code>이라는 일반적인 메서드를 사용할 수 있습니다. 메서드 문법에 대해선 5장에서 다룰 예정이지만, 메서드는 많은 프로그래밍 언어에서 공통된 기능이므로, 이미 보았을 가능성이 높습니다.
  </p>
  <p>
    다음은 <code>clone</code> 메서드가 동작하는 예시입니다.
  </p>
  <p>
    <code class="language-rust edition2021">
      fn main() {
        let s1 = String::from("hello");
        let s2 = s1.clone();
        println!("s1 = {s1}, s2 = {s2}");
      }
    </code>
  </p>
  <p>
    이 방식은 힙 데이터가 복사되는 그림 4-3에서 보여준 동작을 명시적으로 생성합니다.
  </p>
  <p>
    <code>clone</code>을 호출하는 것을 보면, 어떤 임의의 코드가 실행되고 있고 그 코드가 비용이 많이 들 수 있다는 것을 알 수 있습니다. 이는 무언가 다른 일이 일어나고 있음을 나타내는 시각적인 지표입니다.
  </p>
</section>

<section>
  <h4>
    <a>스택 전용 데이터: <code>Copy</code></a>
  </h4>
  <p>
    우리가 아직 이야기하지 않은 또 다른 복잡한 점이 있습니다. 정수와 같은 코드는  (Listing 4-2에 나왔던 코드) 잘 작동하며 유효합니다.
  </p>
  <p>
    <code class="language-rust edition2021">
      fn main() {
        let x = 5;
        let y = x;
        println!("x = {x}, y = {y}");
      }
    </code>
  </p>
  <p>
    하지만 이 코드는 우리가 방금 배운 내용과는 모순되는 점이 존재합니다. <code>clone</code>을 호출하지 않았지만, <code>x</code>는 여전히 유효하며 <code>y</code>로 이동되지않았습니다.
  </p>
  <p>
    그 이유는 정수와 같은, 컴파일 타임에 크기가 알려진 타입들은 스택에 완전히 저장되기 때문입니다.</br>
    그래서 실제 값을 복사하는 작업이 빠르게 이루어지며, 이는 <code>x</code>가 <code>y</code> 변수에 할당된 후에도 유효한 상태를 유지하도록 하는 이유입니다.</br>
    다시 말해, 여기서는 깊은 복사와 얕은 복사 간 차이가 없기 때문에, <code>clone</code>을 호출해도 보통의 얕은 복사와 다를 것이 없으며, 이를 생략할 수 있습니다.
  </p>
  <p>
    Rust에는 <code>Copy</code>라는 특별한 애너테이션이 있습니다. 이는 정수와 같은 스택에 저장되는 타입에 적용할 수 있습니다.</br>
    만약 타입이 <code>Copy</code> 트레이트를 구현한다면, 그 변수를 이동시키지 않고 복사하게 되어, 다른 변수에 할당된 후에도 여전히 유효할 것입니다.
  </p>
  <p>
    Rust는 타입이나 그 일부가 <code>Drop</code> 트레이트를 구현한 경우 <code>Copy</code> 애너테이션을 추가할 수 없게 합니다.</br>
    만약 값이 범위를 벗어난 경우에 대한 특별한 처리가 필요할 때, <code>Copy</code> 애너테이션을 추가하면 컴파일 오류가 발생합니다.</br>
    <code>Copy</code> 트레이트를 구현하는 방법에 대해선 부록 C의 "파생 가능한 트레이트"에서 확인할 수 있습니다.
  </p>
  <p>
    그렇다면, 어떤 타입들이 <code>Copy</code> 트레이트를 구현할까요? 특정 타입에 대한 문서를 확인하면 확실히 알 수 있지만, 일반적으로 간단한 스칼라 값들의 집합은 <code>Copy</code>를 구현할 수 있고, 할당이나 리소스가 필요한 타입은 <code>Copy</code>를 구현할 수 없습니다.</br>
    <code>Copy</code>를 구현하는 몇 가지 타입은 다음과 같습니다.
  </p>
  <ul>
    <li>
      <code>u32</code>와 같은 모든 정수 타입
    </li>
    <li>
      <code>bool</code> 타입 (값은 <code>true</code>와 <code>false</code>)
    </li>
    <li>
      <code>f64</code>와 같은 모든 부동소수점 타입
    </li>
    <li>
      <code>char</code> 타입
    </li>
    <li>
      모든 튜플, 만약 그 안에 포함된 타입들이 모두 <code>Copy</code>를 구현한 경우.
      예를 들어, <code>(i32, i32)</code>는 <code>Copy</code>를 구현하지만, <code>(i32, String)</code>은 구현하지 않습니다.
    does not.
    </li>
  </ul>
</section>

<section>
  <h3>
    <a>소유권과 함수</a>
  </h3>
  <p>
    값을 함수에 전달하는 메커니즘은 변수를 할당하는 방식과 유사합니다. 변수를 함수에 전달하는 것도 할당과 마찬가지로 값을 이동시키거나 복사하게 됩니다. Listing 4-3에서는 변수들이 범위에 들어가고 나가는 위치를 주석으로 표시하였습니다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021">
    fn main() {
      let s = String::from("hello");  // 변수 s가 범위 내에 들어오는 시점
      takes_ownership(s);             // s가 함수로 이동
                                      // s의 값이 함수로 이동되었기 때문에 이 시점에선 유효하지 않음.
      let x = 5;                      // 변수 x가 범위 내에 들어오는 시점
      makes_copy(x);                  // x가 함수로 이동
      // i32는 copy 트레이트를 구현하므로, 여전히 유효하게 사용 가능
      // 이후에도 x를 계속 사용 가능
      } // 여기서 x는 먼저 범위를 벗어나고, 그 후에 s가 범위를 벗어나게 됩니다.
        // 그러나 s의 값은 이동되었기 때문에, 아무것도 해제되지 않습니다.
      fn takes_ownership(some_string: String) { // some_string 변수가 범위 내에 들어오는 시점
      println!("{some_string}");
      } // 여기서 some_string이 범위를 벗어나고 drop이 호출되어 백업 메모리가 해제됩니다.
      fn makes_copy(some_integer: i32) { // some_integer 변수가 범위 내에 들어오는 시점
      println!("{some_integer}");
    } // 여기서 some_integer가 범위를 벗어나며, 특별한 일은 일어나지 않습니다.
  </code>
  <p>
    <span>
      Listing 4-3: 소유권과 범위가 주석 처리된 함수들
    </span>
  </p>
  <p>
    만약 <code>takes_ownership</code> 호출 후에 <code>s</code>를 사용하려고 한다면, Rust는 컴파일 오류를 발생시킵니다. 이러한 정적 검사는 실수로부터 프로그래머를 보호합니다.</br>
    <code>main</code> 함수에 <code>s</code>와 <code>x</code>를 사용하는 코드를 추가하여, 어디에서 사용할 수 있고, 소유권 규칙이 무엇을 막는지 확인해보세요.
  </p>
</section>

<section>
  <h3>
    <a>반환 값과 범위</a>
  </h3>
  <p>
    값을 반환하는 것도 소유권을 이동시킬 수 있습니다. Listing 4-4는 값을 반환하는 함수의 예시로, Listing 4-3과 유사한 주석이 포함되어 있습니다.
  </p>
  <p>
    <span>
      파일 명: src/main.rs
    </span>
  </p>
  <code class="language-rust edition2021">
    fn main() {
      let s1 = gives_ownership(); // gives_ownership 함수는 반환 값을 s1으로 이동시킵니다.
      let s2 = String::from("hello"); // s2 변수가 범위에 들어오는 시점
      let s3 = takes_and_gives_back(s2); // s2 변수가 takes_and_gives_back 함수로 이동하며 반환 값이 s3으로 이동됩니다.
      } // 여기서 s3은 범위를 벗어나게 되어 드롭(drop)됩니다.
        // s2는 이동되었으므로 아무 일도 일어나지 않습니다.
        // s1은 범위를 벗어나고 드롭(drop)됩니다.
      fn gives_ownership() -&gt;String { // gives_ownership은 반환 값을 호출한 함수로 이동시킵니다.
        let some_string = String::from("yours"); // some_string 변수가 범위에 들어오는 시점
        some_string // some_string은 반환되어 호출한 함수로 이동합니다.
      }
      // 이 함수는 String을 받아서 String을 반환합니다.
      fn takes_and_gives_back(a_string: String) -&gt;String { // a_string 변수가 범위에 들어오는 시점
      a_string  // a_string은 반환되어 호출한 함수로 이동합니다.
    }
  </code>
  <p>
    <span>
    Listing 4-4: 반환 값의 소유권 이동
    </span>
  </p>
  <p>
    변소의 소유권은 항상 동일한 방식을 따릅니다. 값을 다른 변수에 할당하면 소유권이 이동합니다. 힙에 데이터를 포함하는 변수가 범위를 벗어나면, 그 값은 <code>drop</code>에 의해 정리되며, 데이터의 소유권이 다른 변수로 이동하지 않았다면 메모리가 해제됩니다.
  </p>
  <p>
    이 방식은 잘 작동하지만, 매번 소유권을 취하고 반환하는 것은 조금 번거롭습니다. 만약 함수가 값을 사용하되, 소유권을 가져가지 않게 하고 싶다면 어떻게 해야 할까요? 값을 전달할 때, 함수 내에서 사용할 때뿐만 아니라 값을 다시 사용할 수 있도록 반환해야하는 것도 불편합니다. 함수의 본문에서 생성되는 데이터와 함께 반환할 때도 마찬가지입니다.
  </p>
  <p>
    Rust는 Listing 4-5와 같이 튜플을 사용해 여러 값을 반환할 수 있습니다.
  </p>
  <p>
    <span>파일 명: src/main.rs</span>
  </p>
  <code class="language-rust edition2021">
  fn main() {
    let s1 = String::from("hello");
    let (s2, len) = calculate_length(s1);
    println!("The length of '{s2}' is {len}.");
  }
    fn calculate_length(s: String) -&gt;(String, usize) {
      let length = s.len(); // len()은 string의 길이를 반환합니다.
      (s, length)
  }
  </code>
  <p>
  <span>
    Listing 4-5: 매개변수의 소유권 반환
  </span>
  </p>
  <p>
    하지만 이것은 너무 번거롭고 일반적으로 자주 사용되는 개념에 비해 너무 많은 작업을 요구합니다. 다행히도 Rust에는 소유권을 이동시키지 않고 값을 사용할 수 있는 기능인 <strong>참조(references)</strong>가 있습니다.
  </p>
</section>