# Doc test가 이제 컴파일이 되는지 안되는지 테스트될 수 있습니다!

![Minimum Rust version: 1.22](https://img.shields.io/badge/Minimum%20Rust%20Version-1.22-brightgreen.svg)

이제 여러분은 `compile-fail`테스트를 만들 수 있습니다. 이렇게요:

```
/// ```compile_fail
/// let x = 5;
/// x += 2; // shouldn't compile!
/// ```
# fn foo() {}
```

Please note that these kinds of tests can be more fragile than others, as
additions to Rust may cause code to compile when it previously would not.
Consider the first release with `?`, for example: code using `?` would fail
to compile on Rust 1.21, but compile successfully on Rust 1.22, causing your
test suite to start failing.