# ROP的历史发展

**Stack smashing attacks** 是最早的溢出攻击方式，攻击者通过堆栈溢出覆盖写入想要执行的指令，达到攻击的目的。然而 Stack smashing attacks 并不是无敌的，其对抗技术就是 **微软** 在 **2004年** 引入Windows XP SP2中的 **DEP** (Data Execution Prevention)，和 **Linux PaX Project** 在 **2001年** 提出的 **ASLR** (Address Space Layout Radomization)，通过这两种技术的保护下Stack smashing attacks一定程度上揭制。

随后出现了 **Return-to-library technique** ，简称 **Ret2Lib** 。思路是绕过DEP防护跳转到共享库，将参数压栈，执行注入`\bin\sh`之类的程序并传入参数实现攻击。

Ret2Lib 技术在很长一段时间内都能使用，直到 `x64` 体系的出现。`x64` 相比于 `x86` ，在函数参数的传递不再完全依赖堆栈，其规定函数的第一个参数必须保存到第一个寄存器即 `eax` ，这导致单纯的把参数地址放在堆栈并不能调用需要参数的函数。在这种情况下，**Borrowed Code Chunks** 技术就出现了。这种技术是 Ret2Lib技术的一个进化版，它不再仅仅把返回地址指向整个函数入口，通过借现有的指令实现修改寄存器的值，例如修改 `eax` 寄存器为想要执行的函数参数，在 `x64` 架构上实现二进制攻击。

到目前看来，这一系列的技术进化已经越来越接近 **ROP** 了，但是对于ASLR，随机化的 libc 地址导致调用 libc 的 Borrowed Code Chunks 技术找不到libc的入口，ROP 和 Borrowed Code Chuncks 的区别在于 ROP 的恶意指令完全通过拼接实现，而不是拼接修改寄存器作为参数执行共享库中的函数进行攻击。这些指令片段 (gadget) 通过跳转指令连接在一起，实现一整个攻击程序指令。

现在是2015年底，系统安全领域ROP攻防相关的文章层出不穷。从历史发展的角度来看，15年前的系统对二进制攻击的系统防御机制在现在看来漏洞百出，如果人能够穿越回去，完全可以做到无法无天，也许再过10年如今的防御机制同样是破烂不堪的。
