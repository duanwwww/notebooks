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
    // 允许同名变量（自动mask掉前一个）
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