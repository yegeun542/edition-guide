# 더 쉬운 에러처리를 위한 `?` 연산자

![Minimum Rust version: 1.13](https://img.shields.io/badge/Minimum%20Rust%20Version-1.13-brightgreen.svg) for `Result<T, E>`

![Minimum Rust version: 1.22](https://img.shields.io/badge/Minimum%20Rust%20Version-1.22-brightgreen.svg) for `Option<T>`

러스트가 쓸데 없는 코드를 줄임으로써 에러 처리를 더 쉽게 해주는 새로운 연산자, `?`를 얻었습니다! 이 연산자는 이걸 간단한 하나의 문제를 해결함으로써 이 목표를 이룹니다. 예를 들어, 우리가 파일로부터 데이터를 읽어들이기 위해서 다음과 같은 코드를 짰다고 해봅시다:

```rust
# use std::{io::{self, prelude::*}, fs::File};
fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("username.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
```

> 알아두세요: 이 코드는 [`std::fs::read_to_string`](https://doc.rust-lang.org/stable/std/fs/fn.read_to_string.html)을 사용하면 더 간략화될수 있습니다.
> 우리는 다양한 에러들이 있는 예시를 보기 위해서 위의 예시를 사용할 것입니다.

이 코드는 두 가지 경우에 대해서 에러가 발생할 수 있습니다. 파일을 열때와 파일로부터 데이터를 읽을 때죠. 어떤 에러가 발생하든지간에 우리는 에러를 `read_username_from_file`로부터 리턴하고 싶습니다. 이렇게 하는 것은 I/O operation의 결과에 대해서 `match`하는 것을 포함하죠. 그렇지만 우리가 발생한 에러를 그냥 호출한쪽에 넘기는 이렇게 간단한 경우에는 그렇게 하는 것이 코드를 너무 장황하게 보이게 만들죠.

`?`를 사용하면 위의 코드는 아래와 같이 간략화 될 수 있습니다:

```rust
# use std::{io::{self, prelude::*}, fs::File};
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("username.txt")?;
    let mut s = String::new();

    f.read_to_string(&mut s)?;

    Ok(s)
}
```
`?`는 우리가 위에 썻던 매치 statement를 짧게 줄인 것입니다. 다른 말로 하자면, `?`는 `Result`타입에 적용된다는거죠. 만약 그게 `Ok`였으면, unwrap하고 그 안의 값을 전달합니다. 만약 `Err`인 경우는, 속해 있는 함수가 그 에러를 리턴하도록 합니다. 시각적으로 보자면, 이게 훨씬 깔끔한 방법입니다. 이제 우리는 `?`를 그냥 적는 것 만으로 우리가 에러 처리를 call stack에 넘겨준다는 것을 쉽고 간단하게 표현할 수 있게 되었습니다. 

숙련된 러스트 사용자분들이라면 이게 러스트 1.0부터 사용 가능해진 `try!`매크로와 똑같다는 것을 눈치채셨을 겁니다. 그리고 실제로도 이 둘은 같습니다. 지금까지는 `read_username_from_file`은 아래와 같이 구현되어졌을 겁니다:

```rust
# use std::{io::{self, prelude::*}, fs::File};
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = try!(File::open("username.txt"));
    let mut s = String::new();

    try!(f.read_to_string(&mut s));

    Ok(s)
}
```

그래서 우리가 이미 매크로가 있는데 왜 또 똑같은 일을 하는 것을 추가하냐고요? 여기엔 여러가지 이유가 있습니다. 일단 `try!`매크로는 무척이나 유용하고 idiomatic한 러스트 코드에 자주 등장합니다. 이 매크로가 무척 자주 쓰인다는 것에서, 우리는 프로그래밍 언어에 있어서 더 간결하고 좋은 syntax를 가지는 것이 매우 중요하다는 것을 알수 있습니다. 그리고 러스트의 강력한 매크로 시스템은 이러한 더 나은 새로운 syntax를 고안하는 것을 더 쉽게 만들어 줍니다: 예를들어서, 실험적인 언어 확장들이 고안되고 그것을 매크로로 만들면 언어설계를 바꾸지 않으면서도 그 확장들을 쓸 수 있고 만약 이 매크로들이 유용하다는 것이 입증되면 그때 그것들을 언어의 정식 일부분으로 받아들일 수 있죠. `try!`매크로에서 `?`로의 전환이 그 좋은 예시입니다.

```rust,ignore
try!(try!(try!(foo()).bar()).baz())
```

as opposed to

```rust,ignore
foo()?.bar()?.baz()?
```

첫번째 경우에는 코드를 눈으로 가볍게 쓱 훑기가 좀 어렵습니다. 그리고 각각의 에러 레이어가 표현식 앞에 `try!`를 쓸때마다 붙지요. 이게 간단한 에러 처리를 아주 복잡하게 보이게 만들면서 코드 흐름을 파악하는 것을 방해합니다. 이러한 종류의 에러 처리를 포함한 method 연쇄는 builder pattern같은 경우에서 자주 나타납니다.  

그렇지만 `?`를 쓰면 매크로를 사용했을때와는 다르게 더 깔끔한 에러 처리를 할 수가 있죠.

You can use `?` with `Result<T, E>`s, but also with `Option<T>`. In that
case, `?` will return a value for `Some(T)` and return `None` for `None`. One
current restriction is that you cannot use `?` for both in the same function,
as the return type needs to match the type you use `?` on. In the future,
this restriction will be lifted.
여러분은 또한 `?`를 `Result<T, E>`와 쓰는 것 뿐만이 아니라 `Option<T>`와도 쓰실 수 있습니다. 이 경우에는, `?`는 `Some(T)`안의 값을 리턴하거나 `None`의 경우에는 `None`을 리턴합니다. 한가지 주의하셔야 할 점은 현재로써는 여러분이 `?`를 하나의 함수 내부에서 `Result`와 `Option`둘 다에 대해서는 쓰지 못하신다는 것인데 미래에는 이것이 가능해질 것입니다.
