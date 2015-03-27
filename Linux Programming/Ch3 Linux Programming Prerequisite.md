#Ch3 Linux Programming Prerequisite
##一、Programming Principle
###1.Abstract VS Concrete
从大规模软件质量保障的层面上来讲，代码正越来越抽象

* 将本质（算法等）抽象出来，将具体问题封装
* *Meta Programming*  
写产生代码的代码
* Linux下很多命令的处理方式也很抽象
* 抽象使得代码的正确性提高  
具体环境中的问题在抽象环境中更容易表现出来
* 抽象使项目的整体开销降低  
因为问题更容易表现，测试成本得到降低
###2.API的调用与选择
* 库与库之间的功能往往很类似  
如果只调用WIN32 API，理论上讲是可以实现所有功能的，*只是写得复杂一点*  
使用更高层的库，功能可能减少，但调用更方便
* 库的层次越高，调用的接口越令人舒适  
开发周期更短，但效率更低
* 只要运行效率能够忍受，通常都会使用高层库
* 软件公司通常推自己的私有库
* 选库时，能调通用库的尽量用通用库
* 即使是写底层程序，仍然是在做调用（硬件接口）

> ######接口的层次
> 	硬件——驱动——内核——Windows API/Linux系统调用——C Library/STL/...——Java/.Net
> * API/系统调用是用户态程序能够调用到的最底层
> * 如果全部调用标准库，那么代码就是跨平台的  
> 而且效率也没差多少
> * Java和.Net因为增加了一层虚拟机，所以效率差别达到数量级水平
> * MFC的层次比API要高，但它由API直接产生
##二、编程工具
###1.编辑工具  
vim、emacs
####习惯
* 代码缩进  
tab用空格代替（？）  
避免不同编辑器造成的不同显示效果
* {}的位置（？）  
令代码紧凑，同屏内容显示的越多，理解就越快
* 中文  
要想通用，编码格式改成UTF-8
###2.编译、链接  
* gcc/g++  
* 新兴的*clang*
###3.调试  
gdb  
gdb存在大量的前端
###4.make命令
用于自动编译链接的脚本
###5.版本控制工具  
* CVS（好老）
* SVN
* Git
* hg（？）
##三、编程语言
###1.高级语言
* C/C++、Java、Fortran
* ELF二进制格式  
Executable and Linkable Format  
###2.脚本
* shell脚本
* Perl, Ruby, Python, tck/tk, sed, awk  
使用此类脚本可以跨平台使用  
脚本通常解释执行
###3.开发工具
* gcc
* gdb
* 二进制工具  
as, ld, ar, ldd  
一般不直接调用，而由gcc作为前端选择调用  
ldd用于查询库的依赖
* make
###4.编译、链接的过程
####最简单的编译链接过程
	编译——链接——执行

* 逐个文件分别编译  
单文件——单文件  
还可细分
* 所有目标文件一起链接  
多文件——单文件
####展开的过程
* 编译细分为预处理、编译到asm和编译到目标文件
	* 带#的“代码”统统都是预处理
* 多输入、单输出的过程只发生在链接阶段
* 编译时操作与运行时操作
* 静态链接与动态链接  
-lm参数含义：在系统中寻找libm系列的库文件，似乎优先载入动态库
###5.库与头文件
* 静态库
* 动态库/共享对象(so)
###6.其它语言
* Java  
编译到字节码，仅此而已  
Java没有链接的问题，因为它**不需要**  
*可以理解为所有.class文件都是动态链接库*
* .Net平台  
编译到CLR
* VC++及Delphi  
###7.编译的具体过程
（略）
###8.常用命令
* gcc -c  
编译
* gcc  
链接或编译+链接
* g++  
简单地更换前端
###9.gcc的选项
	gcc [options] [filename]
* -E  
仅预处理
* -S  
只预处理、编译
* -c  
只预处理、编译、汇编
* -o  
制定输出文件名
* -g  
产生调试工具必需的符号信息
* -O/On  
优化程度控制
* -Wall  
显示所有警告信息
* -Idir  
指定额外的头文件搜索路径  
没有空格  
*需要指定多个路径时使用多个-I选项*
* -Ldir  
指定额外的库文件搜索路径
* -lname  
链接时搜索指定的库文件
* -DMACRO[=DEFN]  
定义`MACRO`宏的值为DEFN  
对应有-UMACRO的选项用于解除定义  
*但是选项中宏的定义/解除定义发生在代码预处理之前，解除定义往往起不到效果*
###10.文件扩展名
* .c
* .i
* .cc, .cp, .cpp, .c++, .C, .cxx
* .ii
* .h
* .H, .hh
* ……
###11.gdb
*不想用就算了*

GNU DeBug

* 调试时-g是必备选项
* 建议关闭优化选项  
可能导致指令与代码无法对应，从而无法正确定位错误位置
####gdb的功能  
* 设置断点
* 监视变量值
* 单步执行
* 修改变量值
####gdb命令
* file  
打开要调试的文件
* break/tbreak  
设置断点  
行号、函数名、地址  
tbreak设置临时断点
* run
* list
* next  
执行一条语句，不进入函数内部
* step  
执行一条语句，进入函数内部
* display
* print  
临时显示表达式的值
* kill
* quit
* shell
* make
###12.make&makefile
多文件项目可使用IDE，也可以手动使用make
####make&makefile
* makefile描述模块间的依赖关系
* make命令根据makefile对程序进行管理和维护
* make判断被维护文件的时序关系  
实际项目非常大，编译需要的时间很长；修改部分代码后，并不需要对全局所有代码都再编译一遍  
普通的shell脚本无法满足增量编译的需求
* 定义整个工程的编译规则
* 自动化编译
####增量编译
执行makefile时，判断当前已有的可执行文件与目标代码是否比源代码时间晚，如果符合这一条件，就认为代码没有修改过；否则就需要重新编译一次  
*因此需要注意系统时间*
####make命令
	make [-f Makefile] [option] [target]
make查找的脚本名默认为Makefile，如果非此名称，就需要以-f选项手动指定  

* `#make target`
* `#make`
* `#make clean`
#####安装软件时的各项操作
* `./ configure`与`make distclean`互为逆操作
* `make [all]`与`make clean`互为逆操作
* `make install`与`make uninstall`互为逆操作
####makefile的规则结构
	target ...: prerequisties ...
		command
		...
* prerequisties之后必须换行
* 命令前必须以Tab缩进
* target是一个目标文件，可以是目标代码，也可以是可执行文件
* prerequisties是要生成target所需要的文件或目标
* command是make需要执行的命令  
可以是任意的shell命令
* 默认隐含了目标all，依赖于第一个目标
####伪目标
* 伪目标只是一个标签，显式地指明这个“目标”才能让它生效
* 伪目标不能与文件名重名，但可以使用标记`.PHONY`显式指明一个目标是伪目标  

		.PHONY.clean
		clean:
			...
* 伪目标一般没有依赖文件  
*但也可以指定*
* 伪目标同样可以作为默认目标，只要放在第一个
####多目标
当多个目标依赖于同一个文件，并且生成命令大体类似，就可以使用自动化变量`$@`表示目前规则中所有目标的集合

	bigoutput littleooutput:text.g
		generate text.g -$(subset output,,$@)>$@
等价于：

	bigoutput:text.g
		generate text.g -big>bigoutput
	
	littleoutput:text.g
		generate text.g -little>littleoutput
####预定义变量
* $<  
第一个依赖文件
* $?  
比目标新的依赖文件
* $+  
所有的依赖文件，包含重复值
* $^  
所有的依赖文件，去除了重复
* $*  
不包含扩展名的目标文件名称
* $@  
目标的完整名称
* $%  
目标为归档文件成员时为档案名

> 更多变量参见*[http://blog.chinaunix.net/uid-25100840-id-1751135.html](http://blog.chinaunix.net/uid-25100840-id-1751135.html "make的自动变量和预定义变量")*
####多目标扩展
	<targets...>:<target-pattern>:<prereq-patterns>
		<commands>
		...
> ######例子
> 见ppt
> 
> * 目标从`$object`中提取
> * %.o表明要所有以.o结尾的目标，即`$object`集合的模式
> * 依赖模式%.c取模式%.o中的o，并为其加上.c的后缀，依赖目标因此成为foo.c bar.c
####使用函数
语法：

	$(function arguments)
	${function arguments}
* 字符串处理  
字符串替换：`$(subset from,to,text)`  
去除前后空格：`$(strip string)`
* 文件名操作函数  
取目录名：`$(dir names)`  
去除扩展名：`$(basename names)`  
* foreach
`$(foreach var,list,text)`  
将list中的每一个参数进行处理后放入var
* if  
`$(if condition,then-part)`  
`$(if condition,then-part,else-part)`
* call函数  
`$(call expression,param1,param2,...)`  
将表达式中的$(1)等变量做替换
###12.软件设计原则
####清晰原则  
* 尽量少用花哨的编程技巧
* 问题并不复杂时，尽量不要用这些技巧，以后会造成软件维护困难
####吝啬原则
* 能把软件写小就不要写大
* 功能上，可以分在不同软件中的功能不要集成在一起
* 稳定性、清晰性、性能指标等的要求
####扩展原则
* 能给用户定制的参数尽量给用户定制
* 有助于延长软件的生命周期
