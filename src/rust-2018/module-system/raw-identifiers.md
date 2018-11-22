# 키워드를 식별자로 사용하기

![Minimum Rust version: beta](https://img.shields.io/badge/Minimum%20Rust%20Version-beta-orange.svg)

다른 많은 프로그래밍 언어들처럼 Rust에도 "키워드"라는 개념이 존재합니다. 
키워드들은 언어 안에서 특별한 의미를 가집니다. 그리고 여러분은 이러한 키워드들을 변수이름, 함수 이름 등등으로 쓸 수 없죠. 그렇지만 이러한 키워드들을 일반적으로는 식별자로 쓸 수 없는 곳에서 식별자로 쓸 수 있게 만드는 방법이 존재합니다. 

예를 들어보자면, `match`는 키워드입니다. 만약 여러분이 이 코드를 컴파일 하려고 한다면:

```rust,ignore
fn match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}
```

컴파일 에러가 발생하겠죠:

```text
error: expected identifier, found keyword `match`
 --> src/main.rs:4:4
  |
4 | fn match(needle: &str, haystack: &str) -> bool {
  |    ^^^^^ expected identifier, found keyword
```

그렇지만 여러분은 이렇게 할 수 있습니다:

```rust
fn r#match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}

fn main() {
    assert!(r#match("foo", "foobar"));
}
```

이 방법을 쓸때, 함수의 정의부분에서뿐만 아니라 함수를 호출할때에도 `r#`를 써야하는 점에 주의하세요.

## 왜 이런 기능이 추가되었나요?

이 기능은 그다지 유용한 기능은 아닙니다만, 이 기능을 소개하게 된 주요한 동기를 말해보자면, 에디션간의 호환성문제때문입니다. 예를 들어서, `try`는 Rust 2015에디션에서는 키워드가 아니었습니다. 그렇지만 Rust 2018에디션에서 키워드가 돠었지요. 따라서 만약에 여러분이 Rust 2015에디션에서 `try`라는 이름을 가진 함수를 정의했고, 이 함수를 Rust 2018에디션에서 사용하고 싶으시다면 위의 기능을 이용하시면 됩니다. 

## 새로운 키워드들

새롭게 2018에디션에 추가된 키워드들은 다음과 같습니다:

### `async`와 `await`

[RFC 2394]: https://github.com/rust-lang/rfcs/blob/master/text/2394-async_await.md#final-syntax-for-the-await-expression

`async`키워드는 `async fn`과 `async ||`클로저, 그리고 `async { .. }` 블록에서 사용되어지기 위해 예약되었습니다. 또한 `await`는 우리가 `await!(expr)`과 관련해서 보다 유연한 선택을 내릴 수 있도록 예약되었습니다. 더 자세한 정보를 원하신다면, [RFC 2394]를 참고하세요.

### `try`

[RFC 2388]: https://github.com/rust-lang/rfcs/pull/2388

`try`키워드가 Rust 2018에디션에 새로이 추가되었기 때문에, `do catch { .. }` 블록은 이제 `try { .. }`라고 불리게 됬습니다. 더 자세한 정보를 원하신다면 [RFC 2388]를 참고하세요.
