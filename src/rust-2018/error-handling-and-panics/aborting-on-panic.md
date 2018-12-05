# 패닉이 발생한 경우 Abort하기

![Minimum Rust version: 1.10](https://img.shields.io/badge/Minimum%20Rust%20Version-1.10-brightgreen.svg)

기본적으로(By default), 러스트 프로그램들은 `panic!`발생시에 stack unwind를 합니다. 만약 여러분이 이것 대신에 즉시 abort하는 것을 원하신다면 여러분은 `Cargo.toml`파일에 아래와 같이 적으시면 됩니다!

```toml
[profile.debug]
panic = "abort"

[profile.release]
panic = "abort"
```

왜 굳이 abort를 하냐고요? stasck unwinding을 하지 않음으로써, 여러분은 더 적은 사이즈의 바이너리를 얻으실 수 있습니다. 여러분은 panic을 잡아낼 수 없게 되시겠지만, 여러분이 어떤 프로젝트를 하시냐에 따라서 둘 다 올바른 선택이 될 수 있습니다. 