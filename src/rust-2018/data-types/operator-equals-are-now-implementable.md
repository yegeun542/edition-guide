# 이제 복합 연산자들이 implementable 합니다. 

![Minimum Rust version: 1.8](https://img.shields.io/badge/Minimum%20Rust%20Version-1.8-brightgreen.svg)

The various “operator equals” operators, such as `+=` and `-=`, are
implementable via various traits. For example, to implement `+=` on
a type of your own:
`+=`과 `-=`같은 다양한 복합 연산자들이 이제 trait를 통해서 implementable합니다. 예를 들면, `+=`를 여러분의 custom type에 대해서 implement하는 코드를 보세요:

```rust
use std::ops::AddAssign;

#[derive(Debug)]
struct Count { 
    value: i32,
}

impl AddAssign for Count {
    fn add_assign(&mut self, other: Count) {
        self.value += other.value;
    }
}

fn main() {
    let mut c1 = Count { value: 1 };
    let c2 = Count { value: 5 };

    c1 += c2;

    println!("{:?}", c1);
}
```

위의 코드는 `Count { value: 6 }`를 출력할 것입니다. 