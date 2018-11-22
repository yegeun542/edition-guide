# 이제 `loop`가 값과 함께 break할 수 있습니다. 

![Minimum Rust version: 1.19](https://img.shields.io/badge/Minimum%20Rust%20Version-1.19-brightgreen.svg)

이제 `loop`가 값과 함께 break할 수 있습니다: 


```rust
// 이전 버전
let x;

loop {
    x = 7;
    break;
}

// 새 버전
let x = loop { break 7; };
```

Rust는 과거부터 "expression 지향 언어"라고 말해왔는데요, 그 말은, 언어에 존재하는 대부분의 것들이 expression이며 따라서 특정한 값으로 evaluate된다는 말입니다. (statement와는 다르게 말이죠) `loop`는 이러한 기조에서 과거에는 statement였기 때문에 이러한 기조에서 벗어나 있었습니다.  

지금 당장은 이것이 적용되는 것은 `loop`뿐입니다. `while`과 `for`에는 적용되는 사항이 아닙니다. 지금 확실히 정해진 것은 없지만, 미래에 `while`과 `for`에도 이것이 적용될지도 모릅니다. 
