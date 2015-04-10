#Ch2 Shell Programming
**Shell:*****A command interpreter and programming environment***

* 用户和操作系统之间的接口
* 作为核外程序而存在

##一、各种不同的Shell
* ash
* ash.static
* bsh
* bash
* sh
* csh
* tcsh
* ksh

##二、Shell的双重角色
* 命令解释程序
	* Linux的开机启动过程
	* Shell的工作步骤
		* 打印提示符
		* 得到命令行
		* 解析命令
		* 查找文件
		* 准备参数
		* 执行命令
* 独立的程序设计语言解释器
	* KISS  
	Keep it *simple and stupid*
	* 重定向与管道

##三、脚本
脚本能在命令行直接输入  
*但仅执行一次*
###1.编写脚本文件
* 注释  
`#This is comment`  
第一行的`#!/bin/bash`用于指示此脚本由bash执行
* 退出码  
`exit 0`
* `;`的作用
###2.执行脚本文件
* `sh script_file`
* `chmod +x script_file(chown,chgrp optionally)`  
`./script_file`
* `source script_file`  
或者`.script_file`

第三种方法与前两种有所不同，前两种新开一个shell执行脚本，而方法三在当前终端直接执行  
*如在配置环境变量时，前两种方法可能会发生配不进去的问题*
##四、用户环境
* .bash\_profile, .bash\_logout, .bashrc  
	* .bash_profile在用户登录时被读取，其中包含的命令被bash执行
	* .bashrc在启动一个shell时执行
	* .bash_logout在登录退出时读取执行
* alias/unalias  
给命令提供别名/取消别名，由shell记录
* 环境变量  
/etc/profile文件中也能配置环境变量，但由于它在系统启动时执行，这个文件对Linux的所有用户都会产生作用，并且修改此脚本后，只有重启才能产生效果
	* `export` command
	* `export`, `env` and `set` command  
	不使用`export`则设置用户变量
##五、变量
###1.用户变量
用户在shell脚本里定义的变量
####变量的赋值和使用
	var=value
	echo $var
#####read命令
`read var`或`read`

* 类似于`INPUT`语句的功能，将用户的输入赋值给变量
* 可以以-t设置超时
* 以-p设置提示内容
* 以-n指定接受字符数量
* 以-s用于输入密文
* **read不指定变量时存储到默认变量reply中**
####引号的用法
* **单引号**内的所有字符保持字符本身的意思  
包括转义符'\'
* 除了$, `, /外，双引号内的**所有字符保持本身含义**
* 没有任何引号时，shell尽最大可能解析
####转义符
还包括'\'和换行
###2.环境变量
shell环境提供的变量，通常以大写字母命名

* $HOME  
当前用户的登录目录
* $PATH
* $PS1  
命令行提示符，通常为'$'
* $PS2  
辅助提示符，提示后续输入，通常为'>'
* $IFS  
输入区分隔符，通常是空格、Tab或换行符
###3.参数变量和内部变量
调用脚本时如果带有参数，对应参数和额外产生的变量

> ######例：一个调用
> 	b.sh -i 30

* $#  
传递到脚本程序的参数个数  
上例为2
* $0  
脚本程序名  
上例为b.sh
* $1, $2, ...  
每个传入的参数  
-i与30
* $*  
一个由全体参数组成的清单，由$IFS分隔开  
上例为b.sh -i 30
* $@  
$\*的变体，不使用$IFS，返回参数列表  
上例与$\*没有区别，但直接将分隔符变成空格
##六、条件测试
###1.退出码
###2.test命令  
* `test expression`  
* 另一种写法：`[ expression ]`  
中括号内的空格不可省略，因为`[`本身就是一个命令  
*`[[`同样在条件测试的位置出现，它比`[`和`test`更强大，多了一些C/C++的逻辑语法，如&&和||*
####test命令支持的条件测试
* 字符串比较  
=两边尽量加空格，变量名尽量用双引号包围
	* str1=str2
	* str1!=str2
	* -z str  
	字符串为空则结果为真
	* -n str  
	字符串不为空则结果为真
* 算术比较  
	* exp1 -eq exp2  
	等于
	* exp1 -ne exp2  
	不等
	* exp1 -gt exp2  
	大于
	* exp1 -ge exp2  
	大于等于
	* exp1 -lt exp2  
	小于
	* exp1 -le exp2  
	小于等于
* 与文件有关的条件测试  
此处所有文件都可以带路径
	* -e file  
	文件存在则为真
	* -d file  
	是子目录则为真
	* -f file  
	是普通文件则为真
	* -s file  
	文件长度不为零则为真
	* -r file  
	文件则读则为真
	* -w file  
	文件可写则为真
	* -x file  
	文件可执行则为真
* 逻辑操作
	* !exp
	* exp1 -a exp2
	* exp1 -o exp2
###3.条件语句
####if语句
* 形式

		if [ expression ]
		then
			statements
		elif [ expression ]
		then
			statements
		elif ...
			...
		else
			statements
		fi
* 紧凑形式  
;在同一行上分割不同命令的分隔符  
在非if语句条件下也适用
####case语句
	case str in
		str1|str2) statements;;
		str2|str4) statements;;
		*) statements;;
	esac
每个执行的语句后面一定是双分号
##七、重复语句
它们都可以用break直接跳出循环
###1.for语句
	for var in list
	do
		statements
	done
适用于对一系列字符串做循环处理，有几个字符串就重复几次循环
###2.while语句
    while contidion
    do
    	statements
    done
###3.until语句
    until condition
    do
    	statements
    done
通常不建议使用until语句
###4.select语句
    select item in itemlist
    do
    	statements
    done
用于生成菜单列表

*select语句中的#?提示符也是可以改的，在$PS3中修改*
##八、命令表和语句块
###1.命令组合
* 分号串联  
命令顺次执行
* 条件组合
	* AND命令表  
	`statement&&statement&&...`  
	在前一个命令执行成功后才执行后一个命令
	* OR命令表  
	`statement||statement||...`  
	前一个命令若失败，执行后一个命令
###2.语句块
    {
    	statement1
    	statement2
    }
或

	{ statement1;statement2 }
* 在某些只接受一条命令的情况下提供多条命令  
* 单行书写时，大括号一定要用空格包围  
不加空格的大括号用在解析第10个及以后的参数时，如${10}  
*$()又不一样*
##九、函数
###1.形式
	function func()//括号内一定没有东西
	{
		statements
		return value
	}

其中function关键字可以省略
###2.局部变量
* 在函数体内直接定义变量，变量默认是全局的用户变量
* 因此需要使用`local`关键字  
`local a=5`
* 在函数体中，$1、$2等等变量会被参数所覆盖，但$0仍不会被覆盖
###3.函数调用
* `func arg1 arg2 ...`
* 使用双引号时，以引号内整体作为一个参数
###4.返回值
* `return`
* 如果没有显式地return，以最后一条执行语句的结果作为返回值  
通常执行成功返回0，执行失败时返回错误码
* 返回值可以以$?获取

> ######例：一个函数
> 见ppt
##十、其它
###1.杂项命令
* `break`
* `continue`
* `exit`
* `return`
* `export`  
将变量定义为环境变量
* `set`
* `unset`  
从环境中将变量删除  
这与将变量置为空的效果不同
* `trap`  
trap之后跟随一条命令，如：  
`trap 'echo something' INT`  
意为脚本在收到INT信号（`Ctrl+C`）时执行`echo something`
* `:`  
空命令，等同于`pass`
* `.`或`source`  
在当前shell中执行命令  
*对`:`的重定向输出可以用于建立一个新文件，或将其内容清空*
###2.捕获命令输出
* $(command)
* \`command\`

> ######例：
> * `echo $PWD`
> * `echo $(pwd)`
> 
> 虽然它们的结果相同，但执行机制完全不同
###3.算术扩展
* `$(())`  
可以获得算术表达式的结果，括号内是表示算术表达式的字符串  
**只能做整数运算，浮点数不被接受**
* `expr`命令也可用于进行计算，但一些命令需要转义  
`$(expr $x + 5)`
###4.参数扩展
> ######例：文件的批处理
> 见ppt

* ${param:-default}  
如果param为空，就把它设置为default的值
* ${#param}  
取param长度
* ${param%word}  
从尾部取起，删除与word匹配的最短部分
* ${param%%word}  
删除与word匹配的最长部分
* ${param#word}  
从头部取起，删除与word匹配的最短部分
* ${param##word}  
删除与word匹配的最长部分  

*以上四个命令都可以在word中继续使用通配符*
> ######例：将文件更换扩展名
> 	"${file%.*}".obj
###5.即时文档	
在脚本中向一条命令传送输入数据  
定义一个结束符，只有在捕获到结束符（串）时输入才被认为结束

> ######例：
> 	#!/bin/bash
> 	cat>>file.txt<<!CAT!INPUT!
> 	Hello, this is a here document.
> 	!CATINPUT! 