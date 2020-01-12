---
title: "AFL Quick Start"
date: 2020-01-12T09:51:04+08:00
draft: true
tags: 
    - Fuzzing
    - AFL
---


它通过对源码进行重新编译时进行插桩的方式自动产生测试用例来探索二进制程序内部新的执行路径。

<!--more-->

与其他基于插桩技术的fuzzers相比，afl-fuzz具有较低的性能消耗，有各种高效的fuzzing策略和技巧最小化技巧，不需要先行复杂的配置，能无缝处理复杂的现实中的程序。

当然AFL也支持直接对没有源码的二进制程序进行测试，但需要QEMU的支持。


## 安装afl-fuzz

从官网下载源码压缩包，解压。然后编译安装：

```bash
make
sudo make install
```

安装完成后一般需要配置一下，将coredumps输出为文件，而不是把崩溃信息发送到特定的处理程序：

```bash
sudo su
echo core > /proc/sys/kernel/core_pattern
```

官方文档：http://lcamtuf.coredump.cx/afl/README.txt

## 测试示例

对于有源码的测试程序，可以使用afl来代替gcc或clang来编译。用afl的插桩可以优化afl-fuzz的性能，加速fuzz。

### 测试程序

下面是一个崩溃示例程序：

```C
#include <stdio.h>
#include <signal.h>

int main(int argc, char *argv[])
{
 char buf[233] = {0};
 FILE *input = NULL;
 input = fopen(argv[1], "r");
 if(input != 0)
 {
  fscanf(input ,"%s", &buf);
  printf("buf is %s\n", buf);
  func(buf);
  fclose(input);
 }
 else
  printf("error!");

 return 0;
}

int func(char *data)
{
 if(data[0] == 'A')
  raise(SIGSEGV);

 else
  printf("ok\n");

 return 0;
}
```

当输入文件的首字母为A时程序崩溃。

用afl-gcc编译程序，插桩：

```bash
afl-gcc test.c -o test
```

### 准备

新建输入、输出文件夹：

```bash
mkdir fuzz_in fuzz_out
```

准备初始化 测试用例, 将case内容写成aaa， fuzzer 会根据测试用例进行突变:

```bash
echo aaa > fuzz_in/case
```

### 开始fuzzing

`fuzz_in` 是自带的输入目录，`fuzz_out` 是指定的输出目录

```bash
afl-fuzz -i fuzz_in -o fuzz_out ./test @@
```

这里使用`@@`标志，从文件中读取输入。在实际执行的时候会把`@@`替换成`fuzz_in`下的测试样本

### fuzzing 结果

![](https://pic.lushx.cn/afl.png)

```bash
fuzz_out/crashes$ cat id\:000000\,sig\:11\,src\:000000\,op\:flip1\,pos\:0
Aaa
```

---
参考资料：

1. [linux下fuzz初试](https://xz.aliyun.com/t/2714)
2. [Finding bugs using AFL](https://stfpeak.github.io/2017/06/11/Finding-bugs-using-AFL/)
