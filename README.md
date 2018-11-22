# 러스트 에디션 가이드 한국어판
# The Rust edition guide Korean version

[지금 바로 읽어보기](https://yegeun542.github.io/book/introduction.html)

안녕하세요! 

Rust를 쓰시는 한국어 유저님들. 이 저장소는 러스트 에디션 가이드의 한국어판을 위한 저장소입니다. 러스트 에디션 가이드의 한국어판이 있었으면 좋겠다는 생각에 시작하게 되었습니다... 만 대학생인 관계로 시험기간에는 시험공부를 해야하니, 이것에만 짧게 집중해서 할수는 없을것 같습니다. 그래도 최대한 시간을 할애해서 빨리 끝내고 주기적으로 관리하겠습니다!

현재 mdbook에 multilingual support가 논의만 되고 있지 구현이 안된것으로 아는데, 파이썬처럼 공식문서에서 한국어를 바로 선택해서 볼 수 있으면 정말 좋을것같습니다. 

| 목차   | 진행도                                |
| ----------|-----------------------------------|
| 서문       | ✔️  |
| 1. 에디션이 무엇인가요?    | ✔️ |
| 2. Rust 2015 | ✔️ |
| Rust 2018      | |
| 3.1 모듈 시스템 | ✔️ |
| 3.2 에러 처리와 패닉  | |
| 3.3 컨트롤 플로우 |  |

최대한 읽기 쉽게 번역하는 것을 목표로 하고 있고, idiomatic, backward compatibility 등의 단어는 번역하기 보다는 그대로 놔두는 것을 선택했습니다. 
(정확한 의미전달을 하기 위해서 원문 그대로 놔둔 곳도 있습니다)

혹시 번역 오류를 발견하신다면 꼭 연락주세요!! ㅠㅠ
<yyang542@wisc.edu>

# The Rust Edition Guide

[![Build Status](https://travis-ci.org/rust-lang-nursery/edition-guide.svg?branch=master)](https://travis-ci.org/rust-lang-nursery/edition-guide)

This book explains the concept of "editions", major new eras in [Rust]'s
development. You can [read the book
online](https://rust-lang-nursery.github.io/edition-guide/).

[Rust]: https://www.rust-lang.org/

## License

The Edition Guide is dual licensed under `MIT`/`Apache2`, just like Rust itself.
See the `LICENSE-*` files in this repository for more details.

## Building locally

You can also build the book and read it locally if you'd like.

### Requirements

Building the book requires [mdBook]. To get it:

[mdBook]: https://github.com/azerupi/mdBook

```bash
$ cargo install mdbook
```

### Building

To build the book, do this:

```bash
$ mdbook build
```

The output will be in the `book` subdirectory. To check it out, open it in
your web browser.

_Firefox:_

```shell
$ firefox book/index.html                       # Linux
$ open -a "Firefox" book/index.html             # OS X
$ Start-Process "firefox.exe" .\book\index.html # Windows (PowerShell)
$ start firefox.exe .\book\index.html           # Windows (Cmd)
```

_Chrome:_

```shell
$ google-chrome book/index.html                 # Linux
$ open -a "Google Chrome" book/index.html       # OS X
$ Start-Process "chrome.exe" .\book\index.html  # Windows (PowerShell)
$ start chrome.exe .\book\index.html            # Windows (Cmd)
```

To run the tests:

```bash
$ mdbook test
```
