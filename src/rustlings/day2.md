# day2
第二天，从rust语言圣经的字符学起。
## 字符
字符
```rust
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let g = '国';
    let heart_eyed_cat = '😻';
}
```
Rust 的字符不仅仅是 ASCII，所有的 Unicode 值都可以作为 Rust 字符，包括单个的中文、日文、韩文、emoji 表情符号等等，都是合法的字符类型。

## 所有权（重中之重！）
首先需要理解堆栈

### 规则
>Rust 中的每一个值都有一个 所有者（owner）。
>值在任一时刻有且只有一个所有者。
>当所有者（变量）离开作用域，这个值将被丢弃。

作用域（scope）。作用域是一个项（item）在程序中有效的范围。
### 字符串
Rust 为我们提供动态字符串类型: String, 该类型被分配到堆上，因此可以动态伸缩，也就能存储在编译时大小未知的文本。
```rust
let s = String::from("hello");
```
`::` 是一种调用操作符，这里表示调用 String 中的 from 方法，因为 String 存储在堆上是动态的，你可以这样修改它：
```rust
fn main() {
    let mut s = String::from("hello");

    s.push_str(", world!"); // push_str() 在字符串后追加字面值

    println!("{}", s); // 将打印 `hello, world!`
}
```

String 类型是一个复杂类型，由存储在栈中的堆指针、字符串长度、字符串容量共同组成，其中堆指针是最重要的，它指向了真实存储字符串内容的堆内存，容量是堆内存分配空间的大小，长度是目前已经使用的大小。

**基本类型绑定是直接拷贝，复杂类型不是**

其实就是C++的RAII模式。

**Rust 永远也不会自动创建数据的 “深拷贝”。**

如果我们 确实 需要深度复制 String 中堆上的数据，而不仅仅是栈上的数据，可以使用一个叫做 clone 的通用函数。

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();

    println!("s1 = {}, s2 = {}", s1, s2);
}
```

变量的所有权总是遵循相同的模式：将值赋给另一个变量时移动它。当持有堆中数据值的变量离开作用域时，其值将通过 drop 被清理掉，除非数据被移动为另一个变量所有。

**函数的参数和返回值也可以转移所有权。**

字符串字面量是切片

#### String 与 &str 的转换
```rust
String::from("hello,world")
"hello,world".to_string()
```
 Rust 不允许去索引字符串

#### 操作字符串
```rust
fn main() {
    let mut s = String::from("Hello ");

    s.push_str("rust");
    println!("追加字符串 push_str() -> {}", s);

    s.push('!');
    println!("追加字符 push() -> {}", s);
}
```

```rust
fn main() {
    let mut s = String::from("Hello rust!");
    s.insert(5, ',');
    println!("插入字符 insert() -> {}", s);
    s.insert_str(6, " I like");
    println!("插入字符串 insert_str() -> {}", s);
}
```

```rust
fn main() {
    let string_replace = String::from("I like rust. Learning rust is my favorite!");
    let new_string_replace = string_replace.replace("rust", "RUST");
    dbg!(new_string_replace);
}
fn main() {
    let string_replace = "I like rust. Learning rust is my favorite!";
    let new_string_replacen = string_replace.replacen("rust", "RUST", 1);
    dbg!(new_string_replacen);
}
fn main() {
    let mut string_replace_range = String::from("I like rust!");
    string_replace_range.replace_range(7..8, "R");
    dbg!(string_replace_range);
}
```

```rust
fn main() {
    let mut string_pop = String::from("rust pop 中文!");
    let p1 = string_pop.pop();
    let p2 = string_pop.pop();
    dbg!(p1);
    dbg!(p2);
    dbg!(string_pop);
}
fn main() {
    let mut string_remove = String::from("测试remove方法");
    println!(
        "string_remove 占 {} 个字节",
        std::mem::size_of_val(string_remove.as_str())
    );
    // 删除第一个汉字
    string_remove.remove(0);
    // 下面代码会发生错误
    // string_remove.remove(1);
    // 直接删除第二个汉字
    // string_remove.remove(3);
    dbg!(string_remove);
}
fn main() {
    let mut string_truncate = String::from("测试truncate");
    string_truncate.truncate(3);
    dbg!(string_truncate);
}
fn main() {
    let mut string_clear = String::from("string clear");
    string_clear.clear();
    dbg!(string_clear);
}
```

```rust
fn main() {
    let string_append = String::from("hello ");
    let string_rust = String::from("rust");
    // &string_rust会自动解引用为&str
    let result = string_append + &string_rust;
    let mut result = result + "!"; // `result + "!"` 中的 `result` 是不可变的
    result += "!!!";

    println!("连接字符串 + -> {}", result);
}
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

// String = String + &str + &str + &str + &str
let s = s1 + "-" + &s2 + "-" + &s3;

```
### 引用和借用
引用和解引用的符号和C一样，但是注意引用是个类型。

可变引用
```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```
**可变引用同时只能存在一个**

**可变引用与不可变引用不能同时存在**

>同一时刻，你只能拥有要么一个可变引用, 要么任意多个不可变引用
>引用必须总是有效的

### 切片(slice)
```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```
## 结构体
直接看代码
```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
    user1.email = String::from("anotheremail@example.com");
```
>初始化实例时，每个字段都需要进行初始化
>初始化时的字段顺序不需要和结构体定义时的顺序一致

语法糖：
```rust
fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}

fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

#### 更新
```rust
  let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
  let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };

```

### 元组结构体(Tuple Struct)
结构体必须要有名称，但是结构体的字段可以没有名称，这种结构体长得很像元组，因此被称为元组结构体
```rust
    struct Color(i32, i32, i32);
    struct Point(i32, i32, i32);

    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
```
>元组结构体在你希望有一个整体名称，但是又不关心里面字段的名称时将非常有用。例如上面的 Point 元组结构体，众所周知 3D 点是 (x, y, z) 形式的坐标点，因此我们无需再为内部的字段逐一命名为：x, y, z。

### 单元结构体(Unit-like Struct)
如果你定义一个类型，但是不关心该类型的内容, 只关心它的行为时，就可以使用 单元结构体
## 枚举
```
enum PokerCard {
    Clubs(u8),
    Spades(u8),
    Diamonds(u8),
    Hearts(u8),
}

fn main() {
   let c1 = PokerCard::Spades(5);
   let c2 = PokerCard::Diamonds(13);
}
```
## 做题
不知为什么昨天题目紧接着是vec，那么先学一下vec吧

由于跳过了泛型，这一部分在后面应该补一下底层
### vec
动态数组类型用 Vec<T> 表示

动态数组允许你存储多个值，这些值在内存中一个紧挨着另一个排列，因此访问其中某个元素的成本非常低。动态数组只能存储相同类型的元素，如果你想存储不同类型的元素，可以使用枚举类型或者特征对象。

### 创建
```rust
let v: Vec<i32> = Vec::new();

//or

let mut v = Vec::new();
v.push(1);//这里也是更新的方法

//or

let v = vec![1, 2, 3];
```

### 读取 vector 的元素
```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("第三个元素是 {}", third);

match v.get(2) {
    Some(third) => println!("第三个元素是 {third}"),
    None => println!("去你的第三个元素，根本没有！"),
}
```
### 遍历
```rust
fn main() {
    let v = vec![100, 32, 57];
    for i in &v {
        println!("{i}");
    }
}

fn main() {
    let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50;
    }
}
```
### 使用枚举来储存多种类型
```rust
fn main() {
    enum SpreadsheetCell {
        Int(i32),
        Float(f64),
        Text(String),
    }

    let row = vec![
        SpreadsheetCell::Int(3),
        SpreadsheetCell::Text(String::from("blue")),
        SpreadsheetCell::Float(10.12),
    ];
}
```
### 排序
在 rust 里，实现了两种排序算法，分别为稳定的排序 sort 和 sort_by，以及非稳定排序 sort_unstable 和 sort_unstable_by。

当然，这个所谓的 非稳定 并不是指排序算法本身不稳定，而是指在排序过程中对相等元素的处理方式。在 稳定 排序算法里，对相等的元素，不会对其进行重新排序。而在 不稳定 的算法里则不保证这点。

总体而言，非稳定 排序的算法的速度会优于 稳定 排序算法，同时，稳定 排序还会额外分配原数组一半的空间

## 正式开始做题

有意思的是vecs2

其中
```rust
v.iter().map(|element| {//这一行代码使用v.iter()函数获取向量v的迭代器，然后使用.map()函数对迭代器中的每个元素进行映射操作。|element|表示对每个元素进行操作，element是一个临时变量，用于存储迭代器中的每个元素。
}).collect()//这一行代码将映射操作后的结果收集到一个新的向量中，并返回该向量。

//因此，整个函数的作用是对一个整数类型的向量进行映射操作，将向量中的每个元素都加上1，并返回一个新的向量。在Rust语言中，.iter()函数用于获取迭代器，.map()函数用于对迭代器中的每个元素进行映射操作，|element|表示对每个元素进行操作，collect()函数用于将映射操作后的结果收集到一个新的向量中。
```

### 方法method
方法（method）与函数类似：它们使用 fn 关键字和名称声明，可以拥有参数和返回值，同时包含在某处调用该方法时会执行的代码。不过方法与函数是不同的，因为它们在结构体的上下文中被定义，并且它们第一个参数总是 self，它代表调用该方法的结构体实例。

**Rust 使用 impl 来定义方法，例如以下代码：**
```rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    // new是Circle的关联函数，因为它的第一个参数不是self，且new并不是关键字
    // 这种方法往往用于初始化当前结构体的实例
    fn new(x: f64, y: f64, radius: f64) -> Circle {
        Circle {
            x: x,
            y: y,
            radius: radius,
        }
    }

    // Circle的方法，&self表示借用当前的Circle结构体
    fn area(&self) -> f64 {
        std::f64::consts::PI * (self.radius * self.radius)
    }
}
```
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```
### match
match 的匹配必须要穷举出所有可能，因此这里用 _ 来代表未列出的所有可能性

match 的每一个分支都必须是一个表达式，且所有分支的表达式最终返回值的类型必须相同

和其他语言差不多
### string4.rs
这个题需要知道.into()和to_owned()两个内容，这两个可能要查阅一下书籍才可以

今天做到40题截止。