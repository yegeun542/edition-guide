# 새로운 프로젝트 만들기

여러분이 Cargo로 새로운 프로젝트를 만들려고 한다면, Cargo가 자동적으로 최신 에디션을 위한 설정을 추가할 것입니다. 

```console
> cargo +nightly new foo
     Created binary (application) `foo` project
> cat .\foo\Cargo.toml
[package]
name = "foo"
version = "0.1.0"
authors = ["your name <you@example.com>"]
edition = "2018"

[dependencies]
```

저기 있는 `edition = 2018` 설정만 하면 여러분의 패키지가 Rust 2018 에디션을 쓸 수 있습니다!. 

만약 여러분이 옛날 버전을 쓰고 싶다면 언제든지 이 값을 바꾸실 수 있습니다. 예를 들면, 

```toml
[package]
name = "foo"
version = "0.1.0"
authors = ["your name <you@example.com>"]
edition = "2015"

[dependencies]
```

이렇게 하시면 여러분의 패키지가 Rust 2015로 빌드될 것입니다.
