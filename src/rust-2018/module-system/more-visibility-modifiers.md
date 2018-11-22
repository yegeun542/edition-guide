# 추가적인 visibility modifier

![Minimum Rust version: 1.18](https://img.shields.io/badge/Minimum%20Rust%20Version-1.18-brightgreen.svg)

여러분은 `pub`키워드를 사용하여 무언가를 모듈의 public interface의 일부분으로 만들 수 있습니다. 그렇지만, 여기에 더해서, 아래와 같은 것들도 이제는 가능합니다:

```rust,ignore
pub(crate) struct Foo;

pub(in a::b::c) struct Bar;
```

첫번째 양식은 `Foo` struct를 crate전체에 대해서 public하도록 만듭니다. 그렇지만, crate밖에서는 접근할 수 없습니다. 
첫번째와 비슷하게 생긴 두번째 양식은 `Bar` struct를 또다른 모듈 `a::b::c`에 대해서 public 하게 되도록 만들어줍니다.