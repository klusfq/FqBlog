---
title: Rust学习笔记
categories: []
date: 2020-04-04 23:17:12
tags: 
- Rust
---

### 标准宏

#### `println!()`用法

```rust
println!("{}", 1);      // 打印Display
println!("{:o}", 9);    // 打印八进制
println!("{:x}", 255);  // 小写的十六进制
println!("{:X}", 255);  // 大写十六进制
println!("{:p}", &0);   // 打印指针
println!("{:b}", 15);   // 打印二进制
println!("{:e}", 10000f32); // 小写科学计数法
println!("{:E}", 10000f32); // 大写科学计数法

println!("{:?}", "test");   // 打印Debug
println!("{:#?}", ("test1", "test2")    // 格式化的Debug打印

```
<!-- more -->

### 常用库

#### `std::thread`库
```rust
use std::thread;

fn main() {
    let mut children = vec![];
    
    for ci in 0..10 {
        children.push(thread::spawn(move || {
            println!("this is thread number is {}", ci);
        }));
    }
    
    for child in children {
        let _ = child.join();
    }
}
```
- 提供了两个方法: `thread::spawn(Closure cf)` 和 `thread.join()`


#### `std::sync::mpsc`库
> Rust 针对线程之间的通信提供了异步的通道（channel）。通道允许两个端点之间信息的单向流动：Sender（发送端） 和 Receiver（接收端）。

```rust
use std::thread;
use std::sync::mpsc::channel;

fn main() {
    for mi in 0..10 {
        let (tx, rx) = channel();
    
        thread::spawn(move || {
            tx.send(i).unwrap();
        });
    }
    
    for _ in 0..10 {
        let j = rx.recv().unwarp();
        assert!(0 <= j && j < 10);
    }
}
```

#### `std::path`库
```rust
use std::path::Path;

fn main() {
    let vpath = Path::new(".");
    let vpath = vpath.canonicalize().unwrap();
    println!("abs: {:?} {:?}", vpath.is_absolute(). vpath.as_path());
    println!("{:?}", vpath.parent().unwrap());
}
```

- `Path::new()`从 `&' static str` 创建一个 `Path`实例
- `Path`提供了`.is_absolute()`, `.as_path()` 和 `.parent()`等方法

### 异常详解

#### Result 和 Option
> 对 `io::stdin().read_line(&mut guess).ok().expect("Failed to read line");` 代码段的理解

```rust

// Result定义
pub enum Result <T, E> {
    Ok(T),
    Err(E),
}

// Result.ok()方法的定义
pub fn ok(self) -> Option<T> {
    match self {
        Ok(x)  => Some(x),
        Err(_) => None,
    }
}


// Option定义
pub enum Option <T> {
    None,
    Some(T),
}


// Option.expect()方法的定义
pub fn expect(self, msg: &str) -> T {
    match self {
        Some(val) => val,
        None => panic!("{}", msg),
    }
}
```
> 对 `io::stdin().read_line(&mut guess).unwrap();` 代码段的理解
```rust
// Result.unwrap() 方法的定义
pub fn unwrap(self) -> T {
    match self {
        Ok(t) => T,
        Err(e) => panic!("called `Result::unwrap()` on an `Err` value: {:?}", e)
    }
}
```
