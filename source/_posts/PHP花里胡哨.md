---
title: PHP花里胡哨
date: 2020-07-06 16:40:32
tags: 
- PHP
---

### 类方法/属性动态调用

```PHP
class A
{
    public $two_closure = NULL;

    public function __construct()
    {
        $this->two_closure = function ($name) {
            echo "this is closure, and the name is {$name}" . PHP_EOL;
        };
    }

    public function one_step()
    {
        echo "this is one" . PHP_EOL;
    }
}

// 动态调用常规方法
$num = 'one';
$methodName = "{$num}_step";
(new A())->$methodName();   // this is one


// 动态调用属性（当属性是闭包时）
$num = 'two';
$propName = "{$num}_closure";
((new A())->$propName)('haha'); // this is closure, and the name is haha
```