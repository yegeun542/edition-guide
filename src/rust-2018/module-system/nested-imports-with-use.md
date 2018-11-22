# 중첩가능한 `use` import

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg)

이제 새로운 방식으로 `use` statement를 사용할수 있게 되었습니다: 바로 중첩입니다. 
만약 여러분이 한번이라도 다음과 같은 import코드를 짜본적이 있으시다면:

```rust
use std::fs::File;
use std::io::Read;
use std::path::{Path, PathBuf};
```

여러분은 이제부터 이렇게 하실 수 있습니다:

```rust
# mod foo {
// 이렇게 한줄에 적거나
use std::{fs::File, io::Read, path::{Path, PathBuf}};
# }

# mod bar {
// 여러줄에 걸쳐서
use std::{
    fs::File,
    io::Read,
    path::{
        Path,
        PathBuf
    }
};
# }
```

우리는 이것이 쓸데없는 반복을 줄이고 코드를 약간 더 명확하게 만들 수 있을거라고 생각합니다
