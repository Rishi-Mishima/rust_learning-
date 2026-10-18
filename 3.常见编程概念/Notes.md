# 3. 常见编程概念

> 将学习变量、基本类型、函数、注释和控制流



关键字: 

- Rust 语言有一组保留的 **关键字**（*keywords*）
- 你不能使用这些关键字作为变量或函数的名称
- 可以在[附录 A](https://kaisery.github.io/trpl-zh-cn/appendix-01-keywords.html) 中找到关键字的列表



## 3.1 变量和可变性

[ch03-01-variables-and-mutability.md](https://github.com/rust-lang/book/blob/9cc190796f28505c7a9a9cacea42f50d895ff3bd/src/ch03-01-variables-and-mutability.md)

变量默认是不可变的（immutable）

生成一个新项目: 

```shell 
cargo new variables --vcs none 
```

文件名：src/main.rs

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

保存并使用 `cargo run` 运行程序。应该会看到一条与不可变性有关的错误信息

所以需要变成 `let mut x`

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

### 常量

不能对常量使用 `mut`

- 常量不只是默认不可变，它们永远都是不可变的

声明常量时要用 `const` 关键字

- 并且 *必须* 标注值的类型
- 在下一节[“数据类型”](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#数据类型)中，我们会介绍类型和类型注解：**声明常量时必须总是标注类型。**

常量只能被设置为常量表达式，而不能是那些只能在运行时计算出来的值。

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

Rust 对常量的命名约定是==全部大写==，并用下划线分隔单词。

### 遮蔽(shadowed)

正如我们在[第二章](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#比较猜测的数字和秘密数字)的猜数字游戏中看到的，我们可以定义一个与之前变量同名的新变量。

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```

输出: 

```shell
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

> 遮蔽和把变量标记为 `mut` 是不同的

`mut` 和遮蔽之间的另一个区别是：当我们再次使用 `let` 时，实际上是在创建一个新变量，因此我们可以改变值的类型，同时继续复用相同的名字。

例如，假设程序要求用户输入若干空格，以表示他们希望在某段文本之间显示多少空格，随后我们想把这个输入保存成一个数字：

```rust
    let spaces = "   ";
    let spaces = spaces.len();
```

第一个 `spaces` 变量是字符串类型，而第二个 `spaces` 变量是数字类型。遮蔽让我们不必想出不同的名字，比如 `spaces_str` 和 `spaces_num`；相反，我们可以继续复用更简单的 `spaces`。不过，如果像下面这样尝试使用 `mut`，就会得到一个编译时错误：

```rust
    let mut spaces = "   ";
    spaces = spaces.len();
```

这个错误说明，我们不能修改变量的类型



## 3.2 数据类型

[ch03-02-data-types.md](https://github.com/rust-lang/book/blob/9cc190796f28505c7a9a9cacea42f50d895ff3bd/src/ch03-02-data-types.md)

在 Rust 中，每个值都属于某种特定的 **数据类型**（*data type*），这会告诉 Rust 当前指定的是什么种类的数据，从而知道该如何处理这些数据。我们将看到两类数据类型的子集：标量（scalar）和复合（compound）。

记住，Rust 是 **静态类型**（*statically typed*）语言，也就是说，它必须在编译时就知道所有变量的类型。编译器通常可以根据值以及它的使用方式推断出我们想要使用的类型。但在存在多种可能类型的情况下，比如我们在第二章[“比较猜测的数字和秘密数字”](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#比较猜测的数字和秘密数字)一节中使用 `parse` 将 `String` 转换为数值类型时，就必须加上类型注解，像这样：

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

如果不按前面的代码那样加上 `: u32` 类型注解，Rust 就会显示如下错误。

 #### Rust 严厉的“错误处理”铁律

Rust 是一门极其强调安全性的语言。它有一条铁律：**如果一个函数可能返回错误（即返回 `Result`），你就绝对不能直接把它当成成功的结果来用，你必须显式地处理错误。**

如果你写成：

```rust
let guess: u32 = "42".parse(); // 错误！类型不匹配
```

编译器会直接报错，因为左边是 `u32`（数字），右边是 `Result<u32, ParseIntError>`（结果包裹），它们类型不同，无法直接赋值。

所以，你必须告诉编译器你打算怎么处理这个 `Result`：

- 用 `.expect("...")`：意思是“我知道你返回的是 `Result`，但我向你保证这里面绝对没问题。如果真出错了，你就直接崩溃”。
- 或者用 `match` 去匹配。

虽然对于 `"42"` 来说 `.expect()` 显得有点多此一举，但这是 Rust 统一的语法规则。**不管字符串内容是什么，只要调用了 `.parse()`，就必须处理它带来的 `Result`。**



### 标量类型(scalar)

**标量**（*scalar*）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。你可能在其他语言中见过它们。让我们深入了解它们在 Rust 中是如何工作的。



#### 整型

**整型** 是没有小数部分的数字。我们在第二章已经用过 `u32` 这种整数类型。这个类型声明表明，它关联的值应该是一个占用 32 位空间的无符号整数（有符号整数类型以 `i` 开头，而不是 `u`）。表格 3-1 展示了 Rust 内建的整数类型。我们可以使用其中任意一种来声明整数值的类型。

| 长度     | 有符号  | 无符号  |
| -------- | ------- | ------- |
| 8-bit    | `i8`    | `u8`    |
| 16-bit   | `i16`   | `u16`   |
| 32-bit   | `i32`   | `u32`   |
| 64-bit   | `i64`   | `u64`   |
| 128-bit  | `i128`  | `u128`  |
| 架构相关 | `isize` | `usize` |

>  **有符号** 和 **无符号** 指的是数字是否可能为负数

如果可以安全地假定它是正数，通常就不会写加号。有符号数使用[二进制补码（two’s complement）](https://en.wikipedia.org/wiki/Two's_complement)表示

⚠️注意 整型溢出

- Rust 用 *panicking* 这个术语表示程序因错误而退出；我们会在第九章[“`panic!` 与不可恢复的错误”](https://kaisery.github.io/trpl-zh-cn/ch09-01-unrecoverable-errors-with-panic.html)一节中更深入地讨论 panic。

为了显式地处理溢出的可能性，可以使用这几类标准库提供的原始数字类型方法：

- 所有模式下都可以使用 `wrapping_*` 方法进行 wrapping，如 `wrapping_add`
- 如果 `checked_*` 方法发生溢出，则返回 `None` 值
- 用 `overflowing_*` 方法返回值和一个布尔值，表示是否出现溢出
- 用 `saturating_*` 方法在值的最小值或最大值处进行饱和处理



#### 浮点型

Rust 也有两个原生的 **浮点数**（*floating-point numbers*）类型，它们是带小数点的数字。Rust 的浮点数类型是 `f32` 和 `f64`，分别占 32 位和 64 位。默认类型是 `f64`，因为在现代 CPU 中，它与 `f32` 速度几乎一样，不过精度更高。所有的浮点型都是有符号的

文件名：src/main.rs

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

浮点数按照 IEEE-754 标准表示。



#### 数值运算

文件名：`src/bin/calculation.rs` 

Rust 中的所有数字类型都支持基本数学运算：加法、减法、乘法、除法和取余。整数除法会向零舍入到最接近的整数。下面的代码展示了如何在 `let` 语句中使用各种数值运算：

```rust
 // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;
    let truncated = -5 / 3; // 结果为 -1

    // remainder
    let remainder = 43 % 5;
```

这些语句中的每个表达式使用了一个数学运算符并计算出了一个值，然后绑定给一个变量。[附录 B](https://kaisery.github.io/trpl-zh-cn/appendix-02-operators.html) 包含 Rust 提供的所有运算符的列表。



#### 布尔类型

Rust 中的布尔类型有两个可能的值：`true` 和 `false`。Rust 中的布尔类型使用 `bool` 表示

文件名：src/bin/bool.rs

```rust
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
```

布尔值最主要的使用场景是条件表达式，例如 `if` 表达式。我们会在[“控制流”](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#控制流)一节介绍 `if` 表达式在 Rust 中是如何工作的。



#### 字符类型

Rust 的 `char` 类型是语言中最原始的字母类型。

文件名：src/bin/bool.rs

```rust
fn main(){
    let c = 'z';
    let z: char = 'ℤ'; // with explicit type annotation
    let heart_eyed_cat = '😻';
}
```

我们使用单引号来表示 `char` 字面值，而字符串字面值使用的是双引号



#### 复合类型

**复合类型**（*compound types*）可以把多个值组合成一个类型。Rust 有两种原生的复合类型：元组（tuple）和数组（array）。

##### 元祖类型

元组是一种将多个不同类型的值组合成一个复合类型的通用方式。元组长度固定：一旦声明，它的大小就不能增长或缩小。

我们通过在圆括号中写一组由逗号分隔的值来创建元组。元组中的每个位置都有一个类型，而且这些不同位置上的值类型不必相同。下面这个例子中加入了可选的类型注解：

文件名: src/bin/tuple.rs

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```



从元祖类型中模式匹配

- （pattern matching）来解构（destructure）元组

文件名: src/bin/tupleDistructure.rs

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {y}");
}
```



我们也可以使用点号（`.`）后跟值的索引来直接访问所需的元组元素。例如：

文件名: src/bin/tupleDot.rs

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```



单元(unit): 

- 不带任何值的元组有一个特殊名字，叫做 **单元（unit）**。这种值以及其对应的类型都写作 `()`，表示空值或空的返回类型。如果一个表达式没有返回任何其他值，它就会隐式返回单元值。



##### 数组类型

另一种包含多个值的方式是 **数组**（*array*）。和元组不同，数组中的每个元素都必须具有相同类型。Rust 中的数组也不同于某些其他语言中的数组：==Rust 的数组长度是固定的==。

文件名: src/bin/array.rs

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

当你希望把数据分配在栈（stack）上而不是堆（heap）上时（[第四章](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#栈stack与堆heap)会更详细地讨论栈与堆），或者当你想确保始终拥有固定数量的元素时，数组就非常有用。

- 但是数组不如vector灵活, [第八章](https://kaisery.github.io/trpl-zh-cn/ch08-01-vectors.html)会更详细地讨论 vector。

可以像这样编写数组的类型：在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
let b: [i32; 1] = [0];
```

这里，`i32` 是每个元素的类型。分号之后，数字 `5` 表明该数组包含五个元素。

你还可以通过在方括号中指定初始值加分号再加元素个数的方式来创建一个每个元素都为相同值的数组：

```rust
let a = [3; 5];
```

变量名为 `a` 的数组将包含 `5` 个元素，这些元素的值最初都将被设置为 `3`。这种写法与 `let a = [3, 3, 3, 3, 3];` 效果相同，但更简洁。



###### 访问数组元素

数组是在栈（stack）上分配的一整块、大小已知且固定的内存。

访问数组元素

文件名: src/bin/arrayIndex.rs

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```





###### 无效的数组元素访问

如果尝试访问数组末尾之后的元素

文件名：src/bin/arrayIndexError.rs

```rust
use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];

    println!("Please enter an array index.");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");

    let element = a[index];

    println!("The value of the element at index {index} is: {element}");
}
```

