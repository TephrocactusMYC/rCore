# day1
## 写在最前
很好，报名了开源操作系统训练营2023秋冬季。

然而由于时间问题，在开课已经一周已有200多人顺利晋级的情况下，才刚开始（蚌埠住了！） :(

那么计划一共花七天左右的时间来入门 `Rust` 语言并且完成开源操作系统训练营魔改版本的 100 道 rustlings 题目（原版为94题）！

个人仅采用 [RUST 语言程序设计](https://kaisery.github.io/trpl-zh-cn/title-page.html)进行学习，没有跟课程走。

*所有内容仅为个人纪录，部分内容较为简短，他人也许难以阅读，因为这并不是面向公众的教学博客。*
## 正式开始
### rustc
rustup 进行版本管理。


简单地运行程序
```
$ rustc hello_world.rs
$ ./hello_world
Hello, world!
```

*Rust 的缩进风格使用 4 个空格，而不是 1 个制表符（tab）.*
### cargo
包管理工具和构建项目的工具是 `cargo`!

有如下名称
```
cargo new       #创建项目
cargo build     #构建项目
cargo run       #编译并运行
cargo check     #快速检查代码确保其可以编译，但并不产生可执行文件
```
TOML (Tom's Obvious, Minimal Language) 格式，这是 Cargo 配置文件的格式。

代码会存在 `src` 目录之下
