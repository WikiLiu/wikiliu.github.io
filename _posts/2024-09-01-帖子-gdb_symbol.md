---
title: "gdb symbol"
date: 2024-07-2T15:47:30-04:00
classes: wide
categories:
  - 帖子
tags:
  - GDB
---
gdb调试用户app时需要符号信息，安装的app或者so一般都是stripped且没有符号信息，gdb <binary> 会出现找不到符号的情况。

## [debuginfo](https://ubuntu.com/server/docs/about-debuginfod)

1.添加环境变量到`~/.bashrc`：

```sh
    export DEBUGINFOD_URLS="https://debuginfod.ubuntu.com"
```

2.打开debufinfo，新版的gdb打开时会询问是否打开debuginfod，也可持久化添加到`~/.gdbinit`：

```sh
set debuginfod enabled on
```

> 优点：操作简单，库很全

> 缺点：需要连接外网，加载全部link的库符号导致很慢

## apt安装符号包

1.搜索库所在的deb包，例如使用apt-file：

```sh
apt-file search <name>.so
```

2.查找apt 源中是否有符号包(`apt-cache search <name>` 后以 `<name>-dbgsym`结尾的包)，然后安装

> 优点：精准安装，也比较快，内网可以apt source

> 缺点：可能会出现没有符号包的情况

> tips: `show debug-file-directory`检查一下是否匹配符号包路径，如果sudo安装符号包路径可能是`/usr/lib/debug`. 不匹配可ln链接下

## 手动编译

如果apt源中找不到符号包，就只能使用dpkg-buildpackage甚至重编带符号的库了

1. dpkg-buildpackage [参见kernel:串口双机调试](https://wiki.glenfly.com/doku.php?id=kernel:%E4%B8%B2%E5%8F%A3%E5%8F%8C%E6%9C%BA%E8%B0%83%E8%AF%95kernel) 第2节方式一

2. 下载源码重编，改编译参数`-g -o0`，替换so
