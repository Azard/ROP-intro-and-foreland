# ROP前沿攻防

## 本章目录

* [ROP前沿攻防](./README.md)
    + [ROPecker - NDSS 2014](./ROPecker.md)
    + [ROP is still dangerous - USENIX Security 2014](./ROP-is-still-dangerous.md)
    + [Code Pointer Integrity - OSDI 2014](./Code-Pointer-Integrity.md)
    + [Missing the Pointer - S&P 2015](./Missing-the-Pointer.md)

## 简要介绍

本章内容包含数篇博客形式的文章，内容排序上是提出了一种防御策略，再提出一种攻击策略能够攻破之前的防御策略，一攻一防，再攻再放， 以实践和口语化的描述介绍了近期与ROP相关的安全领域顶会的成果，提炼其精华给读者。

### ROPecker 摘要

这篇文章发在安全领域顶会[NDSS 2014](http://www.internetsociety.org/doc/ropecker-generic-and-practical-approach-defending-against-rop-attacks)上。提出了ROPecker，一个针对ROP攻击的特点开发出的ROP攻击的检测和防御工具，在`Linux-x86`系统上实现。

ROPecker的防御策略依据了ROP攻击的两个特征：
* ROP攻击的调用链通常特别长，并且由jmp指令和分支判断指令作跳转。
* ROP攻击通常会在代码段进行大幅度的跳转。

ROPecker需要假设**DEP** (Data Execution Prevention) 机制打开。针对ROP攻击的特性进行了如下设计：
1. 先对ROPecker保护和检测的相应程序进行一次离线的gadget分析，并且使用硬件上实现的**LBR** (last branch record) 寄存器记录代码执行流的branch信息。
2. 在程序执行过程中，通过**sliding window**机制，即将不在代码当前执行片段周围的代码变为**不可执行**状态，一旦执行了不在**sliding window**中的代码就触发ROPecker对ROP攻击的分析机制。
3. 分析机制基于离线的gadget分析结果，根据当前程序的过去和未来的执行进行仿真分析，如果发现执行流不符即判断程序受到了ROP攻击，强制终止程序，达到检测和防御的目的。

ROPecker是第一个可以针对所有形式ROP攻击的一种general的，不需要源代码，二进制代码重写，并且非常有效率的防御机制。ROPecker的overhead对于CPU 2.6%，硬盘IO 1.56%，带宽 0.08%。

### ROP is still dangerous 摘要

这篇文章发在安全领域顶会[USENIX Security 2014](https://www.usenix.org/conference/usenixsecurity14/technical-sessions/presentation/carlini)上。对ROP问题进行了进一步研究，提出了3种全新的ROP攻击方法：
* Call-Preceded ROP
* Evasion Attacks
* History Flushing

这些攻击方法能够攻击目前比较成功的**KBouncer**，和上一小结介绍的**ROPecker**两种防御方法，说明了modern ROP攻击的危险性。

文章分析了**KBouncer**防御ROP的原理：
1. Call-Preceded原则，没有恶意的代码指令执行的时候，`ret`指令回到的地址的上一条指令一定是`call`。
2. ROP的攻击一般都是由短的gadget组成的长串指令来执行的。

根据分析的防御原理，文章重点介绍了**History Flushing**方法能够通过大段的`NOP`的指令，绕过**KBouncer**的防御从而实现ROP攻击。文章提出一个观点：ROP的防御必须着眼于正常执行和ROP攻击执行的最基本的差别。

### Code Pointer Integrity 摘要

这篇文章发在系统领域顶会[OSDI 2014](https://www.usenix.org/node/186160)上。本文首先和已经应用于工业上的**CFI** (Control Flow Integrity) 进行比较，CFI有粗粒度和细粒度两种，需要在安全性和性能上做出trade off，本文提出的CPI安全性和性能都更好。

现有的技术CPS将内存被分成了两部分：
* 一部分是安全内存用来存放代码指针相关，包含一个safe heap和多个safe stack给各个线程
* 另一部分是普通内存，包含普通heap，多个普通stack和只读的代码

这两部分用硬件保证了隔离，只有需要操作代码指针的指令才能处理安全内存。在CPS模式下，敏感指针被认为是代码指针，攻击者无法更改代码指针。

本文的CPI内存的组织结构和CPS是一样的，也分成安全内存和常规内存，但是安全内存保护的是敏感指针和元数据。

在CPI模式下，敏感指针被认为是代码指针和被用来处理敏感指针的指针，并保护这些敏感指针。保护的对象从所有的敏感指针扩展成内存bug产生的控制流攻击。对于敏感指针的保护，由CPS的隔离扩展到了CPI的**隔离加运行时检查**。因此CPI对安全保障性能比CPS更高。对于普通数据的处理，由于指令级别的区域隔离，CPS和CPI都很快。

CPI对于未修改的C/C++代码的overhead为8.4%~10.5%，CPS的overhead为0.5%~1.9%。

### Missing the Pointer 摘要

这篇文章发在安全领域顶会[S&P 2015](http://www.ieee-security.org/TC/SP2015/program.html)上。
上一节介绍的在2014年提出的CPI (Control Pointer Integrity) 作为一种新的防止控制流劫持的手段被提出，通过保护Code Pointer的方式防止攻击，性能表现优异，最大开销平均不超过10%。

然而，其设计用于保护Code Pointer的Safe Region可以利用Data Pointer的overwrite漏洞攻击，而Data Pointer是CPI为了性能而没有保护的，本文利用这一点提出了可以攻破CPI保护的攻击技术，并在Nginx服务器上做了实际测试。

根据CPI的论文，我们假设被保护的程序同时开启了DEP和ASLR，在此基础上要进行攻击。攻击的要点有二：
* CPI的Safe Region在`x86-64`及`ARM`架构上并没有利用硬件或软件机制保护起来防止访问或修改，而是基于信息隐藏。作者认为由于程序本身没有指针指向Safe Region，其中的数据也就不会泄露，这一假设是错误的，我们可以通过基于时间的侧信道攻击来获取其中的数据。
* 作者认为其Safe Region在程序本身不暴露其位置，为了获取其位置势必进行猜测并引起崩溃，只需监测暴力破解时产生的异常数量崩溃事件即可及时发现攻击，但实际上可以在一次崩溃都不引发的情况下定位Safe Region（约需98小时，由于太慢另外实现了一种允许导致崩溃的攻击需要6秒，实验中引起了13次崩溃，会议演讲时再次演示，引起了10次崩溃）。

以上两点突破了CPI作者所认定的前提条件，暴露了CPI的一些问题。
