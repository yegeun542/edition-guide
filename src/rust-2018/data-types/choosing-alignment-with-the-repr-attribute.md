# repr attribute로 alignment 설정하기

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg)

From [Wikipedia](https://en.wikipedia.org/wiki/Data_structure_alignment):

> 현대 컴퓨터 하드웨어안의 CPU는 메모리 안의 데이터가 적절하게 aligned되어 있을 때 메모리 읽기연산과 쓰기연산을 가장 효과적으로 수행할 수 있습니다. 일반적으로 말하자면 이것은 메모리상의 주소가 데이터의 크기의 배수로 되어있는 경우를 말합니다. Data alignment란 데이터들을 그들의 natural alignment에 맞춰서 align하는 것을 말하는데요, 이 natural alignment를 보장하기 위해서 우리는 때때로 데이터 사이, 또는 데이터의 가장 마지막 요소 뒤에 빈 공간(padding)을 넣습니다. 

`#[repr]` attribute는 새로운 parameter를 갖게 되었는데요, `struct`의 alignment를 설정하는 `align`입니다:

```rust
struct Number(i32);

assert_eq!(std::mem::align_of::<Number>(), 4);
assert_eq!(std::mem::size_of::<Number>(), 4);

#[repr(align(16))]
struct Align16(i32);

assert_eq!(std::mem::align_of::<Align16>(), 16);
assert_eq!(std::mem::size_of::<Align16>(), 16);
```

만약 여러분이 저수준작업을 하고 있다면, 이러한 것이 여러분에게 굉장히 중요할수도 있습니다. 

이러한 alignment는 일반적으로 걱정할 거리가 아닌데요, 컴파일러가 알아서 일반적인 경우에 그에 맞는 alignment 를 설정하기 때문이죠. 그렇지만, 때때로 우리는 foreign system과 함께 동작해야하는 경우같이 일반적인 alignment가 아닌 alignment가 필요한 경우들과 맞닥뜨립니다. 예를 들면, 아래와 같은 상황들이 custom alignment가 필요하거나 있으면 더 편해지는 경우들이죠. 

* Hardware can often have obscure requirements such as "this structure is
  aligned to 32 bytes" when it in fact is only composed of 4-byte values. While
  this can typically be manually calculated and managed, it's often also useful
  to express this as a property of a type to get the compiler to do a little
  extra work instead.
* `gcc` 와 `clang` 같은 C 컴파일러들은 structure들의 alignment를 사용자 마음대로 바꿀 수 있도록 허용합니다. Rust도 이러한 custom alignment를 허용한다면 C 코드와 상호작용하는 것이 더 쉬워지겠죠? (예를 들면 정확하게 C로 structure를 건네주는 것이 더 쉬워집니다)
* Custom alignment can often be used for various tricks here and there and is
  often convenient as "let's play around with an implementation" tool. For
  example this can be used to statically allocate page tables in a kernel or
  create an at-least cache-line-sized structure easily for concurrent
  programming.

이 기능의 목적은 위와 같은 상황들을 더 쉽게 다루기 위해 컴파일러가 기본적으로 선택하는 alignment를 바꿀 수 있는 가볍고 단순한 방법을 제공하는 것입니다. 