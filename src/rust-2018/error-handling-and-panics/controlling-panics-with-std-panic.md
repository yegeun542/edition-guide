# `std::panic`으로 panic! 컨트롤 하기

![Minimum Rust version: 1.9](https://img.shields.io/badge/Minimum%20Rust%20Version-1.9-brightgreen.svg)

러스트에는 `std::panic`모듈이 있는데요, 이 모듈에는 panic으로부터 시작된 stack unwinding을 멈추는 method들이 들어 있습니다. 

```rust
use std::panic;

let result = panic::catch_unwind(|| {
    println!("hello!");
});
assert!(result.is_ok());

let result = panic::catch_unwind(|| {
    panic!("oh no!");
});
assert!(result.is_err());
```

일반적으로, 러스트는 어떠한 작업이 실패하게 되는 경우를 두 가지로 분류합니다:

- *사전에 예상된 문제*로 인한 경우, 예를 들어, 파일이 발견되지 않는 경우죠.
- *예측되지 않은 문제*로 인한 경우, 예를 들면, 배열의 잘못된 인덱스를 참조하는 경우죠. 

사전에 예상되었던 문제들은 주로 여러분의 프로그램이 통제할 수 있는 범위 밖에서 나타나는 문제들입니다; 잘 짜여진 프로그램은 이러한 종류의 에러에 잘 대응합니다. 러스트에서 이러한 예측가능한 에러들은 함수로 하여금 호출한 쪽에 어떠한 문제가발생했는지에 대한 정보를 넘겨줄 수 있는 [`Result` 타입][result]을 통해서 처리할 수 있습니다. 만약 어떠한 함수를 실행하고 오류가 리턴되면 그것에 맞는 처리를 해 주면 되지요. 

[result]: http://doc.rust-lang.org/std/result/index.html

사전에 예측되지 않은 에러들은 *버그*입니다: 이것들은 주로 assertion이나 contract가 violated되면서 나타납니다. 이러한 종류의 에러들은 사전에 예측가능하지 않기 때문에, 아까와 같은 처리 방법으로 이들을 처리할 수는 없습니다. 대신에, 러스트는 *panic*이라는 것을 씁니다. (panic은 기본적으로 stack unwind를 합니다, 오류가 발생한 thread의 destructor는 실행 하지만 다른 어떠한 코드도 실행하지 않습니다.) 오류가 발생하지 않은 다른 thread들은 계속 실행됩니다. 그렇지만 이러한 thread들이 channel 이나 shared memory를 통해 panic이 발생한 thread와 상호작용하는 경우 그 thread도 panic하게 됩니다. 따라서 panic은 panic이 발생한 부분과 직접적으로 연결되어 있지 않은 어떠한 지점까지 연쇄적으로 발생합니다. 그 단절되어 있는 부분은 계속 실행이 가능하고 어쩌면 이 부분이 panic으로부터 recover할 수도 있습니다. 예를 들어, 우리가 서버를 운용하고 있다고 생각해보세요. 이 경우에, 많은 thread들 중에 하나의 thread에서 panic이 발생했다고 해서 전체 서버가 동작을 멈출 필요는 없습니다. 

또한 프로그램들이 unwind하는 대신에 *abort*할 수 있다는 것도 알아두세요, 이 경우엔 panic을 잡아내려 하는 것이 불가능할 수도 있습니다. 만약 여러분의 코드가 `catch_unwind`에 의존한다면, 여러분의 `Cargo.toml`파일에 아래의 내용을 추가하세요!

```toml
[profile.debug]
panic = "unwind"

[profile.release]
panic = "unwind"
```

만약 여러분의 프로그램의 사용자가 abort하는 쪽을 선택한다면, 컴파일 에러가 발생하게 될 것입니다. 

`catch_unwind` API는 *thread 안에서* 위에서와 같은 panic으로부터 안전한 영역을 만들수 있게 도와주는데요, 중요한 예시들은 아래와 같습니다:

* 다른 프로그래밍 언어에 러스트를 Embedding하는 경우
* thread들을 다루는 abstraction
* 테스트 프레임워크. 왜냐하면 테스트들은 panic을 할 수 있고 그게 테스트를 하는 프로그램을 죽여서는 안되기 때문이죠.

첫번째의 경우에는, 다른 언어의 영역에서 unwinding을 하는 것은 undefined behavior입니다. 그리고 이것은 주로 segmentation fault를 일으키죠. panic이 잡힐 수 있도록 한다는 것은 여러분이 안전하게 Rust코드를 C API를 통해서 노출시키거나 unwinding을 C 에서의 error로 바꿀 수 있다는 것을 의미합니다.

두번째 같은 경우에는, threadpool 라이브러리를 생각해보세요. 한 thread가 panic을 일으킨 경우, 여러분은 보통 그 thread를 죽이는 것보단 panic을 잡고 client of the pool과 communicate 하고 싶을 것입니다. `catch_unwind` API는 `resume_unwind`와 함께 쓰일수 있는데요, 이것은 패닉 과정을 그게 속하는 client of the pool에서 restart하는데 쓰일 수 있습니다. 

둘 다의 경우에, 여러분은 패닉으로부터 안전한 영역을 thread안에 만드시는 겁니다. 그리고 이것이 panic을 다른 종류의 error로 바꾸는 거죠. 