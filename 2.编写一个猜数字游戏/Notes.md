# 2. [编写一个猜数字游戏](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#编写一个猜数字游戏)

> 本章是关于`let`, `match`, 方法(methods), 关联函数（associated functions）、外部 crate 等



## 准备一个新项目

```shell
cargo new guessing_game --vcs none

cargo run
```

## 处理一次猜测

猜数字程序的第一部分请求和处理用户输入，并检查输入是否符合预期的格式。首先，我们会允许玩家输入一个猜测。在 *src/main.rs* 中输入示例 2-1 中的代码。

```rust
// 为了获取用户输入并将结果打印为输出，我们需要把输入/输出库 io 引入当前作用域。
// io 库来自标准库，也被称为 std
use std::io; 

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new(); // 1. 创建一个空字符串盒子

    io::stdin()
        .read_line(&mut guess) // 2. 把用户输入的东西装进这个盒子里
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

库: 

- 默认情况下，Rust 会把标准库中的一组内容自动带入每个程序的作用域，这组内容被称为 *预导入（prelude）*。你可以在[标准库文档](https://doc.rust-lang.org/std/prelude/index.html)中查看 prelude 里都有哪些内容。

- 如果你需要的类型不在 prelude 中，就必须使用 `use` 语句显式地将其引入作用域。`std::io` 库提供了许多有用的功能，其中包括接收用户输入。

### 使用变量储存值

接下来，创建一个 **变量**（*variable*）来储存用户输入，像这样：

```rust
let mut guess = String :: new();
```

在变量名前使用 `mut` 来使一个变量可变：

- 我们将会在第三章的 [“变量与可变性”](https://kaisery.github.io/trpl-zh-cn/ch03-01-variables-and-mutability.html#变量和可变性) 部分详细讨论这个概念

```rust 
let apples = 5; // 不可变
let mut bananas = 5; // 可变
```

等号的右边是 `guess` 所绑定的值，它是 `String::new` 的结果，这个函数会返回一个 `String` 的新实例。[`String`](https://doc.rust-lang.org/std/string/struct.String.html) 是一个标准库提供的字符串类型，它是 UTF-8 编码的可增长文本块。

`::new` 那一行的 `::` 语法表明 `new` 是 `String` 类型的一个 **关联函数**（*associated function*）。关联函数是针对某个类型实现的函数，在这个例子中是 `String`。这个 `new` 函数创建了一个新的空字符串。你会发现许多类型上都有一个 `new` 函数，因为这是为某种类型创建新值的常用函数名。

- 它的意思是：**创建一个名为 `guess` 的、可变的、类型为字符串（`String`）的空变量。**

### 接受用户输入

现在调用 `io` 库中的函数 `stdin`，这允许我们处理用户输入：

```rust
io::stdin()
	.read_line(&mut guess)
```

如果程序的开头没有使用 `use std::io;` 引入 `io` 库，我们仍可以通过把函数调用写成 `std::io::stdin` 来使用该函数。`stdin` 函数返回一个 [`std::io::Stdin`](https://doc.rust-lang.org/std/io/struct.Stdin.html) 的实例，这是一种代表终端标准输入句柄的类型。

接下来，代码中的 `.read_line(&mut guess)` 调用了标准输入句柄上的 [`read_line`](https://doc.rust-lang.org/std/io/struct.Stdin.html#method.read_line) 方法，以获取用户输入。

- 将 `&mut guess` 作为参数传递给 `read_line` 函数
- `&` 表示这个参数是一个 **引用**（*reference*）
  - 它允许多处代码访问同一处数据，而无需在内存中多次拷贝。引用是一个复杂的特性，Rust 的一个主要优势就是安全而简单的操纵引用
  - 我们只需知道它像变量一样，默认是不可变的。因此，需要写成 `&mut guess` 来使其可变，而不是 `&guess`。（第四章会更全面地讲解引用。）



### 使用Result类型来处理潜在的错误

```rust
.expect("Failed to read line");
```

`read_line` 会将用户输入附加到传递给它的字符串中，不过它也会返回一个类型为 `Result` 的值。

- [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html) 是一种[*枚举类型*](https://kaisery.github.io/trpl-zh-cn/ch06-00-enums.html)，通常也写作 *enum*，它可以是多种可能状态中的一个。我们把每种可能的状态称为一种 **枚举成员**（*variant*）。

> [第六章](https://kaisery.github.io/trpl-zh-cn/ch06-00-enums.html)将介绍枚举的更多细节。这里的 `Result` 类型将用来编码错误处理的信息

`Result` 的成员是 `Ok` 和 `Err`

- `Ok` 成员表示操作成功，内部包含成功时产生的值。
- `Err` 成员则意味着操作失败，并且 `Err` 中包含有关操作失败的原因或方式的信息。

`Result` 类型的值，像其他类型一样，拥有定义于其实例上的方法。`Result` 的实例拥有 [`expect` 方法](https://doc.rust-lang.org/std/result/enum.Result.html#method.expect)。如果 `io::Result` 实例的值是 `Err`，`expect` 会导致程序崩溃，并输出当做参数传递给 `expect` 的信息。所以当 `read_line` 方法返回 `Err`，则可能是来源于底层操作系统错误的结果。如果 `Result` 实例的值是 `Ok`，`expect` 会获取 `Ok` 中的值并原样返回。在本例中，这个值是用户输入到标准输入中的字节数。

- 如果不调用 `expect`，程序也能编译，不过会出现一个警告
- 消除警告的正确做法是实际去编写错误处理代码，不过由于我们就是希望程序在出现问题时立即崩溃，所以直接使用 `expect`。[第九章](https://kaisery.github.io/trpl-zh-cn/ch09-02-recoverable-errors-with-result.html) 会学习如何从错误中恢复。



### 使用`println!` 占位符打印值

```rust
   println!("You guessed: {guess}");
```



### 测试

```shell
cargo run 
```



## 生成一个秘密数字

接下来，需要生成一个秘密数字，好让用户来猜。

秘密数字应该每次都不同，这样重复玩才不会乏味；范围应该在 1 到 100 之间，这样才不会太困难。

- Rust 标准库中尚未包含随机数功能。然而，Rust 团队还是提供了一个包含上述功能的 [`rand` crate](https://crates.io/crates/rand)



### 使用crate来增加更多功能

crate 是一组 Rust 源代码文件。我们正在构建的项目是一个 *二进制 crate*，它会生成一个可执行文件。

`rand` crate 则是一个 *库 crate*，库 crate 可以包含任意能被其他程序使用的代码，但不能独立执行

文件名：Cargo.toml

```toml
[dependencies]
rand = "0.9.3"
```

`[dependencies]` section 用来告诉 Cargo：这个项目依赖哪些外部 crate，以及它们的版本要求。

- Cargo 理解 [语义化版本（Semantic Versioning）](http://semver.org/)，也常简称为 *SemVer*，它是一种编写版本号的标准。`0.8.5` 实际上是 `^0.8.5` 的简写，表示任何至少为 `0.8.5` 但小于 `0.9.0` 的版本

然后cargo build 重新构建

```rust
cargo build
```

### [*Cargo.lock* 文件确保可重现构建](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#cargolock-文件确保可重现构建)

当你第一次构建项目时，Cargo 会计算出所有符合要求的依赖版本，并把它们写入 *Cargo.lock* 文件。之后再次构建项目时，Cargo 会发现 *Cargo.lock* 已经存在，于是直接使用其中记录的版本，而不是重新计算一遍。这就让你自动拥有了可重现构建（reproducible build）。换句话说，项目会一直停留在 `0.8.5`，直到你显式升级它，这都要归功于 *Cargo.lock* 文件。由于 *Cargo.lock* 对可重现构建非常重要，所以它通常会和项目中的其余代码一起提交到版本控制系统中。



### 更新crate到一个新版本

当你 **确实** 想更新某个 crate 时，Cargo 提供了 `update` 命令，它会忽略 *Cargo.lock* 文件，并重新计算所有符合 *Cargo.toml* 中声明要求的最新版本。然后，Cargo 会把这些版本写回 *Cargo.lock* 文件。不过，默认情况下，Cargo 只会查找大于 `0.8.5` 且小于 `0.9.0` 的版本。如果 `rand` crate 发布了两个新版本：`0.8.6` 和 `0.999.0`，那么运行 `cargo update` 时你会看到如下输出：

```shell
$ cargo update
    Updating crates.io index
     Locking 1 package to latest Rust 1.85.0 compatible version
    Updating rand v0.8.5 -> v0.8.6 (available: v0.999.0)

```

Cargo 会忽略 `0.999.0` 这个版本。这时，你也会注意到 *Cargo.lock* 文件发生了变化：你现在使用的 `rand` crate 版本变成了 `0.8.6`。如果你想使用 `rand` 的 `0.999.0` 版本，或者 `0.999.x` 系列中的任何版本，就必须像下面这样更新 *Cargo.toml* 文件：

```toml
[dependencies]
rand = "0.999.0"
```



### 生成一个随机数

```rust
use std::io;

use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

新增了一行 `use rand::Rng;`。`Rng` 是一个 trait，它定义了随机数生成器应实现的方法，想使用这些方法的话，此 trait 必须在作用域中。第十章会详细介绍 trait。

调用 `rand::thread_rng` 函数，得到我们将要使用的那个特定随机数生成器：

- 它与当前执行线程局部相关，并由操作系统提供种子（seed）。
- 然后，我们在这个随机数生成器上调用 `gen_range` 方法。这个方法由通过 `use rand::Rng` 语句引入作用域的 `Rng` trait 定义。`gen_range` 方法接收一个范围表达式（range expression）作为参数，并在该范围内生成一个随机数。
- 这里我们使用的范围表达式形式是 `start..=end`，它在上下边界上都是闭区间，所以我们需要写 `1..=100`，来请求一个 1 到 100 之间的数。

运行代码

```shell
cargo run 
```



## 比较猜测的数字和秘密数字

比较: (这段代码无法通过编译)

```rust
use std::cmp::Ordering; // 枚举
use std::io;

use rand::Rng;

fn main() {
    // --snip--
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is: {secret_number}");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");

  //比较
    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

我们增加了另一条 `use` 声明，把标准库中的 `std::cmp::Ordering` 类型引入作用域。

- `Ordering` 也是一个枚举，它的成员分别是 `Less`、`Greater` 和 `Equal`。这正是比较两个值时可能出现的三种结果。

`cmp` 方法用于比较两个值，并且可以在任何可比较的值上调用。

- 它接收一个对比对象的引用：这里就是把 `guess` 和 `secret_number` 作比较。
- 然后，它会返回一个我们刚刚通过 `use` 引入作用域的 `Ordering` 枚举成员。
- 我们使用 [`match`](https://kaisery.github.io/trpl-zh-cn/ch06-02-match.html) 表达式，根据对 `guess` 和 `secret_number` 调用 `cmp` 后返回的是哪个 `Ordering` 成员，来决定下一步该做什么。

一个 `match` 表达式由 **分支（arms）** 构成。

- 一个分支包含一个 **模式**（*pattern*）和表达式开头的值与分支模式相匹配时应该执行的代码。Rust 获取提供给 `match` 的值并挨个检查每个分支的模式。`match` 结构和模式是 Rust 中强大的功能，它体现了代码可能遇到的多种情形，并确保对所有情况作出处理。这些功能将分别在第六章和第十九章详细介绍。

> 但这段代码无法编译

```shell
cargo build
```

错误的核心表明这里有 **不匹配的类型**（*mismatched types*）。

- Rust 有一个静态强类型系统，同时也有类型推断。当我们写出 `let guess = String::new()` 时，Rust 推断出 `guess` 应该是 `String` 类型，并不需要我们写出类型。另一方面，`secret_number`，是数字类型。

所以我们必须把从输入中读取到的 `String` 转换为一个数字类型，才好与秘密数字进行比较。

这行新代码是：

```rust
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

这里创建了一个名为 `guess` 的变量

- 虽然之前有了这个名为Guess的变量
  - 但是, Rust 很贴心地允许我们用一个新值来 **遮蔽**（*shadowing*）之前的 `guess`, 这样一来，我们就可以继续复用 `guess` 这个变量名，而不必被迫创建两个不同的变量
  - [第三章](https://kaisery.github.io/trpl-zh-cn/ch03-01-variables-and-mutability.html#遮蔽)会更详细地介绍 shadowing；现在你只需要知道，这个特性经常被用来把一个值从一种类型转换为另一种类型。
- 我们把这个新变量绑定到 `guess.trim().parse()` 这个表达式上。
  - 表达式里的 `guess` 指向的是原先那个保存用户输入字符串的 `guess` 变量。`String` 实例上的 `trim` 方法会去掉字符串开头和结尾的空白字符；
  - 在把字符串转换为 `u32` 之前，我们必须先这么做，因为 `u32` 只能包含数值数据。
  - 用户必须按下 enter 才能让 `read_line` 返回并提交他们的猜测，这会在字符串中附加一个换行符（newline）。
    - 例如，如果用户输入 5 并按下 enter，`guess` 实际上会变成 `5\n`。（在 Windows 上，按下 enter 会得到回车加换行，也就是 `\r\n`。）`trim` 方法会去除 `\n` 或 `\r\n`，最后只留下 `5`。
- [字符串的 `parse` 方法](https://doc.rust-lang.org/std/primitive.str.html#method.parse) 将字符串转换成其他类型。
  - 这里用它来把字符串转换为数值。
  - 我们需要告诉 Rust 具体的数字类型，这里通过 `let guess: u32` 指定。`guess` 后面的冒号（`:`）告诉 Rust 我们指定了变量的类型。
  - Rust 有一些内建的数字类型；`u32` 是一个无符号的 32 位整型。对于不大的正整数来说，它是不错的默认类型，[第三章](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#整型)还会讲到其他数字类型。

>  另外，程序中的 `u32` 注解以及与 `secret_number` 的比较，意味着 Rust 会推断出 `secret_number` 也是 `u32` 类型。现在可以使用相同类型比较两个值了！



## 使用循环

`loop` 关键字创建了一个无限循环。我们会增加循环来给用户更多机会猜数字：

```rust
use std::cmp::Ordering;
use std::io;

use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

		// generate the secret no. first 
    println!("The secret number is: {secret_number}");

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse().expect("Please type a number!");

        println!("You guessed: {guess}");

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```

如你所见，我们把从提示用户输入猜测开始往后的所有内容都移进了循环中。请确保循环中的代码都额外缩进四个空格，然后再次运行程序。程序现在会不断要求用户输入新的猜测，但这也引入了一个新问题。用户似乎没法退出了！

- 用户当然总可以用键盘快捷键 ctrl-C 中断程序
  - 正如我们在 [“比较猜测的数字和秘密数字”](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#比较猜测的数字和秘密数字) 一节讨论 `parse` 时提到的那样：如果用户输入的不是数字，程序就会崩溃。我们可以利用这一点来让用户退出，比如输入 string

### 猜测正确后退出

让我们增加一个 `break` 语句，在用户猜对时退出游戏：

```rust
 match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
```



### 处理无效输入

为了进一步改进游戏体验，我们不希望程序在用户输入非数字时崩溃；相反，我们希望它忽略这些输入，让用户继续猜。我们可以通过修改将 `guess` 从 `String` 转换为 `u32` 的那行代码来做到这一点，如示例 2-5 所示：

```rust
 let guess: u32 = match guess.trim().parse() {
   // 1. 如果成功了（Ok），把里面那个数字“抓”出来，临时起名叫 num
            Ok(num) => num,
   // 2. 如果失败了（Err），忽略错误（_ 代表不关心具体错误），直接跳过（continue）
            Err(_) => continue,
        };
```

我们将 `expect` 调用换成 `match` 语句，以从遇到错误就崩溃转换为处理错误。须知 `parse` 返回的是一个 `Result` 类型，而 `Result` 是一个拥有 `Ok` 和 `Err` 成员的枚举。这里使用的 `match` 表达式，和我们之前处理 `cmp` 方法返回的 `Ordering` 时用的是同一种方式。

如果 `parse` 能够成功地将字符串转换为一个数字，它会返回一个包含结果数字的 `Ok`。这个 `Ok` 值与 `match` 第一个分支的模式相匹配，该分支对应的动作返回 `Ok` 值中的数字 `num`，最后如愿变成新创建的 `guess` 变量

如果 `parse` **不能**把字符串转换为数字，它就会返回一个包含更多错误信息的 `Err`。

- 这个 `Err` 值无法匹配第一个 `match` 分支中的 `Ok(num)` 模式，但会匹配第二个分支里的 `Err(_)` 模式。
- 下划线 `_` 是一个==通配值==；在这个例子里，它表示我们要匹配所有 `Err` 值，而不关心其中具体包含什么信息。
- 因此，程序会执行第二个分支中的代码 `continue`，告诉程序进入 `loop` 的下一次迭代，并请求另一个猜测。于是，程序就有效地忽略了 `parse` 可能遇到的所有错误！



### 完整代码

> 记得删掉打印出秘密数字

```rust
use std::cmp::Ordering;
use std::io;

use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {guess}");

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

