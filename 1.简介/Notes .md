# Getting Started 

## 1.1 Installation 

```shell
rustc --version
cargo --version
rustup update stable
```

安装rustlings

```shell
cargo install rustlings
rustlings init
cd rustlings
rustlings
```



## 1.2 Hello World 

创建hello world文件包(directory)

- main.rs 

```rust
fn main() {
    println!("Hello, world!");
}
```

编译 (cd rust/hello_world)

```shell
$ rustc main.rs
$ ./main
Hello, world!
```

首先，`println!` 调用了一个 Rust 宏（macro）。如果调用的是函数，就应该写成 `println`（不带 `!`）

- 宏并不总是遵循与函数相同的规则
- Rust 是一种 **预先编译**（*ahead-of-time compiled*）语言，这意味着你可以先将程序编译好，再把可执行文件交给其他人



## 1.3 Hello, Cargo !

Cargo 是 Rust 的构建系统和包管理器

- 管理库, 依赖(dependencies)

### 使用Cargo创建项目

```shell
 cargo new hello_cargo
$ cd hello_cargo
```

进入 *hello_cargo* 目录并列出文件。你会看到 Cargo 为我们生成了两个文件和一个目录：一个 *Cargo.toml* 文件、一个 *src* 目录，以及位于 *src* 目录中的 *main.rs* 文件。

它还会在 *hello_cargo* 目录中初始化一个 Git 仓库，并生成一个 *.gitignore* 文件。

- 如果在一个已经存在的 Git 仓库中运行 `cargo new`，这些 Git 相关文件就不会再生成；
- 你可以通过运行 `cargo new --vcs=git` 来覆盖这个行为

使用 [*TOML*](https://toml.io/)（*Tom’s Obvious, Minimal Language*）格式，这是 Cargo 配置文件所使用的格式。

> 如果你一开始没有使用 Cargo 创建项目，比如我们\之前创建的 “Hello, world!” 项目，也可以把它转换成一个使用 Cargo 的项目。
>
> 只要把项目代码移到 *src* 目录中，并创建一个合适的 *Cargo.toml* 文件即可。一个简单的办法是运行 `cargo init`，它会自动为你创建这个文件。



!! 让Cargo不要生成Git仓库

```shel
cargo new hello_cargo --vcs none
```



### 构建并允许cargo项目

在 *hello_cargo* 目录下，输入下面的命令来构建项目：

```shell
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs

```

这条命令会在 *target/debug/hello_cargo* 生成一个可执行文件

执行这个文件

```shell
$ ./target/debug/hello_cargo
```

第一次运行 `cargo build` 时，Cargo 还会在项目根目录创建一个新文件：*Cargo.lock*。这个文件会记录项目依赖的精确版本。由于这个项目没有依赖，所以文件内容比较少。

- 永远都不需要手动修改这个文件；Cargo 会自动管理它的内容。

> 也可以使用`cargo run` 

也可以使用 `cargo run`，在一条命令中完成编译并运行生成的可执行文件：

```shell
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

- 比起必须先运行 `cargo build` 再用可执行文件的完整路径来执行程序，使用 `cargo run` 更方便，所以大多数开发者会选择 `cargo run`。

Cargo 还提供了一个叫 `cargo check` 的命令。该命令快速检查代码确保其可以编译，但并不产生可执行文件：

```shell
$ cargo check
   Checking hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
```

为什么你会不需要可执行文件呢？通常 `cargo check` 比 `cargo build` 快得多，因为它省略了生成可执行文件这一步。

- 如果你在编写代码时持续检查，`cargo check` 可以让你更快知道当前代码是否还能正常编译！
- 因此，很多 Rustaceans 都会在编写程序时定期运行 `cargo check` 来确保代码可以编译；等到准备好使用可执行文件时，再运行 `cargo build`。



我们回顾下已学习的 Cargo 内容：

- 可以使用 `cargo new` 创建项目。
- 可以使用 `cargo build` 构建项目。
- 可以使用 `cargo run` 一步构建并运行项目。
- 可以使用 `cargo check` 在不生成二进制文件的情况下构建项目来检查错误。
- 有别于将构建结果放在与源码相同的目录，Cargo 会将其放到 *target/debug* 目录。

### release发布构建

在日常开发和写代码时，继续安心使用 **`cargo run`**，因为它能帮节省大量的编译等待时间。

当你把程序写完了，想看看它在最终状态下跑得有多快，或者准备发给别人使用时，可以使用 **`cargo run --release`** 或者直接使用 **`cargo build --release`**。

### 把Cargo当作习惯

实际上，当你在任何已有项目上工作时，都可以使用如下命令，通过 Git 检出代码、进入项目目录并构建它：

```console
$ git clone example.org/someproject
$ cd someproject
$ cargo build
```

关于更多 Cargo 的信息，请查阅 [其文档](https://doc.rust-lang.org/cargo/)。