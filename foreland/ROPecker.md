# ROPecker

原paper地址：[NDSS 2014](http://www.internetsociety.org/doc/ropecker-generic-and-practical-approach-defending-against-rop-attacks)。

### ROPecker 防御原理

ROPecker针对ROP攻击的以下两点特征进行防御：

· 进行一次ROP攻击时，程序的执行流通常包含非常长的gadget路径并且由大量的ret,jmp指令进行连接和跳转。

· ROP攻击时代码的执行指针通常会在代码段中进行大幅度的跳转

### ROPecker 防御设计

针对ROP攻击的两点特征，ROPecker设计了如下机制对ROP攻击进行防御：

**· Offline pre-processor**

ROPecker需要预先对于将要执行的程序进行分析。通过对于程序的提前的离线分析，对于程序的二进制code进行分析并且按照bit-vector建立IG database。在提前分析的过程中，ROPecker将代码按照1byte->4bit将每一段代码按照其语句内容转换为与bit-vector对应的内容并且存储于IG database中。当判断程序是否存在ROP攻击时，通过对于相应代码段对应的IGdatabase中的代码进行查询，通过分析结果即可判断程序是否陷入了异常执行。

![screenshot from 2016-01-07 16 55 18](https://cloud.githubusercontent.com/assets/7068001/12166254/9479ff00-b55f-11e5-8922-f566da659cbd.png)

**· sliding window**

根据ROP攻击具有的在代码段中进行大幅度跳转的特点，ROPecker进行了sliding window的设计。当代码在当前的sliding window中执行时认为并未受到ROP攻击，不触发对于ROP攻击的检查。而当下一条要执行的代码进入非sliding window中的代码段时，触发对ROP攻击的判断和检查。如果判断为未受到ROP攻击则更新sliding window的范围。

sliding window的设计通过针对ROP攻击的特征，在保证对于ROP攻击有效侦测的同时，可以大大减少触发侦测的次数以保证ROPecker的效率。通过对于sliding window大小的设置可以在效率和精度上面进行trade off。论文中推荐8KB或者16KB大小的sliding window。

![screenshot from 2016-01-07 17 11 09](https://cloud.githubusercontent.com/assets/7068001/12166552/b20bc826-b561-11e5-844b-6f0c03b41992.png)

**· risky system call**

由于例如mprotect和mmap2等函数会使得执行sliding window外的代码不会触发报警，所以对于类似的有风险的系统调用，ROPecker也会触发对于ROP的检查。

**· Runtime ROP detection**

当程序由于执行当前不在sliding window中的代码而触发Runtime ROP detection时，ROPecker会进行三步检查。

*condition filtering*

 通过检查触发ROP detection的位置，先将不可能是ROP攻击的正常的执行流排除，以降低ROPecker的overhead。通过几个简单的整数比较就可以对于所有的触发ROP detection的情况进行filter从而降低对于正常执行的overhead。
 
*past payload detection*

根据程序执行过程中获得的LBR(last branch record)寄存器中记录的对于上一个branch condition的判断数据，。

