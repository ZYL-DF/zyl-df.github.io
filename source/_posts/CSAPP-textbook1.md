---
title: CSAPP 第一、二章笔记
date: 2022.10.20
updated: 2022.10.20
tags: 
- 学习
- CSAPP
categories: 
- 笔记
keywords:  
- CSAPP
- 笔记
description: You are (not) learning
toc: true
toc_number: false
katex: true
top_img: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210200028800.png
cover: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210200017696.jpg
---

# 第一节 Bits, Bytes and Integers

逻辑移位（SHR） 补全填0

算术移位（SAR）按最高位填数字

无符号数

![image-20221019205222808](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192052067.png)

## 有符号数与无符号数的转换

在进行有符号数与无符号数的运算时（特别是比较运算），会将有符号数转换为无符号数再进行运算。

![image-20221019211118711](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192111775.png)

![image-20221019211152492](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192111550.png)

![image-20221019211232099](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192112255.png)

---

## 无符号数加法$+^u_wx$

![image-20221019211425348](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192114417.png)



## 无符号数求反$-^u_wx$

![image-20221019212302086](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192123230.png)



## 补码加法$+^t_wx$

![image-20221019212504690](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192125774.png)



## 补码的非$-^t_wx$

![image-20221019212606152](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192126251.png)

---

## 浮点数

### IEEE标准设定

$$
V=(-1)^s×M×2^E
$$



- **s**:符号（sign) s 决定这数是负数还是正数，而对于数值 0 的符号位解释作为特殊情况处理。

- **M**:尾数(significand) M 是一个二进制小数，它的范围是$1\sim 2-\epsilon$或者是 $0\sim1-\epsilon$

- **E**:阶码（exponent) E的作用是对浮点数加权，这个权重是2的E次幂(可能是负数)。

  

  将浮点数的位表示划分为三个字段，分别对这些值进行编码：

- 一个单独的符号位s，直接编码s

- **k**位的阶位 **exp**=$e_{k-1}e_{k-2}...e_1e_0$，编码E

- **n**位的小数字段 **frac**=$f_{n-1}f_{n-2}...f_1f_0$,编码M

| 浮点类型 |  s   |     exp      |    frac     |
| :------: | :--: | :----------: | :---------: |
|  单精度  |  31  | 30~23 ：k=8  | 22~0 ：n=23 |
|  双精度  |  63  | 62~52 ：k=11 | 51~0 ：n=52 |

![image-20221019232814573](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210192328721.png)

### 分类

对单精度的分类，主要在于判断**exp**的条件

$Bias=2^k-1$  , 是单精度是 **127** , 双精度是 **1023** 的偏置值。由此产生指数的取值范围，也就是exp，对于单精度是 -126 ~ +127, 而对于双精度是 -1022~ +1023



|   种类   |       表示方式       |      E       |    M    |
| :------: | :------------------: | :----------: | :-----: |
|  规格化  | exp不全为0，不全为1  | **E=e-Bias** | **1+f** |
| 非规格化 |       exp全为0       | **E=1-Bias** |  **f**  |
|  无穷大  | exp全为1，frac全为0  |              |         |
|   NaN    | exp全为1，frac有1有0 |              |         |



## 强制类型转换



![image-20221020000627015](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210200006336.png)

# 第六节 机器级控制

## gcc命令执行过程

```
gcc 1.cpp -Og -o 1
```

预处理器（展开#include，#define）	->	编译器（产生汇编代码，.s)	

->	汇编器	(转为二进制代码，.o)->	链接器（链接库文件，生成可执行文件）

---

## 汇编代码数据格式



### 数据大小

| C语言数据类型 | Intel数据类型 | 汇编代码后缀 | 大小（字节） |
| :-----------: | :-----------: | :----------: | :----------: |
|     char      |     字节      |    **b**     |    **1**     |
|     short     |      字       |    **w**     |    **2**     |
|      int      |     双字      |    **l**     |    **4**     |
|     long      |     四字      |    **q**     |    **8**     |
|     char*     |     四字      |    **q**     |    **8**     |
|     float     |    单精度     |      s       |      4       |
|    double     |    双精度     |      l       |      8       |

*在x86-64机器上，指针长为8个字节*



### 寄存器

一个 X86-64 的中央处理单元（CPU)包含一组 16 个存储 64 位值的通用目的寄存器。
这些寄存器用来存储整数数据和指针。

![image-20221015221938443](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210152219834.png)

指令可以对这 16 个寄存器的低位字节中存放的不同大小的数据进行操作。字节级操作可以访问最低的字节，16 位操作可以访问最低的 2 个字节，32 位操作可以访问最低的 4 个字节，而 64 位操作可以访问整个寄存器。
在后面的章节中，我们会展现很多指令，复制和生成 1 字节、2 字节、4 字节和 8 字节值。当这些指令以寄存器作为目标时，对于生成小于 8 字节结果的指令，寄存器中剩下的字节会怎么样，对此有两条规则：

- 生成 1 字节和 2 字节数字的指令会保持剩下的字节不变

- 生成 4 字节数字的指令会把髙位 4 个字节置为 0。后面这条规则是作为从IA32到    X86-64 的扩展的一部分而采用的



| 寄存器 |     详细释义      |      意义      |                 作用                 |
| :----: | :---------------: | :------------: | :----------------------------------: |
|   ax   |  accumulator reg  |   累加寄存器   | 累加器可用于乘、除、输入/输出等操作  |
|   bx   |     base reg      |   基址寄存器   |       它可作为存储器指针来使用       |
|   cx   |    counter reg    |   计数寄存器   |  控制循环次数或用CL来指明移位的位数  |
|   dx   |     data reg      |   数据寄存器   |  乘除运算时作为默认的操作数参与运算  |
|   si   |   Source index    | 来源索引寄存器 | 存放存储单元在段内的偏移量，方便寻址 |
|   di   | Destination index | 目的索引寄存器 | 存储器指针、串指令中的目的操作数指针 |
|   bp   |   Base pointer    | 基址指针寄存器 |  被调用者保存/栈基址寄存器-指向栈底  |
|   sp   |   Stack pointer   | 堆栈指标寄存器 |          栈寄存器-指向栈顶           |



除了整数寄存器，CPU还维护一组单个位的条件码寄存器，他们描述了最近的算术或逻辑操作的属性，通过检测这些寄存器来执行条件分支指令。



### 标志寄存器

- 进位标志**CF**(CarryFlag)**【无符号】**

  > 进位标志。最近的操作使最高位产生了进位。可用来检查无符号操作的溢出

  

- 零标志**ZF**(ZeroFlag)

  > 零标志。最近的操作得出的结果为0

  

- 符号标志**SF**(SignFlag)**【有符号】**

  > 符号标志。最近的操作得到的结果为负数

- 溢出标志**OF**(OverflowFlag)**【有符号】**

  > 溢出标志。最近的操作导致一个补码溢出——正溢出或负溢出

  

- 奇偶标志**PF**(ParityFlag)

- 辅助进位标志**AF**(AuxiliaryCarryFlag)

  

除了**leaq**的所有指令，都该设置条件码，因为leaq指令是用来进行地址计算的。

对于逻辑操作，例如XOR，进位标志和溢出标志会设置成0。

对于移位操作，进位标志将被设置为最后一个被移出的位，而溢出标志设置为0。

INC和DEC指令会设置溢出和零标志，但是不会改变进位标志。

---

还有两类指令他们只设置条件码而不改变任何其他寄存器

<img src="https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/CSAPP/1-6/202210152242304.png" alt="image-20221015224209196" style="zoom:50%;" />