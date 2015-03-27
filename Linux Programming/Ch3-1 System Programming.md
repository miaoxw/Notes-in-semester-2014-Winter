#Ch3-1 Linux System Programming
*——File system*
##一、文件与文件系统
###1.文件  
对Linux来说，文件只是**一个对象**，可以像文件一样读写，有如同文件一样的属性与权限
###2.文件系统  
组织文件的方式
####文件系统的多种含义
* 一种特定的文件格式
* 按特定格式进行了格式化的存储介质
* 操作系统（通常在内核中）用来管理文件系统以及对文件进行操作的机制及其实现
###3.文件类型与结构
####文件类型
* 普通文件
* 字符设备
* 块设备
* FIFO（管道）
* socket（网络）
* 符号链接
* 目录
####文件结构
字节流
###4.Linux的文件系统
####VFS
* 文件的统一管理与抽象都由VFS实现
* VFS将用户的文件操作转换为对应的驱动级别的操作
####VFS模型
打开一个文件后，VFS会在**内存**中创建4个对象

* 超级块  
用于描述当前的文件系统
* i-node  
用于标志文件
* 文件对象  
用于描述文件打开状态
* 目录项  
用于描述文件的目录关系
####Ext2文件系统
* 一个引导块，之后是若干个块组
* 引导块不属于文件系统管理  
由MBR或GPT负责管理  
所有文件系统均如此
* 每个块组中有若干个块  
	> 1超级块+n组描述符+1数据块位图+1索引节点位图+n索引节点表+n数据块
####硬链接与软链接
* 硬链接是文件系统下的两个文件，使用同一个i-node  
不能跨越文件系统，对应系统调用`link`
* 软链接存储被链接文件的文件名与路径  
可跨越文件系统，对应系统调用`symlink`
####`ls -l`的回顾
*链接计数只包含硬链接*
###5.系统调用与库函数
都以C函数的形式出现
####系统调用
* Linux内核的对外接口
* 用户程序与内核之间的唯一接口
* 提供最小接口
####库函数
* 依赖于系统调用
* 提供较复杂功能
####底层I/O系统调用
#####文件描述符
* 所有类Unix系统都存在此概念
* 直接使用数值类型代替  
`int fd`，但是值非负
* Linux内核在打开文件时分配一个文件对象，文件描述符指向内核中的**文件描述符表**元素，此元素再指向文件对象
* 每个进程启动后至少已经分配3个文件描述符  
对应stdin（0）, stdout（1）, stderr（2）  
定义于unistd.h
#####`open`/`creat`
* `open`既能打开文件也能创建文件
* `creat`只用于创建文件
* 如果文件打开/创建失败，返回-1
######flags参数
文件访问模式  

* O_RDONLY  
只读
* O_WRONLY  
只写
* O_RDWR  
读写

前三个模式中选用一个

* O_APPEND  
追加模式
* O_TRUNC  
文件已经存在时，定位到开始位置（覆盖模式）
* O_CREAT  
如果文件不存在，就创建一个
* O\_EXCL  
和O\_CREAT一起使用时，如果文件已经存在将报错，打开失败

这些模式可以任意选用0个或更多

> 使用creat相当于在open中使用以下flags，因而**可以完全被替代**：  
> `O_CREAT|O_WRONLY|O_TRUNC`
######mode参数
* 用于定义文件创建之后的权限
* 对应Linux中的八进制文件权限设定
* 使用umask可以用于控制禁止拥有的权限  
	> ######例：
	> * 原始权限：666  
	> rw-rw-rw-
	> * 掩码：022  
	> ----w--w-
	> * `permission&~umask`的结果  
	> 644，rw-r--r--
#####`close`
* 关闭时提供文件描述符作参数
* 成功返回0，失败返回-1
* 程序退出时应当关闭所有文件  
否则文件描述符将始终占用内核的内存资源，缓存中的内容也可能发生意外
#####`read`/`write`
* size\_t read(int fd,void *buf,size\_t count)  
返回读到的字节数，已到文件末尾为0，错误为-1
* size\_t write(int fd,const void *buf,size\_t count)  
返回写入的字节数，错误为-1
#####`lseek`
* 用于文件内的定位
* off\_t lseek(int fildes,off\_t offset,int whence)  
如果定位成功，返回操作后的偏移位置；如果失败，返回-1
######whence参数
* SEEK_SET  
偏移量从文件头计算
* SEEK_CUR  
偏移量从当前位置计算
* SEEK_END  
偏移量从文件尾计算
#####`dup`/`dup2`
* 用于复制文件描述符
* int dup(int oldfd)
* int dup2(int oldfid,int newfd)
* 如果成功，返回新的文件描述符，否则返回-1
* 描述符复制的主要应用是**文件重定向**
将新文件描述符对应的文件关闭，再使0/1/2号文件描述符指向原有的描述符位置
#####`fcntl`
文件描述符控制，几乎所有控制都可由此调用完成

* int fcntl(int fd,int cmd)
* int fcntl(int fd,int cmd,long arg)
* int fcntl(int fd,int cmd,struct flock *lock)
######cmd参数
* F_DUPFD  
使用此cmd也可以实现`dup`和`dup2`的功能
* F_GETFD/F_SETFD  
获取和设置文件描述符的close-on-exec标志
	> ######例：
	> ppt上的两段代码
	> 
	> * 第二段代码内容显然
	> * 第一段代码
	> 	* `fork()`处产生程序执行分歧，产生子进程
	> 	* `fcntl`设定文件描述符是否会被复制一份给子进程
* F\_GETFL/F\_SETFL  
设置文件描述符的标志（除close-on-exec）  
**并非所有标志都可通过F\_SETFL修改**  
只有O\_NONBLOCK（非阻塞方式）, O\_ASYNC（异步方式）, O\_APPEND（追加模式）, O\_DIRECT（直接模式，不通过缓存）, O\_NOATIME（读文件不更新访问时间）可以被修改  
* F\_GETOWN/F\_SETOWN
* F\_GETLK/F\_SETLK/F\_SETLKW  
对文件进行加锁、解锁等操作
#####`ioctl`
* 参数与`fcntl`相同  
用于对普通文件的设置  
*常用于驱动程序对设备文件的设定*
* 命令可以由驱动程序自定义
####标准I/O库
全部由C/C++标准库提供
#####文件流
######流与FILE类型
* FILE类型的指针
* 预定义stdin, stdout, stderr三个文件指针
######缓存I/O
* 三种模式
	* 全缓存  
	由库函数提供缓存，刷新时才写入
	* 行缓存  
	完成一行后写入
	* 无缓存
* `setbuf`与`setvbuf`  
	* 用于设定缓存模式
	* void setbuf(FILE \*stream,char \*buf)  
	这里的缓存大小在stdio.h中有宏预定义，为`BUFSIZ`
	* int setvbuf(FILE \*stream,char \*buf,int mode,size\_t size)  
	对于模式参数，\_IOBUF对应全缓存，\_IOLBF对应行缓存，\_IONBF对应无缓存

注：

FILE *的层次比文件描述符要高，在文件指针中，文件描述符是其一个字段
#####标准库函数
* 流的打开/关闭  
fopen/fclose  
库函数关注通用性，因此打开模式不使用比特位来设定
* 流的读写  
注：*宏定义的函数，参数不要有副作用*
	* getc/fgetc/getchat  
	getc是宏的形式，fgetc是函数的形式
	* ungetc  
	将字符放回流中  
	**撤销只能单步进行**，不能连续调用
	* putc/fputc/putchar
	* fgets(char \*s,int size,FILE \*stream)
	* gets(char *s)  
	不建议使用，因为不会在读取结束后追加任何结束符
	* fputs
	* puts
* 二进制流读写
	* fread(void \*ptr,size\_t size,size\_t nmemb,FILE \*stream)
	* fwrite(const void \*ptr,size\_t size,size\_t nmemb,FILE \*stream)  
	文本文件本质上是对二进制文件的读写格式约定  
	返回值为成功读写的字节数
* 格式化I/O
	* scanf(const char *foramt,...)
	* fscanf(FILE \*stream,const char \*format,...)
	* sscanf(const char \*str,const char \*format,...)  
	很常用于做字符串和数字的相互转换
	* printf/fptintf/sprintf
* 流的定位
	* fseek/ftell/rewind  
	ftell获取文件当前位置，rewind使当前位置重新指向文件头部
	* fgetpos/fsetpos
* 流的刷新
####流与文件描述符
#####流->文件描述符
int fileno(FILE *fp)
#####文件描述符->流
由于此时需要在用户空间进行封装，所以需要设定打开方式  
FILE *fdopen(int fildes,)
####临时文件
* char \*tmpnam(char \*s)  
生成一个临时文件文件名，参数可以是NULL
* FILE *tempfile()  
直接创建临时文件并打开
###6.高级系统调用
处理文件的属性或是目录
####stat/fstat/lstat
* stat与lstat使用文件名为参数，fstat使用文件描述符为参数
* 使用软链接时，stat寻找软链接指向文件的属性，而lstat寻找软链接本身的属性
####struct stat
包括多个属性

* st_ino  
文件的inode号
* st_rdev  
设备号，指向具体设备
* st_nlink  
文件硬链接的数目
* st_blksize
* st_blocks
####文件类型与模式
#####文件类型
* S_ISREG()  
是否为普通文件
* S_ISDIR()  
是否为目录文件
* S_ISCHAR()  
是否为字符设备文件
* S_ISBLK()  
是否为块设备文件
* S_ISFIFO()  
是否为FIFO文件（如管道）
* S_ISLNK()  
是否为符号链接文件
* S_ISSOCK  
是否为socket文件
#####文件权限
* r
* w
* x
* SUID  
在当前文件已有执行权限的前提下，SUID置为1可将执行权限提升到文件所有者的权限
* GUID  
在当前文件已有执行权限的前提下，GUID置为1可将执行权限提升到文件所有者所在组的权限  
设置在目录上时，在此目录中创建的文件自动继承目录的GID
* Sticky bit  
粘滞位，只有目录和文件的所有者可以删除此文件

**文件权限全部在struct stat的st_mode中表示**
####权限相关函数
#####int access(const char *pathname, int mode)
* 适用情况：并不想知道777是怎么分布的，只想知道当前执行的程序对此文件有没有执行权限、写权限
* 按**实际用户ID和实际组ID**测试文件存取权限
* 返回0为成功，返回-1为失败
* mode参数的宏
	* R_OK  
	文件可读
	* W_OK  
	文件可写
	* X_OK  
	文件可运行
	* F_OK  
	文件存在
#####`chmod`/`fchmod`
* int chmod(const char \*path, mode_t mode)
* int fchmod(int fildes, mode_t mode)
#####`chown`/`fchown`/`lchown`
修改文件的所有者

* int chown(const char \*path, uid\_t owner, gid\_t group)
* int fchown(int fd, uid\_t owner, gid\_t group)
* int lchown(const char \*path, uid\_t owner, gid\_t group)

这两类函数返回0为成功，返回-1为失败
#####`umask`
mode\_t umask(mode\_t mask)  
为进程设置文件存取权限屏蔽字，并返回以前的值
####链接
#####创建一个新的硬链接到已有文件
int link(const char \*oldpath, const char \*newpath)
#####删除硬链接中的一个，并且在计数归零时删除文件本身
int unlink(const char \*pathname)
#####创建一个符号链接
int symlink(const char \*oldpath, const char \*newpath)  
*软连接并不需要特别地`unlink`，只需要补充一个读取连接的函数即可*

以上三个函数返回0为成功，返回-1为失败
#####读取符号链接内容
int readlink(const char \*path, char \*buf, size\_t bufsiz)  
读取成功，返回向buf中写入字符数的计数，失败返回-1  
*存入的字符串末尾****没有****\0*
####处理目录
#####mkdir/rmdir
* int mkdir(const char \*pathname, mode\_t mode)
* int rmdir(const char \*pathname)
#####工作目录的修改
* int chdir(const char \*path)  
将工作目录修改为对应路径
* int fchdir(int fd)  
将当前目录改为文件描述符指定的目录  
返回0为成功，返回-1为失败

注：*当前工作目录是进程的属性，所以该函数只影响调用chdir的进程本身*
######`getcwd`
char 、*getcwd(char 、*buf, size、_t size)  
获得当前工作目录的绝对路径
####读目录
#####数据结构
都在<dirent.h>中定义

* DIR  
目录本身的属性

		typedef struct __dirstream DIR;
* dirent  
目录中每一项的属性

		ino_t d_ino;					/* inode number */
		char d_name[NAME_MAX + 1];		/* file name */
#####目录的操作
* DIR \*opendir(const char \*name)
* int closedir(DIR \*dir)
* struct dirent \*readdir(DIR \*dir)
* off\_t telldir(DIR \*dir)
* void seekdir(DIR \*dir, off\_t offset)

> ######例：目录扫描程序
> 见ppt第79页
###7.文件锁
####文件锁的作用
几个进程同时操作一个文件
####锁的位置
* 文件锁在内核中  
*与之相比，文件描述符表就不在内核中，存在于用户态*
* 文件对象是在内核态的，其内存只有内核能访问，*如果另外一个程序打开，也许是就不能被其他的打开了*  
*斜体部分由璟神脑补出来*
* 实际实现中，锁可以在更底层实现  
如文件系统
####文件锁的分类
* 记录锁
* 劝告锁  
检查、加锁由应用程序自己控制
* 强制锁  
检查、加锁由内核控制  
影响open()、read()、write()等操作
* 共享锁
* 排他锁
* 特殊类型  
共享模式强制锁、租借锁
####标志位
* mount -o mand /dev/sdb7 /mnt
* 超级块中的s_flags字段表示此分区是否可以加锁  
inode中标记文件是否允许被加锁
* MS_MANDLOCK标志位
####`fcntl`记录锁
int fcntl(int fd,int cmd, struct flock \*flock)
#####struct flock的结构
定义了锁的性质和特性

* short l\_type  
锁类型，包含F\_RDLCK（读锁）、F\_WRLCK（写锁）、F\_UNLCK（取消锁）
* short l\_whence  
起始位置
* off\_t l\_start  
偏移量
* off\_t l\_len  
加锁的字节数  
以上三个字段的含义与文件定位中的类似
* off\t l\_pid  
加锁的进程号
#####cmd参数
* F\_GETLK  
取得当前锁的情况
* F\_SETLK  
进行加锁或解锁  
如果无法加锁，立即返回，得到-1的返回值，错误代码为EAGAIN或EACCESS
* F\_SETLKW  
以等待的方式进行加锁或解锁  
如果无法加锁，将一直等待到成功加上锁为止
####其它封锁命令
#####`lockf`函数  
int lockf(int fd,int cmd,off\_t len)  

* 锁的层次比fcntl中的锁更高，效率可能有所降低  
* 此加锁命令与`fcntl`中的加锁命令尽量不要混用  
* cmd命令
	* F\_ULOCK  
	解锁
	* F\_LOCK  
	加强制锁
	* F\_TLOCK  
	加建议锁