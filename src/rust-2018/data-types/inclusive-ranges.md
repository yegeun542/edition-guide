# `..=` for inclusive ranges

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg)

예전부터 여러분은 exclusive range를 ".."를 이용해서 다음과 같이 만들어오셨을 겁니다:

```
for i in 1..3 {
    println!("i: {}", i);
}
```

이 코드는 `i: 1` 그리고 `i: 2`를 출력합니다. 지금부터는 여러분은 inclusive range를 다음과 같이 사용하실 수 있습니다:

```rust
for i in 1..=3 {
    println!("i: {}", i);
}
```

이 코드는 이전의 코드와 같이 `i: 1` 그리고 `i: 2`를 출력하고 `i: 3`까지 출력할것입니다. 이러한 inclusive range는 여러분이 range범위 안의 모든 가능한 경우에 대해서 모두 iterate하고 싶을때 특히 유용할 수 있습니다. 예를 들면, 아래와 같은 놀라운 Rust 프로그램을 보시죠:

```rust
fn takes_u8(x: u8) {
    // ...
}

fn main() {
    for i in 0..256 {
        println!("i: {}", i);
        takes_u8(i);
    }
}
```

이 프르그램이 무엇을 하는지 아시겠나요? 정답은요... 아무것도 안합니다! 하하 우리는 이 프로그램을 컴파일 하려할때 이 warning을 받게 되는 데요, 거기에 힌트가 쓰여 있습니다: 

```text
warning: literal out of range for u8
 --> src/main.rs:6:17
  |
6 |     for i in 0..256 {
  |                 ^^^
  |
  = note: #[warn(overflowing_literals)] on by default
```

아아, `i`가 `u8`이기 때문에 오버플로우가 발생하는군요. 따라서 이는 `for i in 0..0`라고 적는 것과 마찬가지입니다. 그래서 아무 일도 일어나지 않는 것이죠. 

그런데 만약 우리가 여기에 inclusive range를 적용하면 어떻게 될까요?:

```rust
fn takes_u8(x: u8) {
    // ...
}

fn main() {
    for i in 0..=255 {
        println!("i: {}", i);
        takes_u8(i);
    }
}
```

이 코드는 여러분이 처음에 예상하셨을 256개의 line들을 정상적으로 출력할 것입니다. 