# ROP入门与前沿

[ROP](https://en.wikipedia.org/wiki/Return-oriented_programming)全称Return-oriented programming，翻译成中文叫做面向返回编程，是一种可以绕过诸多防御机制的内存攻击技术。狭义上的黑帽黑客(black hat)是指能hack系统的人，ROP是hacker们常用的招式。

![BlackHat](./resources/1-black-hat.jpg)

## 关于本系列文章

《ROP入门与前沿》假设的读者是略懂操作系统和汇编，没有二进制攻防的基础，借助样例从ROP入门开始介绍，再引入安全实战领域和学术界关于ROP攻击和防御的最新成果，深入浅出的让读者从零开始入门ROP并了解最前沿的研究。对ROP有一定了解的读者也可以通过该系列文章快速了解ROP的前沿研究。

[ROP入门](./introduction/README.md)部分面向没有ROP基础的读者，介绍了什么是ROP，ROP的出现以及ROP的发展历史等内容，再详细的给出两个使用的最广泛的CPU指令集x86跟ARM上的ROP攻击示范样例，让读者对ROP有一个全方位的认识和了解。

[ROP前沿攻防](./foreland/README.md)部分由数篇博客形式的文章组成，从实践的角度深入浅出的介绍了系统安全领域最新的研究成果。每篇文章对应一篇近期的paper，免去形式化的描述，以实践和口语化的叙述提炼安全领域研究成果的精华。本章的内容排序一防一攻，再防再攻，跌宕起伏，一方面让读者了解到近期系统安全领域对ROP的研究都在做些什么，另一方面根据已有的研究成果让读者激发出新的攻防思路。

关于每篇文章的简要介绍见[ROP前沿攻防](./foreland/README.md)。

## 目录

* [前言](./README.md)
* [ROP入门](./introduction/README.md)
    + [什么是ROP](./introduction/What-is-ROP.md)
    + [ROP的历史发展](./introduction/ROP-history.md)
    + [x86 ROP样例]()
    + [Android ARM ROP样例]()
* [ROP前沿攻防](./foreland/README.md)
    + [ROPecker - NDSS 2014](./foreland/ROPecker.md)
    + [ROP is still dangerous - USENIX Security 2014](./foreland/ROP-is-still-dangerous.md)
    + [Code Pointer Integrity - OSDI 2014](./foreland/Code-Pointer-Integrity.md)
    + [Missing the Point(er) - S&P 2015](./foreland/Missing-the-Pointer.md)
