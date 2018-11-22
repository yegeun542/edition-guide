# Struct의 field초기화가 쉬워집니다

![Minimum Rust version: 1.17](https://img.shields.io/badge/Minimum%20Rust%20Version-1.17-brightgreen.svg)

기존의 Rust에서는, struct를 초기화 할때는, 여러분은 무조건 `key: value` 쌍을 입력해야만 했습니다:

```rust
struct Point {
    x: i32,
    y: i32,다
}

let a = 5;
let b = 6;

let p = Point {
    x: a,
    y: b,
};
```

그렇지만, 때때로 이러한 변수들이 field들과 같은 이름을 가지고 있을 때가 있죠. 그래서 여러분은 결과적으로 다음과 같은 코드를 짠 경험이 분명 있을겁니다. 

```rust,ignore
let p = Point {
    x: x,
    y: y,
};
```

지금부터는, 만약 변수들과 field들이 똑같은 이름을 가지고 있다면, 여러분은 둘 다 써야할 필요가 없습니다. 아래와 같이 하나만 적으면 됩니다:

```rust
struct Point {
    x: i32,
    y: i32,
}

let x = 5;
let y = 6;

// new
let p = Point {
    x,
    y,
};
```