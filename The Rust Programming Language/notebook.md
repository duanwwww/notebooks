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
    使用`println`代表调用了`function`（函数），而使用`println!`代表调用了`Rust macro`（宏）


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
    2. 常量、变量、赋值
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
1. 变量默认是不可变的，但是可以使用`mut`关键字来使其可变
2. 使用`let`命名同名变量进行shadow，注意shadow是有作用域的。
3. rust中也有常量，在定义时必须指定类型，值必须为常量表达式。形如
```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3; // 命名规范：全大写+下划线
```
## Data Types
1. rust是静态类型语言
2. 整数类型
    1. 包括：i8~i128, u8~u128, isize, usize
    2. 支持在数字中添加下划线来增加可读性，不会改变数字的值，例`1_000`
    3. 支持多种表示方法，如`0xff`（十六进制），`0o77`（八进制），`0b1111_0000`（二进制），`b'A'`（字节，这种方式只能对u8使用）
3. 浮点类型：`f32`, `f64`
4. 支持四则运算`+ - * /`和取余`%`
5. `bool`和`char`
6. 元组
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
7. 列表
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
1. 函数体由一系列statement组成，可能由expression或statement结束。一个包含分号的语句成为一个statement，而不包含分号的语句则是expression。
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