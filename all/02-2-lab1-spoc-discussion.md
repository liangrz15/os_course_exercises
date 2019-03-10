# lec4: lab1 SPOC思考题

##**提前准备**
（请在上课前完成）

 - 完成lec4的视频学习和提交对应的在线练习
 - git pull ucore_os_lab, v9_cpu, os_course_spoc_exercises in github repos。这样可以在本机上完成课堂练习。
 - 了解x86的保护模式，段选择子，全局描述符，全局描述符表，中断描述符表等概念，以及如何读写，设置等操作
 - 了解Linux中的ELF执行文件格式
 - 了解外设:串口，并口，时钟，键盘,CGA，已经如何对这些外设进行编程
 - 了解x86架构中的mem地址空间和io地址空间
 - 了解x86的中断处理过程（包括硬件部分和软件部分）
 - 了解GCC的x86/RV内联汇编
 - 了解C语言的可函数变参数编程
 - 了解qemu的启动参数的含义
 - 在piazza上就lec3学习中不理解问题进行提问
 - 学会使用 qemu
 - 在linux系统中，看看 /proc/cpuinfo的内容

## 思考题

### 启动顺序

1. x86段寄存器的字段含义和功能有哪些？
cs: code segment
ds: data segment
ss: stack segment
es: extra segemtn
fs: 附加段寄存器
gs: 附加段寄存器

2. x86描述符特权级DPL、当前特权级CPL和请求特权级RPL的含义是什么？在哪些寄存器中存在这些字段？对应的访问条件是什么？  
CPL：当前特权级（Current	Privilege	Level)	保存在CS段寄存器（选择子）的最低两 位，CPL就是当前活动代码段的特权级，并且它定义了当前所执行程序的特权级别）  
DPL：描述符特权（Descriptor	Privilege	Level）	存储在段描述符中的权限位，用于描述 对应段所属的特权等级，也就是段本身能被访问的真正特权级。 RPL：请求特权级RPL(Request	Privilege	Level)	 
RPL： 保存在选择子的最低两位。RPL说 明的是进程对段访问的请求权限，意思是当前进程想要的请求权限。RPL的值可自由设 置，并不一定要求RPL>=CPL，但是当RPL<CPL时，实际起作用的就是CPL了，因为访 问时的特权级保护检查要判断：max(RPL,CPL)<=DPL是否成立。所以RPL可以看成是每 次访问时的附加限制，RPL=0时附加限制最小，RPL=3时附加限制最大。

3. 分析可执行文件格式elf的格式（无需回答）

### 4.1 C函数调用的实现

### 4.2 x86中断处理过程

1. x86/RV中断处理中硬件压栈内容？用户态中断和内核态中断的硬件压栈有什么不同？
2. 为什么在用户态的中断响应要使用内核堆栈？  
为了安全
3. x86中trap类型的中断门与interrupt类型的中断门有啥设置上的差别？如果在设置中断门上不做区分，会有什么可能的后果?
trap时不会关中断，而interrupt会关中断。

### 4.3 练习四和五 ucore内核映像加载和函数调用栈分析

1. ucore中，在kdebug.c文件中用到的函数`read_ebp`是内联的，而函数`read_eip`不是内联的。为什么要设计成这样？
%ebp可以直接获得。而%eip不能直接获得，必须通过函数调用，然后再从栈上获得。

### 4.4 练习六 完善中断初始化和处理

1. CPU加电初始化后中断是使能的吗？为什么？
不是。BIOS需要先完成自检，再进行中断使能

## 开放思考题

1. 在ucore/rcore中如何修改lab1, 实现在出现除零异常时显示一个字符串的异常服务例程？
2. 在ucore lab1/bin目录下，通过`objcopy -O binary kernel kernel.bin`可以把elf格式的ucore kernel转变成体积更小巧的binary格式的ucore kernel。为此，需要如何修改lab1的bootloader, 能够实现正确加载binary格式的ucore OS？ (hard)
3. GRUB是一个通用的x86 bootloader，被用于加载多种操作系统。如果放弃lab1的bootloader，采用GRUB来加载ucore OS，请问需要如何修改lab1, 能够实现此需求？ (hard)
4. 如果没有中断，操作系统设计会有哪些问题或困难？在这种情况下，能否完成对外设驱动和对进程的切换等操作系统核心功能？

## 课堂实践
### 练习一
在Linux系统的应用程序中写一个函数print_stackframe()，用于获取当前位置的函数调用栈信息。实现如下一种或多种功能：函数入口地址、函数名信息、参数调用参数信息、返回值信息。

### 练习二
在ucore/rcore内核中写一个函数print_stackframe()，用于获取当前位置的函数调用栈信息。实现如下一种或多种功能：函数入口地址、函数名信息、参数调用参数信息、返回值信息。
