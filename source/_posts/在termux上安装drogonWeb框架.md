---
title: 在termux上安装drogonWeb框架
date: 2026-07-18 23:01:20
tags:
  - 'C++'
---

# termux上安装Drogon框架

> **作者**: oldmnj

Drogon是由C++开发的开源Web框架，项目开源在 [drogon](https://github.com/drogonframework/drogon)。

termux同样是开源在GitHub，是一个面向安卓用户的终端模拟器，具有较为完整的基于apt的包管理器pkg，所以也有很多人整活。

在termux上安装drogon框架有一下几种方式：

---

## 方法一：通过xmake安装

termux的包管理器pkg有一个包xmake，xmake是一个开源的构建工具，xmake的库里有drogon，可以通过xmake的xrepo安装drogon框架（可能需要构建，据说有时可能会少几个可选依赖）。

### 安装xmake：

在termux的命令行执行：

```bash
pkg update
pkg upgrade
pkg install xmake
```

安装完成后，可以进行下一步：

执行：

```bash
xrepo install drogon
```

即可完成安装，中间可能会有报错之类的，可以去官方的issues查找是否有解决方法。

---

## 第二种方法：直接通过源码构建

直接通过源码构建drogon，此方法需设备的内存足，否则可能在构建过程中因内存不足而导致构建失败。

首先，我们需要安装项目所需的依赖。

从官方文档可以找到，项目有以下几个必须的依赖：

- jsoncpp
- libuuid
- zlib

还有下面这几个可选依赖：

- boost
- openssl
- c-ares
- libbrotli
- pgsql/mariadb/sqlite
- hiredis
- gtest
- yaml-cpp

此为官网文档对依赖的描述：
![依赖描述](https://tgpan.djlns.indevs.in/file/BQACAgUAAyEGAAMBAq9yOQADJWpW_XKwo9zG3PJs4EQdG7-wo3hRAAJyHQACIo64VrAQe3k6T56BPQQ.png)


### 必须依赖的安装：

```bash
pkg install libuuid zlib jsoncpp jsoncpp-static
```

### 可选依赖的安装：

**boost：**

```bash
pkg install boost
```

**brotli：**

```bash
pkg install brotli
```

**openssl：**

```bash
pkg install openssl
```

**c-ares：**

```bash
pkg install c-ares
```

**postgre, mariadb, sqlite3：**

```bash
pkg install libsqlite mariadb postgresql sqlite mariadb-static
```

此处需注意，若要用mariadb，你可能会遇到报错：

```
[ 67%] Generating test_main.h, test...
CANNOT LINK EXECUTABLE "./_drogon_c...": library "libmariadb.so" not found
make[2]: *** [drogon_ctl/CMakeFiles/...] Error 1
make[1]: *** [CMakeFiles/Makefile2:...] Error 2
make[1]: *** Waiting for unfinished jobs....
[ 68%] Linking CXX executable bin/c...
[ 68%] Built target cors
[ 68%] Linking CXX executable bin/a...
[ 68%] Built target async_stream
make: *** [Makefile:156: all] Error 2
```

遇到此报错，原因是找不到mariadb的.a静态库文件，可以通过软链接的方式来解决：

```bash
ln -sf $PREFIX/lib/aarch64-linux-android/libmariadb.so $PREFIX/lib/libmariadb.so
ln -sf $PREFIX/lib/aarch64-linux-android/libmariadbclient.a $PREFIX/lib/libmariadbclient.a
ln -sf $PREFIX/lib/aarch64-linux-android/libmariadb.a $PREFIX/lib/libmariadb.a
```

**hiredis：**

```bash
pkg install libhiredis
```

**gtest：**

```bash
pkg install googletest
```

**yaml-cpp：**

```bash
pkg install libyaml-cpp
```

所有依赖安装完成后，需安装一些构建所需的包：

```bash
pkg install clang make cmake git
```

然后就可以开始构建drogon了。

### 1. clone drogon仓库

```bash
git clone https://github.com/drogonframework/drogon
cd drogon
git submodule update --init
```

### 2. 编译&安装

```bash
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=$PREFIX
make -j$(nproc)
make install
```

现在drogon已经编译构建成功，已安装到 `$PREFIX` 目录下。

在命令行敲下：

```bash
drogon_ctl version
```

用以验证是否安装成功。

可以打开drogon自带的helloworld项目来测试：

```bash
helloworld
```

而后就可以在控制台看到提示，你便可以访问程序提示的url。

