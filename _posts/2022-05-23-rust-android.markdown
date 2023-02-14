---
layout: post
title:  "手把手-如何在Android系统上运行Rust Code"
date:   2022-02-08 01:15:00
published: false
categories: Thinking
tags:
    - Rust
    - 手把手
---

Rust作为一门年轻的系统开发语言，近些年来发展的势头非常强劲，最近团队也都在研究和学习Rust，并使用Rust作为未来团队的核心开发语言。最近一周要将一些Rust library放到Android Application上进行调用，于是看了一些文章。这篇文章是一个手把手的教程，展示下如何使用在Android 系统上运行Rust Code。


## Build Rust Code for Android

首先我们使用Cargo创建一个最小的rust library. 如下图所示。

```
cargo new rust-android --library
```

我们在 `lib.rs`中定义一个最简单的hello的方法。可以看到我们不仅定义了一个hello方法，并且同时写了一条测试。下来我们就想办法将这个简单的library编译成为Android Application可以调用的library。

```Rust
pub fn hello() -> String {
    return "world".to_string();
}

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn it_works() {
        let test_string  = hello();
        assert_eq!(test_string, "world".to_string());
    }
}
```
可以让我们的Rust Code可以编译到Android上，我们需要添加相关Android平台的toolchain。可以运行下边的命令来进行添加。

```
rustup target install armv7-linux-androideabi

// armv7 应该大部分Android设备的架构，但是如果你的安卓设备不是的话可以参考安装下列的toolchain。

rustup target install arm-linux-androideabi
rustup target install aarch64-linux-android
rustup target install i686-linux-android
rustup target install x86_64-linux-android
```


## Introduce Rust library on Android Project


## Debug and Testing


## Reference.
