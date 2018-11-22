# 더 명확한 Path

![Minimum Rust version: beta](https://img.shields.io/badge/Minimum%20Rust%20Version-beta-orange.svg)
![Minimum Rust version: nightly](https://img.shields.io/badge/Minimum%20Rust%20Version-nightly-red.svg) for "uniform paths"

Rust 초심자들에게 Rust의 모듈 시스템은 때때로 가장 어려운 장벽중의 하나입니다. 모든 사람들이 저마다 어려워 하는 것은 다 다르지만 Rust의 모듈 시스템이 모든 사람들에게 어려운 데에는 이유가 있습니다: 모듈 시스템을 정의하기 위한 규칙들은 일관성있으며 단순하지만, 그 규칙들의 순서가 Rust초심자들에게는 일관되지 않으며, 비직관적이고 때로는 알쏭달쏭하게 다가오기 때문입니다. 

따라서, 우리는 Rust 2018에디션에서 모듈시스템에 추가된 몇가지 변경점들에 대해서 이야기 해 보려 합니다. 이러한 변경점들은 모듈 시스템을 *단순화*하고 명확하게 만들기 위해서 추가되었습니다. 

간단히 요약하자면:

* 더이상 `extern crate`를 쓸 필요가 없습니다!
* `crate` 키워드는 현재 crate를 가리키는데 사용됩니다
* 절대 경로는 crate 이름으로 시작합니다.
* `foo.rs` 와 `foo/` 하위디렉토리가 공존할 수도 있습니다; `mod.rs`는 서브디렉토리에 서브모듈을 만들때 더이상 필요하지 않습니다. 

이러한 새 규칙들은 이렇게 보면 전혀다른 별개의 규칙들처럼 보일 수 있습니다. 그렇지만 이러한 규칙들을 만든 목적은 단 하나입니다. 모듈 시스템을 단순화하는 것이죠!

> 추가적으로, nightly 에서는 "uniform path"라고 불리는 또다른 변경점이 있습니다. 
> 이 변경점은 새롭게 바뀐 Path와 backward compatible 합니다. 이 가이드 마지막에 "uniform section"은 이 가이드 마지막에 독자적인 섹션을 가지고 있으니 관심있으시면 찾아보시길 바랍니다

## 자세한 변경사항

자 이제 각각의 새로운 변경점들에 대해서 더 자세하게 이야기 해 봅시다.

### 더 이상 `extern crate`가 필요하지 않습니다

이 변경점은 굉장히 단순하면서도 명확합니다: 더이상 여러분의 프로젝트로 외부의 crate를 import 하기 위해서 `extern crate`를 적을 필요가 없습니다

기존 코드:

```rust,ignore
// Rust 2015

extern crate futures;

mod submodule {
    use futures::Future;
}
```
새로운 코드:

```rust,ignore
// Rust 2018

mod submodule {
    use futures::Future;
}
```

이제, 새로운 crate를 `Cargo.toml`파일에 추가하는 것만으로 그 crate를 여러분의 프로젝트에 손쉽게 추가할 수 있습니다. 만약 여러분이 Cargo를 사용하고 있지 않으시다면, 여러분은 기존에 하던 것과 마찬가지로 외부 crate의 위치를 지정해주기 위해 `--extern` 플래그를 `rustc`에 넘겨주던 것을 계속 하시면 됩니다 

> 참고로: `cargo fix`는 현재 이 변화에 대해서 자동적으로 수정을 하지 않습니다. 
> 우리는 아마도 미래에 이 기능을 추가할 것입니다. 

`extern crate`는 매크로를 불러올때도 쓰였었죠. 이 역시 더이상 필요하지 않습니다. [매크로 섹션](../macros/macro-changes.html)을 참고하세요.

만약에 여러분이 `as` 를 crate를 rename하기 위해 아래와 같이 사용해오셨다면: 

```rust,ignore
extern crate futures as f;

use f::Future;
```

단순히 `extern crate`부분만을 제거한 코드는 동작하지 않을 것입니다. 다음과 같이 하세요:

```rust,ignore
use futures as f;

use self::f::Future;
```

이 변경점은 `f`를 사용하는 어떤 모듈에서든지 일어나야 합니다. 

### `crate`키워드는 현재 있는 crate를 나타냅니다.

crate root가 아닌 곳에서 `use`를 쓸때, 여러분은 root를 `crate::`와 같이 표현할수 있습니다. 예를 들어, `crate::foo::bar`는 crate안 어디에 있던지 언제나 crate의 foo모듈 안의 이름 bar를 나타낼것입니다.

앞에 붙이던 `::`는 지금까지 crate root를 나타내거나 외부 crate를 나타내는데 쓰였었는데요, 이제부터는 모호함을 줄이고자 오직 외부 crate를 나타내는 데에만 사용될 예정입니다. 예를 들어서, `::foo::bar`는 언제나 외부 crate `foo`안의 이름 `bar`를 나타낼 것입니다. 

### Path에 관한 변경사항들

Rust 2018에서는 `use`안에 쓰인 path들은 *무조건* crate이름, `crate`, `self` 또는 `super`중의 하나로 시작해야 합니다. 

예를 들어 Rust 2015에서 쓰인 이 코드를 보시죠:

```rust,ignore
// Rust 2015

extern crate futures;

use futures::Future;

mod foo {
    pub struct Bar;
}

use foo::Bar;
```

위 코드는 이제 이렇게 바뀔 겁니다:

```rust,ignore
// Rust 2018

// 'futures' is the name of a crate
use futures::Future;

mod foo {
    pub struct Bar;
}

// 'crate' means the current crate
use crate::foo::Bar;
```

여기에 더해서, 이 모든 path들은 `use`가 아닌 다른 곳에서 사용될때에도 똑같이 사용되어질수 있습니다. Rust 2015에서 쓰였던 아래 코드를 보시죠:

```rust,ignore
// Rust 2015

extern crate futures;

mod submodule {
    // this works!
    use futures::Future;

    // so why doesn't this work?
    fn my_poll() -> futures::Poll { ... }
}

fn main() {
    // this works
    let five = std::sync::Arc::new(5);
}

mod submodule {
    fn function() {
        // ... so why doesn't this work
        let five = std::sync::Arc::new(5);
    }
}
```

> 실제론 여러분이 `mod submodule`를 저렇게 반복할일은 없고 `function`을 그냥 첫번째 `mod` 블록에 정의하겠지만요 하하

이 예시에서 `submodule`안에 `futures`라고 이름 붙여진 것이 없기 때문에 `my_poll`의 function signature는 옳지 않습니다; 그 말은, 이 path가 relative path로 간주된다는 말이죠. `use futures::`는 심지어 `futures::`가 올바른 코드가 아니더라고 잘 동작합니다. `std`와 함께 쓰일 경우에는 더욱더 복잡해지죠. 왜냐면 `extern crate std;`라고 여러분이 쓰실 일은 없으니까요. 그래서 이 코드가 `main`에서는 잘 동작하지만 서브모듈에서는 동작하지 않는 이유가 대체 무엇일까요? 똑같은 문제입니다: 이 path가 `use` 선언 안에 있지 않기 때문에 relative path로 간주된다는 말이죠. `extern crate std;`는 crate root에 삽입됩니다. 그래서 `main`에서는 잘 동작하지만 서브모듈에서는 동작하지 않는 것이죠.

그렇다면 Rust 2018에서는 이 코드가 어떻게 보일까요?:

```rust,ignore
// Rust 2018

// no more `extern crate futures;`

mod submodule {
    // 'futures' is the name of a crate, so this works
    use futures::Future;

    // 'futures' is the name of a crate, so this works
    fn my_poll<T, E>() -> futures::Poll {
        unimplemented!()
    }

    fn function() {
        // 'std' is the name of a crate, so this works
        let five = std::sync::Arc::new(5);
    }
}

fn main() {
    // 'std' is the name of a crate, so this works
    let five = std::sync::Arc::new(5);
}
```

훨씬 더 간단하고 명확하네요!.

### 더 이상 `mod.rs`가 필요하지 않습니다

Rust 2015에서 여러분이 서브모듈을 만들고 싶으셨다면:

```rust,ignore
///  foo.rs 
///  or 
///  foo/mod.rs

mod foo;
```

그 서브모듈은 `foo.rs`안에 만들어지거나 `foo/mod.rs`안에 만들어질 수 있었죠. 만약 그 서브모듈도 또다른 서브모듈을 가지고 있었다면, *무조건* 그 서브모듈은 `foo/mod.rs`안에 있어야만 했습니다. 따라서 `foo`의 `bar`서브모듈은 `foo/bar.rs`안에 있겠죠. 
`foo/bar.rs`.

그렇지만 Rust 2018에서는 `mod.rs`가 더 이상 필요하지 않습니다.

```rust,ignore
///  foo.rs 
///  foo/bar.rs

mod foo;

/// in foo.rs
mod bar;
```

`foo.rs`는 이제 말그대로 `foo.rs`입니다.
그리고 그것의 서브모듈은 말그대로 `foo/bar.rs`이고요. 이 변경점은 쓸데없이 사용되던 `mod.rs`의 사용을 없애고 여러분이 여러분의 프로젝트를 파일 매니저를 통해서 열었을때, 수없이 많은 `mod.rs`를 보는 것이 아니라 한눈에 소스코드들만 볼 수 있도록 해줍니다.

# Uniform paths

> Uniform path는 현재 nightly에서만 사용가능합니다.

Rust 2018의 Uniform path는 path를 지정하는 방식을 하나로 통합하고 단순화하는 역할을 합니다. Rust 2015에서는 path들이 `use`로 선언될 때와 다른 곳에서 사용되어졌을때 상이하게 행동했었습니다. 정확히 말하자면, `use`안의 path들은 항상 crate root에서부터 시작되었고, 프로젝트 안의 다른 path들은 (암묵적으로) 현재 있는 모듈에서부터 시작했죠. 이러한 차이점은 root에 있는 모듈들입장에서는 전혀 문제가 되는 것이 아닙니다. 따라서 작은 프로젝트의 경우는 괜찮았지만 여러분이 서브모듈을 가지고 있는 큰 프로젝트들에서 일하게 되는 것은 문제가 되었죠.  

Rust 2018에서의 uniform path는 `use`안의 path들이 다른 코드에서의 path들과 똑같은 방식으로 작동하도록 합니다. (top-level 모듈이던지 다른 서브모듈이던지 말이죠)
여러분은 언제나 현재 모듈을 기준으로 한 relative path를 쓰실 수 있고, 아니면 외부 crate 이름으로 시작하는 path, 또는 키워드 `crate`, `super` 또는 `self` 로 시작하는 path를 쓰실 수 있습니다. 

예를 들면 Rust 2015의 이 코드는:

```rust,ignore
// Rust 2015

extern crate futures;

use futures::Future;

mod foo {
    pub struct Bar;
}

use foo::Bar;

fn my_poll() -> futures::Poll { ... }

enum SomeEnum {
    V1(usize),
    V2(String),
}

fn func() {
    let five = std::sync::Arc::new(5);
    use SomeEnum::*;
    match ... {
        V1(i) => { ... }
        V2(s) => { ... }
    }
}
```

Rust 2018에서도 똑같이 작동할 겁니다. 여러분이 `extern crate`라인을 제거할 수 있다는 것만 빼고요:

```rust,ignore
// Rust 2018 (uniform paths variant)

use futures::Future;

mod foo {
    pub struct Bar;
}

use foo::Bar;

fn my_poll() -> futures::Poll { ... }

enum SomeEnum {
    V1(usize),
    V2(String),
}

fn func() {
    let five = std::sync::Arc::new(5);
    use SomeEnum::*;
    match ... {
        V1(i) => { ... }
        V2(s) => { ... }
    }
}
```
그렇지만 아래 코드 역시 Rust 2018에서는 똑같은 일을 합니다. 
모듈 코드를 보시죠:

```rust,ignore
// Rust 2018 (uniform paths variant)

mod submodule {
    use futures::Future;

    mod foo {
        pub struct Bar;
    }

    use foo::Bar;  <----------

    fn my_poll() -> futures::Poll { ... }

    enum SomeEnum {
        V1(usize),
        V2(String),
    }

    fn func() {
        let five = std::sync::Arc::new(5);
        use SomeEnum::*;
        match ... {
            V1(i) => { ... }
            V2(s) => { ... }
        }
    }
}
```

이 변경점은 프로젝트 안에서 코드를 옮기는 것을 더 쉽게 만들어주고 여러 모듈을 가지고 있는 프로젝트에서 발생할 수 있는 복잡성을 줄여주는 역할을 합니다.

만약 여러분이 외부 crate를 가지고 있고, 여러분의 프로젝트 안에 똑같은 이름을 가지고 있는 무언가가 있는 상황같이 path가 충돌하는 상황이있다면 여러분은 error를 받게 될 것입니다. 이런 상황에서 여러분은 둘 중에 하나를 하게 되는데요, 이름을 바꾸거나 path를 좀 더 명확하게 나타내는 것입니다. 후자를 선택하신 경우, `::name`을 외부 crate를 위해서 사용하시거나, `self::name`을 내부 모듈과 그 안에 있는 것들을 위해서 사용하세요.
