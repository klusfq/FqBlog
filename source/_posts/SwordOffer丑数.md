---
title: SwordOffer丑数.md
date: 2022-05-15 17:37:00
tags: 
- 算法
---

## 题目

[剑指offer.49题(LeetCode地址)](https://leetcode.cn/problems/chou-shu-lcof/)

> 我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。

示例:
```markdown
输入：n = 10
输出：12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
```

## 解题

### 思路

第一种，动态规划，三指针；

第二种，优先队列；

我这里使用第二种解题。

### 代码
```rust
use std::collections::{BinaryHeap, HashMap};
use std::cmp::Reverse;

impl Solution {
    pub fn nth_ugly_number(n: i32) -> i64 {
        let mut idx: usize = 0;
        let mut out: i64 = 0;
        let mut bigHeap = BinaryHeap::new();
        let mut repeat: HashMap<i64, bool> = HashMap::new();

        bigHeap.push(Reverse::<i64>(1));
        loop {
            if idx >= (n as usize) || bigHeap.len() == 0 {
                break;
            }

            if let Some(Reverse::<i64>(mx)) = bigHeap.pop() {
                // if idx >= 1490 {
                //     println!("> {}", mx);
                // }

                if (mx * 2) > 0 && repeat.get(&(mx * 2)).is_none() {
                    repeat.insert(mx * 2, true);
                    bigHeap.push(Reverse::<i64>(mx * 2));
                }
                if (mx * 3) > 0 && repeat.get(&(mx * 3)).is_none() {
                    repeat.insert(mx * 3, true);
                    bigHeap.push(Reverse::<i64>(mx * 3));
                }
                if (mx * 5) > 0 && repeat.get(&(mx * 5)).is_none() {
                    repeat.insert(mx * 5, true);
                    bigHeap.push(Reverse::<i64>(mx * 5));
                }

                idx += 1;
                out = mx;
            }
        }
        return out;
    }
}
```



## 总结


### Rust优先队列

```rust

// ** Rust默认只有大顶堆
let mut b_heap = std::collections::BinaryHeap::new();

// ** 小顶堆实现：使用Reverse结构体包住，作为堆节点
BinaryHeap<Reverse(T)>;

// ** 常用操作

// 插入
b_heap.push(T);
// 弹出
b_heap.pop() -> Option<T>;
// 获取根节点值
b_heap.peek() -> Option<&T>;
// 获取堆中元素数
b_heap.len() -> 
```