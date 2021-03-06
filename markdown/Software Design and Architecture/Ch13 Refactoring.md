#Ch13 Refactoring
##一、重构概述
###1.重构的原因
> * 产生自软件的演化，最初用来做软件的**维护**  
> * 重构由敏捷开发从维护手段转化到开发手段

* 一次完成正确设计很困难
* 对问题的理解不深刻
* 无法理解用户需求
* 实现未能预计到演化的需要
* 初始设计有缺陷
* 系统逐渐演变，越来越差
###2.重构的含义
有规律地改变软件系统，**改进内部结构**，但**不改变软件的外部行为**
####重构的动机
* 有一些现成的代码，但不够好
* 修改内部结构，保持外部行为

*问题：*

* 在演化时很容易，但开发阶段没有现成代码
* 怎样才算变得更好？
####重构的时机
* 增加了新的功能的代码
	* 不是增加新需求，而是添加完代码之后重构
	* 目的是使代码在未来更易维护
* 需要修改bug时
* 需要同行评审时
###3.怎样的代码需要重构
**Bad smells**  
代码中不好的地方，有些人以一些固定的重构方法与smells相关联
###4.重构的过程
简单设计+设计驱动

* 做一些小小的变化  
简单重构
* 通过所有测试，确保所有测试依然通过
* 如果通过，做下一项重构
* 如果不通过，解决问题或撤销重构  
这样系统仍然可运作
###5.重构的障碍
####复杂性
* 修改设计很困难
* 理解代码很困难  
质量不高的代码往往很难读懂
####可能引进错误
* 运行测试
* 构建测试
* 先清理，后增加功能
####文化因素
* *“报酬用于增加新功能而不是改进代码”*
* *“只要系统没有崩溃，就不要修复”*
* *“自己的软件，里面有什么问题我知道”*
##二、Bad smells
###1.参考书目
**Refactoring: Improving the Design of Existing Code**  
by Martin Fowler (et al.),1999, Addison-Wesley

* 22种坏味道
* 72种办法
###2.具体的Bad smells
####重复代码
* 同一个类中，两个方法的代码出现了相同代码  
解决方法：抽取方法
* 两个相关类中的相同代码  
解决方法：方法上拉到父类、构建模版方法
* 两个无关类中的相同代码  
解决方法：重复度特别高的抽取父类、抽取方法由一个类调用另一个、将方法替换为方法对象
####太长的方法
* 试图做太多的事情，或是很难做出统一的抽象或边界定义  
总之内聚度太差  
* 通常200行以上的代码*可能*太长  
* 解决方法：抽取方法，将方法分解为多个方法
####太大的类
* 类中的部分和方法太多
* 超过50个方法或属性*可能*太大
* 抽取类或抽取子类
* **例外**  
类库中的类大一点没关系，因为这些类通常不会修改  
自己的类，如果修改的范围可控，也没什么关系
####分散的修改
* 一个类因为多种原因、多种方式被修改  
一个类的秘密太多，且不相关
* 在演化的程序中容易出现
* 低内聚的表现
* 解决方法：拆成多个类或抽取方法
####“霰弹枪”
* 在一些逻辑上相同  
需要变更时产生大量的简单修改
* 一个秘密被分散到了多个地方  
因而要将秘密重新收回
* 解决方法：移动方法或域
####数据类
* 一些类只有属性和get、set方法  
持有大量数据，但不做操作  
最后变成结构化程序设计的风格
* 未必有问题
* 解决方法：封装除去某些不需要的方法，尤其是集合属性、将行为移动到数据类中
####太长的参数列表
* 印记耦合与可读性的取舍
* 方法可能太长
* 解决方法：将方法分解或转而传递整个对象作为参数
####特征嫉妒
* 方法不访问所在类的属性，而访问其它类的
* 方法和行为不集中
* 解决方法：抽取方法，将其转移到数据所在类
* *例外*  
迭代器、访问者、策略模式
####数据团
* 一些数据自觉不自觉地同时出现  
参数中、代码中都会出现
* 考虑数据团是否存在很高耦合，需要封装
* 解决方法：抽取方法、封装对象
####原始类型偏执
* 大量使用原始类型而不是对象
* 解决方法：创建一些小类  
在可修改性上有好处，但可读性会显著降低
####switch选择结构
* 大段的switch
* 解决方法：以多态解决判断问题，用子类代替类型码，有必要时引入空对象
####平行继承树
* 多种差异性采用继承的方法解决会产生排列组合的影响
* 解决方法：移动方法和域，最好移除整个继承结构，最好干脆使用聚合替代
####Lazy class
* 多次重构后，一个类不再承担职责
* 解决方法：去除这个类
####过分的通用性
* 不要给未来的变化留太多东西  
只是敏捷的方法学
* 解决方法：全部去掉
####Temporary field
* 解决方法：引入类似空对象的类
####消息链
* 对象的级联调用
* 解决方法：使用委托或临时变量
####Middle man
* 过多的indirection
* 解决方法：去除middle man，将委托替换为继承
* 是否插入中介，与实际使用场景有关，需要具体判断
####过于亲密
* 类与类之间的关系过于密切  
内聚上可能出了问题，一个类被强行分割
* 解决方法：设法将其合并，或将双向关系变成单向的
####带有不同接口的候选类
* 两个看似不相干的对象其实是一个东西
* 解决方法：抽象出超类，或更名
####不完整类库
* 使用类库时，类库没有提供某些方法  
导致加工后的代码到处出现
* 解决方法：在本地对类进行继承
####拒绝馈赠
* 一个类继承父类，但父类的某些方法不需使用
* 违反了LSP
* 解决方法：重新抽象，方法下推或将继承转换为委托
####注释
* 注释太多也不好  
极限编程的方法论：*代码是最好的注释？*
###3.IDE对重构的支持
* Eclipse
* Visual Studio