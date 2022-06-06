---
title: SwordOffer把数字翻译成字符串
date: 2022-06-06 19:28:42
tags: 
- 刷题
---

## 题目

> 给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

## 草图

![](/images/sword-offer-46-draw.png)

<!-- more -->

示例:
```markdown
输入: 12258
输出: 5
解释: 12258有5种不同的翻译，分别是"bccfi", "bwfi", "bczi", "mcfi"和"mzi"
```

附上链接：[剑指Offer.46题(LeetCode地址)](https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)


## 解题

### 思路

动态规划:

假设第 `dp[n]` 表示前n个字符存在的翻译方法个数，则
1. 当字符 `{n-1}{n}` 组成的数字大于25时，存在 `dp[n] = dp[n-1]`；
2. 当字符 `{n-1}{n}` 组成的数字小于26时，存在 `dp[n] = dp[n-1] + dp[n-2]`；

### 代码
```rust
impl Solution {
    pub fn translate_num(num: i32) -> i32 {
        let snum = num.to_string();
        println!("{}", snum);
        let mut dp_trans = vec![0;snum.len()];

        dp_trans[0] = 1;
        for (ni, nv) in snum.as_bytes().iter().enumerate() {
            if ni == 0 {
                continue;
            }

            // 检测最后两个数字是否可以单独组成一种编码
            let last_pre: Vec<u8> = snum.as_bytes()[ni-1..=ni].to_vec();

            let last_two_num = String::from_utf8(last_pre).unwrap().parse::<i32>().unwrap();
            // println!("last_two_num: {}", last_two_num);

            if snum.as_bytes()[ni-1] != ('0' as u8) && last_two_num <= 25 {
                if ni == 1 {
                    dp_trans[ni] = dp_trans[ni-1] + 1;
                } else {
                    dp_trans[ni] = dp_trans[ni-1] + dp_trans[ni-2];
                }
            } else {
                dp_trans[ni] = dp_trans[ni-1];
            }
        }

        // println!("{:?}", dp_trans);

        return dp_trans[snum.len() - 1];
    }
}
```


## 总结

### 本次做题涉及到多种数据类型转换

1. i32转String
2. String转&[u8]
3. &[u8]转Vec<u8>
4. Vec<u8>转String
5. String转i32
