# `enum`의 Unsafe 버전인 `union`!

![Minimum Rust version: 1.19](https://img.shields.io/badge/Minimum%20Rust%20Version-1.19-brightgreen.svg)

이제 Rust가 `union`을 지원합니다!:

```rust
union MyUnion {
    f1: u32,
    f2: f32,
}
```

`union`들은 `enum`과 비슷하지만 "untagged"되어있다는 점에서 다릅니다. Enum들은 Runtime에 어떤 Variant가 맞는 것인지 저장하는 "tag"를 가지고 있는 반면에 union들은 이러한 "tag"가 없습니다.  

우리가 union안에 있는 데이터를 잘못된 variant로 해석할 수 있고 Rust가 우리를 대신해서 이것을 확인해줄 수 없기 때문에 union의 field를 읽는 것은 unsafe입니다: 

```rust
# union MyUnion {
#     f1: u32,
#     f2: f32,
# }
let mut u = MyUnion { f1: 1 };

u.f1 = 5;

let value = unsafe { u.f1 };
```

패턴 매칭도 잘 동작합니다!:

```rust
# union MyUnion {
#     f1: u32,
#     f2: f32,
# }
fn f(u: MyUnion) {
    unsafe {
        match u {
            MyUnion { f1: 10 } => { println!("ten"); }
            MyUnion { f2 } => { println!("{}", f2); }
        }
    }
}
```

When are unions useful? One major use-case is interoperability with C. C APIs
can (and depending on the area, often do) expose unions, and so this makes
writing API wrappers for those libraries significantly easier. Additionally,
unions also simplify Rust implementations of space-efficient or
cache-efficient structures relying on value representation, such as
machine-word-sized unions using the least-significant bits of aligned
pointers to distinguish cases.

미래에 union에 더 많은 개선점들이 추가될것입니다. 아직까지는 union들은 `Copy`타입만 포함할 수 있고, 또한 `Drop`을 implement하지 못합니다. 우리는 이러한 제약점들을 미래에는 걷어낼수 있을거라고 기대하고 있습니다.  