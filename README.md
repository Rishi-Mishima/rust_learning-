# 🦀 Rust Learning Notes

> 从零开始学习 Rust，记录学习过程、核心知识点、代码练习与项目实践。

这是我的 **Rust 中文学习笔记仓库**，用于系统学习 Rust 编程语言，并通过实际编码加深对语言特性、内存安全、并发编程和系统开发的理解。

本仓库主要参考 [The Rust Programming Language](https://doc.rust-lang.org/book/)（Rust 官方教程），结合个人理解整理中文笔记、示例代码及练习。

## 🎯 学习目标

- 掌握 Rust 基础语法及核心语言特性
- 理解 Ownership、Borrowing 和 Lifetimes
- 熟悉 Struct、Enum、Pattern Matching 和 Traits
- 掌握泛型、错误处理、迭代器与闭包
- 学习 Rust 并发与异步编程
- 熟悉 Cargo、Testing 和 Rust 工程实践
- 将 Rust 应用于 Backend、Blockchain 和 Web3 Infrastructure 开发

## 📚 学习内容

| 主题 | 内容 |
|---|---|
| Getting Started | Rust 环境、Cargo、Hello World |
| Basic Concepts | 变量、数据类型、函数、控制流 |
| Ownership | 所有权、引用、借用、切片 |
| Structs & Enums | 结构体、枚举、模式匹配 |
| Collections | Vector、String、HashMap |
| Error Handling | Result、Option、错误传播 |
| Generics & Traits | 泛型、Trait、生命周期 |
| Functional Features | 闭包、迭代器 |
| Concurrency | 线程、消息传递、共享状态 |
| Async Rust | Future、async/await、Tokio |
| Projects | CLI、Backend、Web3 实践项目 |


> 目录会随着学习进度逐步完善，以上为规划结构。

## 🚀 运行代码

首先安装 Rust：[rustup.rs](https://rustup.rs/)

检查环境：

```bash
rustc --version
cargo --version
```

克隆仓库：

```bash
git clone https://github.com/Rishi-Mishima/rust_learning-.git
cd rust_learning-
```

进入具体的 Cargo 项目，例如：

```bash
cd "1.简介/hello_cargo"
cargo run
```

运行测试：

```bash
cargo test
```

## 🛠️ 项目实践

除了语言基础，本仓库也将逐步加入 Rust 实战项目，重点关注：

- CLI Tools：命令行工具
- Backend Services：后端服务开发
- Async Networking：异步网络编程
- Ethereum / EVM Infrastructure：区块链基础设施开发

## 📖 学习资源

- [The Rust Programming Language](https://doc.rust-lang.org/book/) — 官方教程
- [Rust 程序设计语言中文版](https://kaisery.github.io/trpl-zh-cn/) — 中文教程
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/) — 示例驱动学习
- [Rustlings](https://github.com/rust-lang/rustlings) — Rust 编程练习
- [Rust Standard Library](https://doc.rust-lang.org/std/) — 标准库文档
- [The Cargo Book](https://doc.rust-lang.org/cargo/) — Cargo 使用指南
- [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/) — Rust 异步编程

## 📝 About

本仓库主要用于个人学习、知识整理与实践记录。

笔记以中文为主，保留必要的英文技术术语。内容会随着学习不断更新和完善。

**Learning Rust, one commit at a time. 🦀**