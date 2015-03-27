#Ch3-2 Kernel Driver
##一、Linux内核
###1.什么是内核
* 操作系统是一系列程序的集合，其中最重要的部分构成了内核
* 所有操作系统都有内核
###2.单内核与微内核
####单内核
单内核是一个很大的进程，内部可以分为若干模块，运行时是一个独立的二进制文件，模块间通讯通过直接调用函数实现
####微内核
微内核中大部分内核作为独立的进程在特权下运行，通过消息传递进行通讯
###3.Linux内核的能力
* 内存管理
* 文件系统
* 进程管理
* 多线程支持
* 抢占式
* 多处理器支持
###4.Linux内核区别于其他UNIX商业内核的优点
* 单内核+模块支持
* 免费/开源
* 支持多种CPU，硬件支持能力非常强大
* Linux开发者都是非常出色的程序员
* 通过学习Linux内核的源码可以了解现代操作系统的实现原理
###5.Linux内核层次结构
* 内核直接与硬件控制模块对接
* 包含进程控制、内存管理、虚拟文件系统与网络协议
* 所有功能封装成系统调用，提供给上层
###6.Linux内核源代码的获取
####https://www.kernel.org
* 提供Linux标准内核，是最原始的版本
* 下载到的版本与具体使用的Linux发行版本无关  
*通常各发行版Linux会对内核进行修改*
####apt-get
	apt-cache search linux-source
	apt-get install linux-source-3.2
下载位置通常在/usr/src
####从Ubuntu的源码库获取内核源码
	git clone git://kernel.ubuntu.com/ubuntu/ubuntu-hardy.git
下载整个repo，可以看到所有修改历史
####后续操作
* 解压  

		tar jxvf ~/linux-3.2-tar.bz2
* 清除先前编译产生的目标文件

		make clean
* 配置内核

		make menuconfig
* 编译内核

		make
		#通常不用，虽然内核源码中提供了此命令，因为默认的make会将内核全部编译，且没有压缩机制
		make zImage
		make bzImage			#推荐使用以上两种方式生成内核压缩包
		make modules			#编译不总是使用的功能为模块，文件名为*.ko
* 启用新内核

		make install			#慎用！因为直接将内核复制到/boot，如果发生问题将不可逆
		#通常另外配置引导菜单
* 初始化程序的建立
	* initrd
	
			mkinitrd /boot/initrd.img $(uname -r)
	* initramfs

			mkinitramfs -o /boot/initrd.img 2.6.24-16
			update-initramfs -u

	以上两组命令使用一个即可
####Debian与Ubuntu的简便办法
	make-kpkg					#用于make menuconfig之后
#####好处
* 把后面所有的部分自动做完
* 编译好的内核打成deb安装包
* 可以复制到其它机器安装
> ######补充：`sudo`与`fakeroot`
> * 打deb包时，包内文件通常需要提升到root权限  
> 此时可以让一个普通用户*假装*成root，使用`fakeroot`命令
> * `sudo`执行的命令真正获取到了root权限
> * `fakeroot`命令**创建一个虚拟环境**，在这个环境中拥有root权限  
> 不会对实际环境产生任何影响
##二、驱动
###1.驱动程序
* 许多常见的驱动的源代码集成在内核源码中  
如键盘、鼠标等等
* 其它驱动以模块的形式存在
* 所有驱动必须以内核态运行
* 编译驱动需要内核头文件的支持  
可以没有内核的源码，但驱动与内核是一体的，必须调用内核的内部函数  
起码要获知内核提供的函数，需要安装内核源码头文件包
###2.加载模块
####底层命令
* `insmod`  
insmod <module.ko> [module parameters]  
*只有超级用户才能使用此命令*
* `rmmod`
####高层命令
* `modprobe`  
`modprobe -r`用于释放模块
###3.模块依赖
* 一个模块A引用另一个模块B所导出的符号，我们就说模块B被模块A引用  
如果要装载模块A，必须要先装载模块B
* 否则，模块B导出的符号的引用就不可能被链接到模块A中  

这种模块间的相互关系就叫做**模块依赖**
####模块的依赖
* 自动按需加载
* 自动按需卸载
####模块相关命令
* moddep  
查询模块依赖的所有其它模块
* lsmod  
列出已经装载的模块列表  
与`cat /proc/modules`等价
	* /proc  
	/proc是Linux中一个特殊的目录，其目录内部的内容本质上是一块内存，提供了**Linux内核到用户的访问渠道**，向用户开放部分内核信息
* modinfo
###4.模块之间的通讯
可以共享变量、数据结构，也可以调用对方提供的函数
> ######例：模块间的通讯实例
> 包含add_sub、test两个模块
> 
> * 通常提供一个包含所有函数声明的头文件供其它模块使用
> * `EXPORT_SYMBOL`  
> 告知系统对应的函数提供给其它模块使用
> * `EXPORT_SYMBOL`导出的函数，**整个内核**中都不能有函数与之重名
> * 使用导出函数的模块直接使用相关函数即可
> * `printk`也是以同样的方式提供给其它模块的  
> 因此内核中的导出函数是我们唯一可以调用的
> * 模块的编译  
> test模块在编译时需要额外提供符号表，并在EXTRA_CFLAGS中添加新的标志位
####符号表的导出
* 使用`EXPORT_SYMBOL`和`EXPORT_SYMBOL_GPL`
* 只能导出函数和全局变量  
临时变量不能导出
* 模块只能使用内核或其它模块导出的符号
* /proc/kallsyms中显示了所有导出的符号
###5.Linux内核模块与应用程序的区别
* 运行  
应用程序由用户态运行，占用用户空间的地址  
内核模块占用内核地址
* 入口  
应用程序由main()作为入口  
内核模块的入口由module_init()指定
* 出口  
应用程序没有出口  
内核模块的入口由module_exit()指定
* 运行  
应用程序可以直接运行  
内核模块的运行需要insmod加载
* 调试  
应用程序使用gdb调试  
内核模块使用kdbug, kdb, kgdb等工具进行调试
####注意点
* 不能使用C库开发驱动程序
* 驱动程序没有内存保护机制  
由于内核级别高于系统，驱动出错无法由系统提供任何提示  
可以访问任何物理硬件层面的东西，一旦出错会*死得很难看*  
必须千万小心
* 小内核栈  
内核需要尽可能少地占用系统资源，能减少调用层次就要尽量减少  
*能不递归，就不递归*
* 并发上的考虑  
驱动中的执行顺序需要以另一种思维考虑  
由于完全无法控制系统以几个进程调用驱动，各函数的运行顺序几乎无法控制  
必须考虑大量线程随机运行时驱动的运行情况是否正确
###6.最简单的内核模块
    #include <linux/kernel.h>
    #include <linux/module.h>
    #include <linux/init.h>
	
    static int __init hello_init(void)
    {
    	printk(KERN_INFO "Hello world\n");
    	return 0;
    }
	
    static void __exit hello_exit(void)
    {
	    printk(KERN_INFO "Goodbye world\n");
    }
	
    module_init(hello_init);
    module_exit(hello_exit);

* 在内核中需要输出内容到屏幕时，可以使用内核封装好的`printk()`  
语法与printf类似，只是在所有参数前面多了一个`KERN_INFO`  
`printk`*没有浮点数的支持*  
`KERN_INFO`可以省略，用于通知用户输出内容的类型
* `static`限定此函数仅在当前文件中可以调用  
无法通过链接被其它文件调用
* `__init`与`__exit`是内核编程独有的关键字
###7.内核模块的编译
* 自定义的makefile文件依赖于Linux系统自带的源代码/头文件及其makefile
* 需要提供-C参数提供内核源码路径与-M提供当前路径
###8.与硬件打交道
*不很困难，但很繁琐*  
以S3C2440的LED灯驱动为例讲解

* 硬件提供什么接口，就以相应的方式调用  
*事实上很多硬件厂商本身并不提供任何接口手册,需要****猜***  
需要给出物理地址和设置值的说明
* `ioremap`将内存地址与新的物理存储单元重新绑定映射
###9.模块的参数传递
* 参数在模块加载时传递
* 参数使用module\_param宏来声明  
module\_param(变量名称,类型,访问许可掩码)
* 支持很多参数类型  
**但不包括浮点类型**
###10./proc
* 内核模块和系统交互的主要方式之一  
*另一种是系统调用*
* /proc是一个**伪文件系统**  
就是内存里的内容
* 可以通过/proc使用标准Unix系统I/O调用**访问进程地址空间**
* 修改某些文件可以改变内核参数
* /proc下的文件能查找到的大小都是0
* 大部分文件的时间戳信息会被随时刷新
####/proc的系统调用
* create_proc_entry()  
创建一个文件
* proc_symlink()  
创建符号链接
* proc_mknod()  
创建设备文件
* proc_mkdir()  
创建目录
* remove_proc_entry()  
删除文件或目录
###11.驱动的类型
* 字符设备  
习惯上用于I/O量较小的设备
* 块设备  
习惯上用于I/O量较大的设备
* 网络接口设备  
	* 字符设备与块设备都会在/dev中产生一个设备文件，但网络设备不会
	* 网络设备会在`ifconfig`中产生新的网络接口，以网络编程的方式访问该设备

驱动编写的形式可以任意选定，只在应用程序使用时有所不同
###12.字符设备驱动的编写
字符设备驱动数量较多，形式相对简单
####文件操作——字符设备驱动的对上接口
各种I/O的文件操作在驱动中需要重新实现，由操作系统将相应的应用程序调用转接到驱动上

* read
* write
* flush
* ioctl  
*只规定参数的形式，具体的命令如何使用，完全由驱动自行定义*
####支持操作类型的定义
	struct file_operations scull_fops=
	{
		.owner=THIS_MODULE;
		.llseek=scull_llseek;
		.read=scull_read;
		.write=scull_wtire;
		.ioctl=scull_ioctl;
		.open=scull_open;
		.release=scull_release;
	}

* 此结构体的类型由系统定义
* 内部各字段由系统定义
* owner表明当前驱动的所有者  
可以使用宏
* 其它各字段的值为函数指针
####两个基本结构
即使设备本身并不实际存在于磁盘上，NFS仍然以文件的方式对其进行统一处理
#####file结构体
#####inode结构体
除了标记普通文件的域外，还有两个字段用来特别标记设备文件

* `dev\_t i\_rdev`  
标记设备号
* `struct cdev *i\_cdev`  
用于设定设备参数
###13.字符设备驱动程序的初始化加载过程
####申请设备号
* 一个字符设备或者块设备都有一个主设备号和次设备号  
两者统称设备号
* 主设备号用来表示特定的驱动程序
* 此设备号用来表示使用该驱动程序的各设备
#####申请和释放设备号
* int register\_chrdev\_region(dev\_t first,unsigned int count,char *name)  
向系统申请一个设备号，以first为设备号起始值，申请count个，标识名为name  
标识名会出现在用户查询时出现在/proc/devices和/proc/sysfs中
* int alloc\_chrdev\_region(dev\_t \*dev,unsigned int firstminor,unsigned int count,char \*name)  
与第一种不同，由系统自动分配空闲的设备号  
要求系统提供firstminor以上的count个设备号
* void unregister\_chrdev\_region(dev\_t first,unsigned int count)  
将这些设备号释放

*`dev_t`中同时定义了主设备号和从设备号，需要获知具体的主、从设备号时，可以使用系统提供的宏`MAJOR`和`MINOR`*
####定义文件操作结构体file\_operations
前面已经讲过，此处略过
####创建并初始化定义结构体cdev
此结构体是所有字符设备的抽象，包含了大量字符设备共有的特性
#####cdev结构的初始化
	struct cdev *my_cdev=cdev_alloc();
	my_cdev->ops=&my_fops;
或者直接使用函数：

	void cdev_init(struct cdev *cdev,struct file_operations *fops)
在**手动获取到cdev空间后**对其进行初始化，这在驱动编写中十分常见
####将cdev注册到系统，并和对应的设备号绑定
* 将设备注册到系统中  
int cdev\_add(struct cdev \*dev,dev\_t num,unsigned int count)
* 释放一个已经注册的设备  
void cdev\_del(struct cdev \*dev)
####在/dev文件系统中用`mknod`创建设备文件，并将该文件绑定到设备号上
	mknod /dev/${device}0 c $major 0
即可将其绑定