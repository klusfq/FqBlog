---
title: Rust学习笔记_2
categories: []
date: 2022-06-06 19:18:27
tags: 
- Rust
---

### 类型转换

#### &[8]转Vec<u8>
```rust
// 三种将&[u8]转换为Vec<u8>的方法
let tmp_slice = &[1, 2, 3];

let tmp_vec: Vec<u8> = tmp_slice.iter().map(|p| p.to_owned()).collect();
let tmp_vec: Vec<u8> = tmp_slice.iter().cloned().collect();
let tmp_vec: Vec<u8> = tmp_slice.to_vec();
```


#### Vec<u8>转String
```rust
let tmp_vec = vec![240, 159, 146, 150];

let sparkle_heart = String::from_utf8(tmp_vec).unwrap();
```
