# Custom Derive

![Minimum Rust version: 1.15](https://img.shields.io/badge/Minimum%20Rust%20Version-1.15-brightgreen.svg)

Rust에서 여러분은 아래와 같은 코드를 써보신적이 많으실 겁니다. (특정한 trait들을 자동적으로 implement하게 해주는 기능이지요):

```rust
#[derive(Debug)]
struct Pet {
    name: String,
}
```

위와 같이 하면 `Debug` trait가 `Pet`에 대해서 아주 손쉽게 implement됩니다. 만약, `derive`가 없었다면, 여러분은 아래와 같이 썼어야만 했을 겁니다:

```rust
use std::fmt;

struct Pet {
    name: String,
}

impl fmt::Debug for Pet {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            Pet { name } => {
                let mut debug_trait_builder = f.debug_struct("Pet");

                let _ = debug_trait_builder.field("name", name);

                debug_trait_builder.finish()
            }
        }
    }
}
```

숨막히네요!

매우 편리한 기능이지만, 여기에는 문제가 하나 있습니다. 바로 standard library에 있는 것들만이 자동적으로 implement될 수 있다는 것입니다. 여러분이 만든 custom 타입에는 쓰일 수 없다는 말이지요. 그렇지만 이제부터는, 여러분은 다른 누군가가 여러분의 타입을 derive하고 싶을때 어떻게 하고 싶은지 Rust에게 말해 주실 수 있습니다. 이러한 기능은 유명한 crate들인 [serde](https://serde.rs/) 와 [Diesel](http://diesel.rs/)에서 정말 많이 쓰이지요.

더 자세한 것이 알고 싶으시다면, 다음 링크를 참고하세요. [Rust 언어](https://doc.rust-lang.org/book/second-edition/appendix-04-macros.html#procedural-macros-for-custom-derive).