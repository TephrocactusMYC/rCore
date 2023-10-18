# day1
## 写在最前
很好，报名了开源操作系统训练营2023秋冬季。

然而由于时间问题，在开课已经一周已有200多人顺利晋级的情况下，才刚开始（蚌埠住了！） :(

那么计划一共花七天左右的时间来入门 `Rust` 语言并且完成开源操作系统训练营魔改版本的 100 道 rustlings 题目（原版为94题）！

个人仅采用 [RUST 语言程序设计](https://kaisery.github.io/trpl-zh-cn/title-page.html)和[Rust语言圣经](https://course.rs/about-book.html)进行学习，没有跟课程走。

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

### 编写一个猜数字游戏
```rust,editable
use rand::Rng;
use std::cmp::Ordering;
use std::io;

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
第一行，默认情况下，Rust 设定了若干个会自动导入到每个程序作用域中的标准库内容，这组内容被称为 预导入（preclude） 内容。

main 函数是程序的入口点

println! 是一个在屏幕上打印字符串的宏

变量（variable）来储存用户输入

读取输入
```rust
io::stdin()
        .read_line(&mut guess)
```
stdin 函数返回一个 std::io::Stdin 的实例，这代表终端标准输入句柄的类型。

代码的下一部分，.read_line(&mut guess)，调用 read_line 方法从标准输入句柄获取用户输入。我们还将 &mut guess 作为参数传递给 read_line() 函数，让其将用户输入储存到这个字符串中。read_line 的工作是，无论用户在标准输入中键入什么内容，都将其追加（不会覆盖其原有内容）到一个字符串中，因此它需要字符串作为参数。这个字符串参数应该是可变的，以便 read_line 将用户输入附加上去。

& 表示这个参数是一个 引用（reference），它允许多处代码访问同一处数据，而无需在内存中多次拷贝。引用是一个复杂的特性，Rust 的一个主要优势就是安全而简单的操纵引用。

read_line 会将用户输入附加到传递给它的字符串中，不过它也会返回一个类型为 Result 的值。 Result 是一种枚举类型，通常也写作 enum。枚举类型变量的值可以是多种可能状态中的一个。我们把每种可能的状态称为一种 枚举成员（variant）。

crate 是一个 Rust 代码包。我们正在构建的项目是一个 二进制 crate，它生成一个可执行文件。 rand crate 是一个 库 crate，库 crate 可以包含任意能被其他程序使用的代码，但是不能自执行。

>运行 cargo doc --open 命令来构建所有本地依赖提供的文档，并在浏览器中打开。

一个 match 表达式由 分支（arms） 构成。一个分支包含一个 模式（pattern）和表达式开头的值与分支模式相匹配时应该执行的代码。

loop 关键字创建了一个无限循环。
### 题外话
编程语言有静态类型与动态类型之分，也有强类型和弱类型之分
#### 静态类型 VS 动态类型
静态类型语言中，变量的类型必须先声明，即在创建的那一刻就已经确定好变量的类型，而后的使用中，你只能将这一指定类型的数据赋值给变量。如果强行将其他不相干类型的数据赋值给它，就会引发错误。在静态语言中，一旦声明一个变量是int类型，之后就只能将int类型的数据赋值给它，否则就会引发错误，而动态类型则没有这样的限制，你将什么类型的数据赋值给变量，这个变量就是什么类型

以下语言，皆属于动态类型：
- PHP
- Ruby
- Python

常见的静态类型语言则有：
- C
- C++
- JAVA
- C#
#### 强类型 VS 弱类型
强弱之分，体现在对类型的检查严格程度上，弱类型语言对于变量类型的检查比较宽松，容忍隐式类型转换这种事情的发生。何为隐式类型转换，一般有两种形式：
1. 相关类型之间隐式转换
2. 不相关类型之隐式间转换

举例子来说，一个int类型的数据与一个float类型的数据相加，最终的结果是一个float类型的数据，这个过程就发生了隐式类型转换，int类型数据首先被转成float类型，然后与另一个float进行操作，这便是相相关类型之间隐式转换。

一个int类型数据与一个字符串类型数据相加，竟然没有发生错误，得到的结果是一个字符串，int类型数据隐式转换为字符串，可他们原本是两个不相干的数据类型，这种就是第二种隐式转换。

在弱类型语言中，变量可以隐式强制转换为不相关类型，而在强类型语言中则不可以。按照这种概念定义来划分，PHP，Perl都属于弱类型语言，其他编程语言，你所常见的，比如java, C, C++, Python皆属于强类型语言。

#### RUST?
属于静态强类型语言
#### ref
参考[知乎](https://zhuanlan.zhihu.com/p/109803872)

### 常见编程概念
#### 变量和可变性
变量默认是不可改变的（immutable）

>在尝试改变预设为不可变的值时，产生编译时错误是很重要的，因为这种情况可能导致 bug。如果一部分代码假设一个值永远也不会改变，而另一部分代码改变了这个值，第一部分代码就有可能以不可预料的方式运行。不得不承认这种 bug 的起因难以跟踪，尤其是第二部分代码只是 有时 会改变值。

Rust 编译器保证，如果声明一个值不会变，它就真的不会变，所以你不必自己跟踪它。这意味着你的代码更易于推导。

**如何改变？**使用`mut`!

###### 变量绑定
>在其它语言中，我们用 var a = "hello world" 的方式给 a 赋值，也就是把等式右边的 "hello world" 字符串赋值给变量 a ，而在 Rust 中，我们这样写： let a = "hello world" ，同时给这个过程起了另一个名字：变量绑定。

为何不用赋值而用绑定呢（其实你也可以称之为赋值，但是绑定的含义更清晰准确）？这里就涉及 Rust 最核心的原则——所有权，简单来讲，任何内存对象都是有主人的，而且一般情况下完全属于它的主人，绑定就是把这个对象绑定给一个变量，让这个变量成为它的主人（聪明的读者应该能猜到，在这种情况下，该对象之前的主人就会丧失对该对象的所有权）

##### 常量
不允许对常量使用 mut。常量不光默认不可变，它总是不可变。声明常量使用 const 关键字而不是 let，并且 必须 注明值的类型。
常量可以在任何作用域中声明，包括全局作用域，这在一个值需要被很多部分的代码用到时很有用。

最后一个区别是，常量只能被设置为常量表达式，而不可以是其他任何只能在运行时计算出的值。
```rust
fn main() {
    const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
}
```

##### 其他
使用下划线开头忽略未使用的变量
```rust
fn main() {
    let _x = 5;
    let y = 10;
}
```

变量解构
```rust
fn main() {
    let (a, mut b): (bool,bool) = (true, false);
    // a = true,不可变; b = false，可变
    println!("a = {:?}, b = {:?}", a, b);

    b = true;
    assert_eq!(a, b);
}
```
##### 变量遮蔽(shadowing)
Rust 允许声明相同的变量名，在后面声明的变量会遮蔽掉前面声明的
```rust
fn main() {
    let x = 5;
    // 在main函数的作用域内对之前的x进行遮蔽
    let x = x + 1;

    {
        // 在当前的花括号作用域内，对之前的x进行遮蔽
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}
```
>这和 mut 变量的使用是不同的，第二个 let 生成了完全不同的新变量，两个变量只是恰好拥有同样的名称，涉及一次内存对象的再分配 ，而 mut 声明的变量，可以修改同一个内存地址上的值，并不会发生内存对象的再分配，性能要更好。

变量遮蔽的用处在于，如果你在某个作用域内无需再使用之前的变量（在被遮蔽后，无法再访问到之前的同名变量），就可以重复的使用变量名字，而不用绞尽脑汁去想更多的名字
#### 数据类型
标量（scalar）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。你可能在其他语言中见过它们。让我们深入了解它们在 Rust 中是如何工作的。

|长度	|有符号|	无符号|
|---|---|
|8-bit	|i8	|u8|
|16-bit	|i16	|u16|
|32-bit	|i32	|u32|
||64-bit	|i64	|u64|
|128-bit	|i128	|u128|
|arch	|isize	|usize|

Rust 也有两个原生的 浮点数（floating-point numbers）类型，它们是带小数点的数字。Rust 的浮点数类型是 f32 和 f64，分别占 32 位和 64 位。默认类型是 f64，因为在现代 CPU 中，它与 f32 速度几乎一样，不过精度更高。所有的浮点型都是有符号的。

**注意，我们用单引号声明 char 字面量，而与之相反的是，使用双引号声明字符串字面量。**

单元类型：()

fn main()这个函数返回什么呢？没错， main 函数就返回这个单元类型 ().例如常见的 println!() 的返回值也是单元类型 ()

复合类型（Compound types）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）。

数组中的每个元素的类型必须相同。Rust 中的数组与一些其他语言中的数组不同，Rust 中的数组长度是固定的。
>数组 array 是存储在栈上，性能也会非常优秀。与此对应，动态数组 Vector 是存储在堆上

```rust
#![allow(unused)]
fn main() {
let a: [i32; 5] = [1, 2, 3, 4, 5];
}
```

你还可以通过在方括号中指定初始值加分号再加元素个数的方式来创建一个每个元素都为相同值的数组：
```rust
#![allow(unused)]
fn main() {
let a = [3; 5];
}
```
通过索引访问数组

### 函数
Rust 代码中的函数和变量名使用 snake case 规范风格。在 snake case 中，所有字母都是小写并使用下划线分隔单词。

Rust 不关心函数定义所在的位置，只要函数被调用时出现在调用之处可见的作用域内就行

在函数签名中，**必须**声明每个参数的类型。
```rust
fn add(i: i32, j: i32) -> i32 {
   i + j
 }
```
#### 语句和表达式
Rust 的函数体是由一系列语句组成，最后由一个表达式来返回值
```rust
fn add_with_extra(x: i32, y: i32) -> i32 {
    let x = x + 1; // 语句
    let y = y + 5; // 语句
    x + y // 表达式
}
```
语句（Statements）是执行一些操作但不返回值的指令。 表达式（Expressions）计算并产生一个值。让我们看一些例子。
```rust
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```
**表达式不能包含分号。这一点非常重要，一旦你在表达式后加上分号，它就会变成一条语句，再也不会返回一个值，请牢记！**

函数的返回值就是函数体最后一条表达式的返回值，当然我们也可以使用 return 提前返回
```rust
fn plus_or_minus(x:i32) -> i32 {
    if x > 5 {
        return x - 5
    }

    x + 5
}

fn main() {
    let x = plus_or_minus(5);

    println!("The value of x is: {}", x);
}
```
##### 发散函数
当用 ! 作函数返回类型的时候，表示该函数永不返回( diverge function )
```rust
fn dead_end() -> ! {
  panic!("你已经到了穷途末路，崩溃吧！");
}
```
#### 注释
行注释和块注释与C++相同。

#### if else
```rust
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number);
}
```
if 语句块是表达式，这里我们使用 if 表达式的返回值来给 number 进行赋值：number 的值是 5

用 if 来赋值时，要保证每个分支返回的类型一样
```rust
fn main() {
    let n = 6;

    if n % 4 == 0 {
        println!("number is divisible by 4");
    } else if n % 3 == 0 {
        println!("number is divisible by 3");
    } else if n % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```
#### 循环
##### for
```rust
fn main() {
    for i in 1..=5 {
        println!("{}", i);
    }
}
```
##### while
```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```
##### loop
loop 关键字告诉 Rust 一遍又一遍地执行一段代码直到你明确要求停止。
```rust
fn main() {
    loop {
        println!("again!");
    }
}
```
##### loop label
如果存在嵌套循环，break 和 continue 应用于此时最内层的循环。你可以选择在一个循环上指定一个 循环标签（loop label），然后将标签与 break 或 continue 一起使用，使这些关键字应用于已标记的循环而不是最内层的循环。下面是一个包含两个嵌套循环的示例
```rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

## 做题
好了，那么在学了以上的内容以后，基本上对rust有了最最基本的认识了，可以开始做题了。

第一天做了17题。遇到了切片相关的内容，就此停手。
