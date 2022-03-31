---
title: 实现一个Rust Unix Domain程序
date: 2021-09-03 20:31:43
tags:
- Rust
---

## 背景
最近在用php写脚本时，对`Unix Domain`这种`IPC`进行了一些学习和使用。

我之后是打算利用Rust来实现一个存储服务，所以这次会对针对Rust的`Unix Domain`使用进行了解，同时也会使用Rust来解一个二进制数据包。

<!-- more -->

## Rust部分

### 1. 使用UnixListener

```rust
// 此时会自动创建一个helo.sock文件，如果已存在则会报错
let listen = UnixListener::bind("./helo.sock").unwrap();
```

### 2. 使用UnixStream

```rust
// stream_pack 是一个 Result< Ok(UnixStream), Err(String) > 结构
for stream_pack in listen.incoming() {
  match stream_pack {
    // 因为需要使用stream来写入和读取数据，所以用mut声明
    Ok(mut stream) => {
      // -- 请求处理
    }
    Err(err) => {
      // -- 错误处理
    }
  }
}
```

### 3. 使用包 `ctrlc` 控制 `ctrl + c` 信号

>  基于每次 `UnixListener::bind()` 时都不能存在 `./helo.sock` 文件

```rust
ctrlc::set_handler(move || {
  // 每次服务的时候，删除./helo.sock文件
  fs::remove_file("./helo.sock").unwrap();
  process::exit(0);
}).unwrap();
```

### 4. 读取请求流中的数据

```rust
// ... 在请求处理中，使用线程来分发请求并处理
Ok(stream) => {
  thread::spawn(move || {
    handle_client(stream);
  });
}
// ...


// 请求处理的具体实现
fn handle_client(mut stream: UnixStream) {
  let mut rec_msg = String::new();
  stream.read_to_string(&mut rec_msg).unwrap();
  
  println!("{}", rec_msg);
}
```

### 5. 请求流中的结构化数据

> 网络请求中的数据往往是二进制数据

```rust
// 假设一个场景，有一个数据结构如下：
//		———— 即有一个二进制串，开始的4个字节（一个整型）表示接下来消息的长度（单位：字节）
#[derive(Debug)]
struct Pmsg {
  len: i32,
  msg: Vec<u8>,
  msg_str: String,
}

impl Pmsg {
  	// -- 构造 Pmsg 方法
    pub fn new(len: i32, msg: &[u8]) -> Self {
        let mut imsg: Vec<u8> = vec![0;len as usize];
        imsg.clone_from_slice(msg);
        let smsg = std::str::from_utf8(msg).unwrap().to_string();
        Pmsg {
            len,
            msg: imsg,
            msg_str: smsg,
        }
    }

  
  	// -- 构造 Pmsg.len 的方法
    pub fn build_len(rec_d: &[u8]) -> i32 {
        let mut hnum: Vec<u8>  = vec![0, 0, 0, 0];

        hnum.clone_from_slice(rec_d);

        // -- 小端序读取
        hnum.reverse();

        let hlen = unsafe { *(hnum.as_ptr() as *const i32) };

        return hlen;
    }
}

fn handle_client(mut stream: UnixStream) {
  // 我们接收一个二进制串
  let mut rec_msg = Vec::new();
  stream.read_to_end(&mut rec_msg).unwrap();
  
  // 二进制串的前4个字节是Pmsg.len，紧接着Pmsg.len个字节就是具体消息
  let plen = Pmsg::build_len(&rec_msg[..=3]);
  let pmsg_result = Pmsg::new(plen, &rec_msg[4..]);
  
  println!("{:?}", pmsg_result);
  // 输出结构如下：
  // Pmsg { len: 8, msg: [104, 101, 108, 108, 111, 32, 102, 113], msg_str: "hello fq" }
}
```

## PHP部分

### 实现一个简单unix domain client

```php
// 利用php的socket拓展实现
$sock = socket_create(AF_UNIX, SOCK_STREAM, 0);
socket_connect($sock, './helo.sock');

$msg = 'hello fq';
$premsg = strlen($msg);

// 打包消息为二进制串
$pmsg = pack('N1a*', $premsg, $msg);
socket_write($sock, $pmsg);
```
