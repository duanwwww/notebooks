# The Rust Programming Language 自学笔记

<p align="right">by duanw</p>

---

# Chapter 1: Getting Started

## installation(skipped)

## Hello, world!

1. 源代码
```rust
// main.rs
fn main(){
    println!("Hello, world!");
}
```

2. 怎样运行这段代码
```powershell
$ rustc main.rs
$ ./main
```
3. 代码解析
    1. 函数调用、主函数  
    ```rust
    fn func_name(params){ func_body; }
    // main函数总会在程序开始时被调用
    ```
    2. `println()!`  
    使用`println`代表调用了函数`function`，而使用`println!`代表调用了宏`Rust macro`


## Hello, Cargo!

1. 使用Cargo创建项目
```powershell
$ cargo new hello_cargo
$ cd hello_carge # cargo将会创建与项目同名的文件夹
```
2. 使用cargo运行和检查项目
```powershell
$ cargo build # 编译并生成可执行文件
$ cargo run # 执行可执行文件，如果有更改发生，会首先重新build
$ cargo check # 编译但不生成可执行文件
$ cargo build --release # 生成运行速度更快的release版，但是编译时间更长
```

# Chapter 2: Programming a Guessing Game

## Part1: 检测输入

1. 源代码
```rust
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```
2. 代码解析
    1. 使用依赖 
    ```rust
    use std::io; // 相当于include
    ```
    2. 变量、赋值
    ```rust
    let const_var = 1; // immutable
    let mut guess = String::new(); // mutable
    ```
    3. 基本io语法
    ```rust
    io::stdin()
        .read_line(&mut guess) // 读取一行输入，赋值给guess, 返回值是个Result
        .expect("Failed to read line"); // 调用Result的expect方法处理Result
    ```

    4. 格式化字符串
    ```rust
    let x = 5;
    let y = 10;
    println!("x = {x} and y + 2 = {}", y + 2); 
    // 两种方式
    ```

## Part2: 生成随机数

1. cargo添加依赖
    1. 在`Cargo.toml`中添加：
    ```toml
    [dependencies]
    rand = "0.8.5"
    ```
    （或使用`cargo add rand`）  
    2. `cargo update`更新依赖
2. 生成随机数
```rust
use rand::Rng
let secret_number = rand::thread_rng().gen_range(1..=100);
println!("The secret number is: {secret_number}");
```
## Part3: 比较大小

1. 将猜测的数从`String`转化成`i32`类型
    ```rust
    // 允许同名变量（自动shadow掉前一个）
    let guess: u32 = guess.trim() // 去掉首尾空白字符
                        .parse() // 转化为对应字符，返回Result
                        .expect("Please type a number!"); // 处理Result
    ```
2. 进行比较
   ```rust
   use std::cmp::Ordering;
   match guess.cmp(&secret_number) { // 调用u32类型的cmp方法
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
   ```

## Part4: 重复猜测

1. 使用`loop`和`break`来控制循环
2. 进行错误处理
```rust
let guess: u32 = match guess.trim().parse() { // 使用match 处理 Result
    Ok(num) => num, // 返回 num
    Err(_) => continue, // 重新输入
};
``` 
3. 完整代码
```rust
use std::io; // 相当于include
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    println!("The secret number is {secret_number}");

    loop{
        println!("Please input your guess.");
        
        let mut guess = String::new(); // mutable

        io::stdin()
            .read_line(&mut guess) // 读取一行输入，赋值给guess, 返回值是个Result
            .expect("Failed to read line"); // 调用Result的expect方法处理Result

        // 允许同名变量（自动mask掉前一个）
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num, // 返回 num
            Err(_) => continue // 重新输入
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number){ // 调用u32类型的cmp方法
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

# Chapter3: Common Programming Concepts

## Variables and Mutability

1. 变量`variables`默认是不可变的，但是可以使用`mut`关键字来使其可变
2. 使用`let`命名同名变量进行shadow，注意shadow是有作用域的。
3. rust中也有常量`constants`，在定义时必须指定类型，值必须为常量表达式`constant expression`。形如
```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3; // 命名规范：全大写+下划线
```
## Data Types

1. rust是静态类型语言
2. 整数类型
    1. 包括：`i8`~`i128`, `u8`~`u128`, `isize`, `usize`
    2. 支持在数字中添加下划线来增加可读性，不会改变数字的值，例`1_000`
    3. 支持多种表示方法，如`0xff`（十六进制），`0o77`（八进制），`0b1111_0000`（二进制），`b'A'`（字节，这种方式只能对u8使用）
3. 浮点类型：`f32`, `f64`
4. 支持四则运算`+ - * /`和取余`%`
5. `bool`和`char`
6. 元组`tuple`
    1. 创建时指定每个元素的类型，元素的值不可变
    ```rust
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    ```
    2. 可以使用模式匹配一次性获得所有值
    ```rust
    let (x, y, z) = tup;
    ```
    3. 使用`.`进行下标进行访问
    ```rust
    let first = tup.0;
    ```
7. 列表`array`
   1. 固定长度
   2. 元素具有相同类型，指定类型的规则如下：
   ```rust
   let a: [i32; 5] = [1, 2, 3, 4, 5] // [类型; 数量]
   ```
   3. 含有相同值的列表可以快速创建：
   ```rust
   let a = [3;5];
   ```
   4. 使用`[]`进行下标访问
   5. `.rev()`方法的返回值是逆序的列表
## Functions

1. 定义方式
```rust
fn another_function(x: i32) -> i32 {
    println!("The value of x is: {x}");
    5
}
```
1. 函数体由一系列`statement`组成，可能由`expression`或`statement`结束。一个包含分号的语句成为一个`statement`，而不包含分号的语句则是`expression`。
2. `let`语句没有返回值
## Comments
使用`// 注释内容`
## Control Flow
1. `if`, `else`, `else if`
```rust
if number % 4 == 0 {
    println!("number is divisible by 4");
} else if number % 3 == 0 {
    println!("number is divisible by 3");
} else if number % 2 == 0 {
    println!("number is divisible by 2");
} else {
    println!("number is not divisible by 4, 3, or 2");
}
```

2. `let if`

```rust
let number = if condition { 5 } else { 6 }; // 两边的返回值类型必须一样
```
3. 带有返回值的`loop`
```rust
let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2;
    }
};
```

4. 给循环添加标签，并给`break`和`continue`指定作用的循环。
```rust
fn main() {
    let mut count = 0;
    'counting_up: loop { // 标签必须使用单引号
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break; // 默认退出最里层循环
            }
            if count == 2 {
                break 'counting_up;  // 退出指定的循环
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```
5. `while`
```rust
while index < 5 {
    println!("the value is: {}", a[index]);

    index += 1;
}
```
6. `for` item in collection
```rust
for element in array {
    println!("the value is: {element}");
}
```

# Chapter4:  Understanding Ownership

## What Is Ownership?

1. 当一个变量离开作用域的时候，其对应的内存空间被销毁。所有权指的是变量对内存空间的所有权。
2. 基础类型实现了`Copy`特性，赋值等价于`clone`，且赋值后的原变量依然可以使用
```rust
let x = 5;
let y = x;

println!("x = {x}, y = {y}");

```
3. 复杂类型（需要在堆上分配空间的，如String）的赋值相当于`move`，会转移所有权，且原变量不可用
```rust
let s1 = String::from("hello");
let s2 = s1.clone(); // 复制，保留原变量
let s3 = s1; // 移动，s1不可用

println!("s1 = {s1}, s2 = {s2}"); // 编译失败：s1不可用
```
4. 不使用引用`reference`的情况下，函数的参数会获得原变量所有权，导致原变量不再可用。函数的返回值也会传递所有权。
```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 was moved, so nothing
  // happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it

    let some_string = String::from("yours"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
}

// This function takes a String and returns one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```

## References and Borrowing

1. 在传递参数时使用引用`&`可以避免函数拿走变量的所有权（相当于对原变量的`borrow`）
```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{s1}' is {len}."); // s1仍然可用
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```
2. 使用可变引用`&mut`可以更改变量的值（如果原变量可变）
```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```
3. 每个变量同时只能存在一个可变引用或多个不可变引用（防止数据竞争）
4. 当函数想要返回在函数体内部的变量时不能使用引用，否则将会出现引用的生命周期比其变量的生命周期更长，从而使引用指向一块被释放的内存，这会引发编译错误。

## The Slice Type

1. 切片`slice`是对字符串，列表等的引用，保持了与原有变量的联系。
```rust
fn first_word(s: &String) -> &str { // 字符串切片是&str类型
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}

fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!
    // 错误原因在于，clear需要s的可变引用
    // 而word作为s的不可变引用（切片），生命周期直到下一行println!之后才会结束

    println!("the first word is: {word}");
}
```
2. 切片的下标是左开右闭区间，左侧的0可以省略，右侧的len可以省略
```rust
let slice = &s[1..3];
```
3. 函数参数使用`&str`对切片和`String`都有效

# Chapter5: Using Structs to Structure Related Data

## Defining and Instantiating Structs

1. 结构体`struct`的定义和实例化
```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn main() {
    let mut user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```
2. 使用函数进行构造时可以使用简写
```rust
fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username, // username: username的简写
        email, // email: email的简写
        sign_in_count: 1,
    }
}
```
3. 从其他实例构造
```rust
fn main() {
    // --snip--

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    }; // user1此后不再可用！
    // user1的其他field的所有权已经转移给user2了，导致user1这个整体不再可用
    // 但是user1.email仍然可用
}
```
4. 元组结构体`tuple structs`：用于给元组命名，并区分不同含义的元组
```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
    assert_neq!(black, origin); // 二者不相等
}
```
5. 单元结构体`unit-like structs`：用于实现`traits`
```rust
struct AlwaysEqual;
```

## An Example Program Using Structs

1. 用`debug trait` + `{:?}或{:#?}` 来打印结构体
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {rect1:#?}");
}
```
2. `dbg!`宏的使用
```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    dbg!(&rect1);
}
```
会打印出值和行号，得到的输出类似于
```powershell
[src/main.rs:10:16] 30 * scale = 60
[src/main.rs:14:5] &rect1 = Rectangle {
    width: 60,
    height: 50,
}
```

## Method Syntax

1. 定义结构体的方法`method`
```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}
```
2. 方法的第一个参数往往是self，如果是，则使用`instance.method()`调用，否则使用`struct_name::method()`调用
3. 多个方法可以在同一个`impl`块内定义，也可以在同一结构体的多个`impl`块内定义
4. `impl`块内的`Self`：指代该结构体
```rust
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}
let sq = Rectangle::square(3);
```

# Chapter6: Enums and Pattern Matching

## Defining an Enum

1. 基础定义和使用
```rust
enum IpAddrKind {
    V4,
    V6,
}
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```
2. rust的枚举有更强大的功能，每一个枚举类型可以是一个结构体
```rust
enum Message {
    Quit, // 单元结构体
    Move { x: i32, y: i32 }, // 普通结构体
    Write(String), // 元组结构体
    ChangeColor(i32, i32, i32), // 元组结构体
}
impl Message { // 对整个Message定义统一的方法
    fn call(&self) {
        // method body would be defined here
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```
3. `Option<T>`
```rust
enum Option<T> {
    None,
    Some(T),
}
let some_number = Some(5); // 类型自动推导
let some_char = Some('e'); // 类型自动推导

let absent_number: Option<i32> = None; // 必须手动指定类型
``` 

## The match Control Flow Construct

1. `match`表达式：穷举枚举的所有可能情况
```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

2. 使用变量来匹配其余值，使用匿名变量`_`来表示此值以后不会使用
3. `match`可以用于处理`Option`
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

## Concise Control Flow with if let

用 `if let` + `else` 来代替match进行处理
```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("The maximum is configured to be {max}");
}
```

# Chapter7: Managing Growing Projects with Packages, Crates, and Modules

笔者感此书这里写的不是特别明白，因此笔者这里尝试按照自己的逻辑进行整理，希望使之更易读。

***由于笔者的python没有系统学过面向对象，因此可能在一些理解上有误，请多多指教qwq。***

## 基本思想

我们学习这章的基本动机是出于以下两点考虑：
1. 随着项目规模增大，我们必须多文件开发，但是`struct`只在定义的`scope`内可用
2. 我们希望自己的代码有一种方式可以打包成像`Rand`那样的`package`，以便自己或其他人以后使用

## Modules

1. 模块`module`看起来就像一个域`scope`或命名空间那样，模块内可以是任何内容，例如函数、结构体、其他模块等等。
```rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // Order a breakfast in the summer with Rye toast
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Change our mind about what bread we'd like
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // The next line won't compile if we uncomment it; we're not allowed
    // to see or modify the seasonal fruit that comes with the meal
    // meal.seasonal_fruit = String::from("blueberries");
}
```
2. 使用`pub`关键字来决定模块外部可见的接口（就像上例所展示的那样）。其他函数、结构体可以通过一定的路径直接使用被标记为`pub`的内容。
3. 使用如下语法来声明使用一个`mod`
```rust
// /src/main.rs
pub mod vegetables; // 此pub是可选的，根据你的需求决定
```

## 文件结构
1. 在刚刚一节中我们谈到可以声明`mod`，并且，我们可以在一个文件中声明定义在其他文件中的`mod`，这才是我们的根本目的——实现多文件编程。但是我们必须告诉rust编译器怎样找到这个`mod`的定义位置。因此，我们有两种做法：
   1. 使用默认的路径。当rust发现你使用了`mod`关键字，根据你当前文件的路径，rust编译器会自动按照以下规则寻找可能的`mod`定义：
      1. 此处就是定义
      2. 假如你在`crate root`文件内（也就是`/src/main.rs`和`/src/lib.rs`，稍后会提到）声明使用`mod user`，那么rust会自动去`/src/user.rs`（新版路径）和`/src/user/mod.rs`（旧版路径）文件内去寻找
      3. 如果你不在`crate root`文件内，假设你当前的路径是`/src/net.rs`，那么rust会自动去`/src/net/user.rs`（新版路径）和`/src/net/user/mod.rs`（旧版路径）文件内去寻找
   2. 使用use关键字指定路径。可以使用相对路径和绝对路径两种。
      1. 相对路径：从本文件开始，使用`self`, `super`或当前模块的标识符。
      2. 绝对路径：从`crate root`开始，对于`crate`外部文件，以`crate`的名称开始；对于`crate`内部文件，以`crate`开始。
      3. 以下是一个样例
      ```rust
      // /src/lib.rs
      mod front_of_house {
           pub mod hosting {
               pub fn add_to_waitlist() {}
           }
       }

       // Absolute path
       use crate::front_of_house::hosting;

       // Relative path
       use front_of_house::hosting;

       pub fn eat_at_restaurant() {
           hosting::add_to_waitlist();
       }
      ```
2. 我们可以通过在`mod`声明和`use`前面加上`pub`关键字来将模块重新暴露给外部，以减少文件路径的长度，例如：
```rust
// /src/lib.rs
pub mod user;

pub use crate::front_of_house::hosting;
```
3. `pub use`可以让我们无需将一些`mod`设置为`pub`。在下例中，本来我们的外部代码可能需要使用`restaurant::front_of_house::hosting::add_to_waitlist()`，这要求我们将`front_of_house`设置成`pub`，但是现在可以直接使用`restaurant::hosting::add_to_waitlist()`
```rust
// /src/lib.rs
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```
4. 使用`{}`合并`use`的路径
```rust
use std::cmp::Ordering;
use std::io;

use std::{cmp::Ordering, io};
```
```rust
use std::io;
use std::io::Write;

use std::io::{self, Write};
```
## 基本概念补充：Packages and Crates

1. `crate`：一个`crate`是rust考虑的最小代码单元，分为 `binary crate` 和 `libiary crate` 两种。`binary crate`指的是可以编译成可执行文件的一段代码（包含`main`函数），而`library crate`则不包含main函数，而是用于定义可被其他文件使用的函数
2. `root crate`是rust编译器开始建立根模型的源文件
3. 包`package`是一个或多个`crate`，用于提供一系列函数。它包含一个`Cargo.toml`文件来介绍如何build这些`crate`。一个`package`可以包含任意数量的`binary crate`，但是最多只能包含一个`library crate`
4. cargo默认使用`src/main.rs`作为与包同名的`binary crate`的`root crate`，如果`src/lib.rs`存在，则会将其作为与包同名的`library crate`的`root crate`

# Chapter 8: Common Collections

## Storing Lists of Values with Vectors

1. 创建向量`vector`有两种方法
```rust
let v: Vec<i32> = Vec::new();

let v = vec![1, 2, 3]; // 使用vec! 宏，由于有初始元素，因此可以自动类型推导
```
2. 更新向量中元素的值：`push`, `pop`, `extend`, 使用下标更改
```rust
let mut vec = Vec::new();
vec.push(1);
vec.push(2);
// [1, 2]

assert_eq!(vec.len(), 2);
assert_eq!(vec[0], 1);

assert_eq!(vec.pop(), Some(2)); // 注意返回值是Option<T>
assert_eq!(vec.len(), 1);
// [1]

vec[0] = 7;
assert_eq!(vec[0], 7);
// [7]

vec.extend([1, 2, 3]);
// [7, 1, 2, 3]
assert_eq!(vec, [7, 1, 2, 3]); // 居然可以直接和列表判断equal
```
3. 获取向量中的元素：直接使用下标或使用`get`
```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2]; // 此方法得不到元素时会panic!

// v.push(0); 此时会编译不通过！因为third是一个不可变引用，push需要一个可变引用，二者不能同时存在

println!("The third element is {third}");

let third: Option<&i32> = v.get(2); // safer
match third {
    Some(third) => println!("The third element is {third}"),
    None => println!("There is no third element."),
}
```
4. 使用`for`来迭代访问元素
```rust
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;
}
```
5. 使用`enum`来让向量中包含不同类别的元素
```rust
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
```
6. 在向量离开作用域的时候，会自动释放其内存（因此针对其元素的引用此时也不能存在）

## Storing UTF-8 Encoded Text with Strings

1. 创建一个`String`的三种方式: `new`, `to_string`, `from`
```rust 
// new
let mut s = String::new(); 

// to_string
let data = "initial contents";

let s = data.to_string();

// the method also works on a literal directly:
let s = "initial contents".to_string();

// from
let s = String::from("initial contents");
```

2. 字符串拼接的三种方式：`push`, `push_str`, `+`
```rust
let mut s = String::from("lo");
s.push('l'); // lol

let mut s = String::from("foo");
s.push_str("bar"); // foobar

let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used
// Hello, world!
// 操作数类型必须是String + &str
```
3. `String`和`&str`不允许进行下标访问。原因：部分UTF8字符会占用两个byte，单个byte无意义（下标是按照byte计算的）
4. `String`和`&str`可以进行切片，但是下标仍然是按照byte计算，并且不允许在两字节的UTF8字符中间截断（会panic！）
5. 迭代访问的方法：`.chars()`和`.bytes()`

## Storing Keys with Associated Values in Hash Maps

1. 使用`new`创建，使用`insert`插入和更改，使用`get`获取   
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
let score = scores.get(&team_name) // 返回Option<&i32>
                    .copied() // 得到Option<i32>
                    .unwrap_or(0); // 得到i32
```
2. 迭代访问
```rust
for (key, value) in &scores
```
3. 如果一个键不存在，则为创建并赋值
```rust
let score = scores.entry(String::from("Yellow")).or_insert(50);
*score += 1; 
```

# Chapter 9: Error Handling

## Unrecoverable Errors with panic!

程序在`panic`之后默认会进入`unwinding`模式清理数据，如果希望程序在`panic`之后立刻终止（比如在`release`版本中），使用以下代码：
```toml
// cargo.toml 
[profile.release]
panic = 'abort'
```

## Recoverable Errors with Result
1. `Result`的定义
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

2. 比使用`match`进行处理更优雅的写法：`unwrap_or_else` + 闭包
```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {error:?}");
            })
        } else {
            panic!("Problem opening the file: {error:?}");
        }
    });
}
```

3. 使用`unwrap`在出现`Err`时自动`panic`
4. 使用`expect`在出现`Err`时自动`panic`并输出额外信息
```rust
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
}
```
输出类似：
```powershell
thread 'main' panicked at src/main.rs:5:10:
hello.txt should be included in this project: Os { code: 2, kind: NotFound, message: "No such file or directory" }
```
5. 传递`Result`来处理错误
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");

    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut username = String::new();

    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    } // 注意这里没有分号，因此Ok(username)和Err(e)将成为返回值
}
```
6. 使用`?`运算符来改写上述代码
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username_file = File::open("hello.txt")?;
    let mut username = String::new();
    username_file.read_to_string(&mut username)?;
    Ok(username)
}
```
7. `?`运算符的额外功能：`?`运算符会对得到的错误类型自动调用`from`方法（如果实现了`From trait`），从而将得到的错误类型自动转化为函数的返回值所规定的错误类型。例如，我们将函数的返回值转化为`OurError`类型，并对其实现`From<io::Error>`特型，那么我们无需添加任何代码就可以让`?`运算符返回正确的错误类型。

8. 继续简化代码
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username = String::new();

    File::open("hello.txt")?.read_to_string(&mut username)?;

    Ok(username)
}
```
```rust
use std::fs;
use std::io;

fn read_username_from_file() -> Result<String, io::Error> {
    fs::read_to_string("hello.txt")
}
```
9. 使用`Box<dyn Error>`作为`main`函数的返回值：接受任何错误类型被`?`提早`return`，甚至可以是不同种类的。
10. `main`函数允许任何实现了`std::process::Termination`特型的类型作为返回值（需要包含一个`report`函数，返回`ExitCode`）

## To panic! or Not to panic!

1. 应该默认使用Result 
2. 在示例、原型代码和测试中，使用`panic!`是正确的（包括`unwrap`和`expect`）。在前两者中可以简化代码，在第三者中是为了告知测试人员测试不通过。
3. 当拥有比编译器更多的信息，确信不会出现问题时，可以使用`unwrap`。更推荐使用`expect`来说明为什么认为不会出现问题。
4. 使用他人的代码时可以使用`panic`，因为无法进行错误处理。
5. 当认为代码进入`bad state`，继续运行可能有危险时应该`panic`
6. 当失败（一般意义上）可能发生时应该返回`Result`，来要求对方处理这种可能发生的情况
7. 自定义类型`type`来进行错误处理
```rust
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {value}.");
        }

        Guess { value }
    }

    pub fn value(&self) -> i32 {
        self.value
    }
}
```

# Chapter 10: Generic Types, Traits, and Lifetimes

## Generic Data Types

1. 综述：这是一个类似模板类的东西。写法也和C++的模板类基本相同，但是更为简洁（可以自动推导数据类型）。它设计的初衷是为了让代码能够在不同的数据类型上复用，减少重复代码。
2. 在函数上定义：
```rust
fn largest<T>(list: &[T]) -> &T {
    // --- something ---
}
```
3. 在结构体及其方法上定义：
```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

impl Point<f32> { // f32特有
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

fn main() {
    let integer = Point { x: 5, y: 10 };
    let float = Point { x: 1.0, y: 4.0 };
}
```
方法也可以定义自己的模板，例如：
```rust
struct Point<X1, Y1> {
    x: X1,
    y: Y1,
}

impl<X1, Y1> Point<X1, Y1> {
    fn mixup<X2, Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c' };

    let p3 = p1.mixup(p2); // 自动类型推导太帅啦

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```
4. 在枚举上定义：
```rust
enum Option<T> {
    Some(T),
    None,
}
```

## Traits: Defining Shared Behavior

1. 综述：特型`trait`形如其他语言中的接口`interface`，用于规定不同类之间一些共享的行为
2. 定义一个特型，然后指定其内部的方法（接口），可以为方法提供默认实现也可以不提供。默认实现可以依赖于特型内的其他方法，无论是否有默认实现。
```rust
pub trait Summary {
    fn summarize_author(&self) -> String; // 无默认实现

    fn summarize(&self) -> String { // 提供默认实现，并且依赖于其他接口
        format!("(Read more from {}...)", self.summarize_author())
    }
}
```
3. 使用`impl`和`for`为类实现特型。不同类的实现方式可以不同。
```rust
pub trait Summary {
    fn summarize(&self) -> String;
}

pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```
4. 为类实现特型的基本原则：类和特型至少有一个是自己定义的。
5. 特型作为参数的两种写法
```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}

pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}

pub fn notify(item1: &impl Summary, item2: &impl Summary) {} // 两个类型可以不同

pub fn notify<T: Summary>(item1: &T, item2: &T) {} // 如果要约束类型相同，必须采用这种写法
```
6. 使用多个约束时，用`+`连接
```rust
pub fn notify(item: &(impl Summary + Display)) {}
pub fn notify<T: Summary + Display>(item: &T) {}
```
7. 使用`where`来提升代码可读性
```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {}

fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{}
```
8. 使用特型作为返回值，但需要注意的是返回值的类型仍然必须是一致的，而不能包含多个实现该特型的不同类型。
```rust
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    }
}
```
9. 特型可以让我们有条件地为模板类实现方法
```rust
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```

## Validating References with Lifetimes

1. 综述：生命周期是针对引用而言的，任何引用都含有生命周期。rust含有非常简陋的自动推导生命周期的功能，如果代码较为复杂无法自动推导，则需要手动实现。
2. 生命周期的记号：`'` + 名称，要求全部小写且较简短。
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
3. 对上述函数的生命周期的解释是：output的生命周期和input中参数的生命周期较短者相同。
4. 函数和方法的生命周期分为`input`（施加于函数或方法的参数上）和`output`（施加于返回值上）两种。rust会按照三条基本规则尝试推导生命周期，如果失败，则需要手动标注。
   1. 为所有`input`分配不同的生命周期`fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`
   2. 如果只有一个`input`，其生命周期会应用于所有的`output`
   3. 如果有多个`input`，但是其中包含`&self`或`&mut self`，那么其生命周期会被应用于所有`output`
5. 结构体也可以标注生命周期，这样就可以在其域内使用引用了。所有使用引用的域都必须正确标注生命周期。
```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    let i = ImportantExcerpt {
        part: first_sentence,
    };
    // 直到i失效，part的引用都一直有效，因此是合法的
}
```
6. `'static`代表此引用的生命周期一直持续到整个程序结束，例如所有`&str`实际上是`&'static str`。但是报错中提示使用`'static`常常代表程序在其他部分有问题，而不应该简单地这样修改。
7. 模板类、特型、生命周期的联合使用
```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display,
{
    println!("Announcement! {ann}");
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```