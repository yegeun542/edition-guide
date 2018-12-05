# `main`과 tests 안에서 이제 ?를 쓸 수 있습니다

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg)

여러분은 러스트에서 에러 처리를 할 때 보통 `Result<T, E>`를 쓰거나 `?`를 쓰실겁니다. 만약 여러분이 간단한 프로그램들을 많이 짜시거나 (많은 테스트도 같이 짜신다면), `main`과 `#[test]`안에서 에러 처리를 할때 짜증나셨던 경험이 있으실 겁니다.

예를 들면, 여러분은 아래와 같은 코드를 작성하신 적이 있으실 겁니다:

```rust,ignore
use std::fs::File;

fn main() {
    let f = File::open("bar.txt")?;
}
```

`?`가 `Result`를 함수를 호출한 쪽으로 넘겨주기 때문에, 위와 같은 코드는 동작하지 않습니다. 그래서 아래와 같은 컴파일 에러를 일으키지요.

```rust,ignore
error[E0277]: the `?` operator can only be used in a function that returns `Result`
              or `Option` (or another type that implements `std::ops::Try`)
 --> src/main.rs:5:13
  |
5 |     let f = File::open("bar.txt")?;
  |             ^^^^^^^^^^^^^^^^^^^^^^ cannot use the `?` operator in a function that returns `()`
  |
  = help: the trait `std::ops::Try` is not implemented for `()`
  = note: required by `std::ops::Try::from_error`
```

러스트 2015에서 이 문제를 해결하기 위해서 여러분은 지금까지 아래와 같이 해오셨습니다:

```rust
// Rust 2015

# use std::process;
# use std::error::Error;

fn run() -> Result<(), Box<Error>> {
    // real logic..
    Ok(())
}

fn main() {
    if let Err(e) = run() {
        println!("Application error: {}", e);
        process::exit(1);
    }
}
```
그렇지만 이 경우에는, `run` 함수가 핵심이고 `main`함수는 사실상 아무것도 하지 않죠. 이 문제는 `#[test]`와 함께 쓰일때, 더 심각해집니다. 왜냐면 테스트들은 보통 그 수가 많으니까요.

러스트 2018에서는 이제 `#[test]`와 `main`안에서 `Result`를 리턴할 수 있습니다:

```rust,no_run
// Rust 2018

use std::fs::File;

fn main() -> Result<(), std::io::Error> {
    let f = File::open("bar.txt")?;

    Ok(())
}
```
이 경우에는, 예를들어 파일이 존재하지 않아서 `Err(err)`가 리턴되었다고 친다면, `main`은 `0`이 아닌 에러코드와 함께 `exit` 할 것이고 `err`의 `Debug`폼을 프린트할것입니다.

## 동작 원리

`-> Result<...>`가 `main`과 `#[test]`와 같이 쓰일 수 있도록 하기 위해서 우리가 무슨 마법을 부린 것은 아닙니다. 이러한 것들은 모두 `Termination` Trait 을 이용해서 작동하는데요, `main`의 모든 올바른 리턴 타입들과 테스트 함수들은 이 trait을 무조건 구현해야 합니다. 이 trait은 다음과 같이 정의되어 있습니다.

```rust
pub trait Termination {
    fn report(self) -> i32;
}
```

여러분의 프로그램의 시작 포인트를 지정할때, 컴파일러는 이 trait을 이용해서 여러분이 쓴 `main`함수의 `Result`에 대해서 `.report()`를 호출할겁니다. 

이 trait의 `Result`와 `()`에 대한 두개의 간단한 구현 예시는 다음과 같습니다:

```rust
# #![feature(process_exitcode_placeholder, termination_trait_lib)]
# use std::process::ExitCode;
# use std::fmt;
#
# pub trait Termination { fn report(self) -> i32; }

impl Termination for () {
    fn report(self) -> i32 {
        # use std::process::Termination;
        ExitCode::SUCCESS.report()
    }
}

impl<E: fmt::Debug> Termination for Result<(), E> {
    fn report(self) -> i32 {
        match self {
            Ok(()) => ().report(),
            Err(err) => {
                eprintln!("Error: {:?}", err);
                # use std::process::Termination;
                ExitCode::FAILURE.report()
            }
        }
    }
}
```

`()`의 경우에는 보시다시피 success code가 그냥 리턴됩니다. `Result`의 경우에는 `Ok`인 경우엔 `()`의 구현쪽으로 위임되고, `Err(...)`의 경우는 에러 메세지가 출력되고 failure exit code가 리턴됩니다.

더 자세한게 알고 싶으시다면 [여기(the tracking issue)](https://github.com/rust-lang/rust/issues/43301)나 [여기(the RFC)](https://github.com/rust-lang/rfcs/blob/master/text/1937-ques-in-main.md)에 물어보세요!.

