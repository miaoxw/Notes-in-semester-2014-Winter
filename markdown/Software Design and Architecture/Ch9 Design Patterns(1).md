#Ch9 Design Patterns(1)
*——面向接口编程与OCP*
##一、设计模式
###1.什么是设计模式
* abstracts a recurring design structure
* comprises class and/or object
	* dependencies
	* structures
	* interactions
	* conventions
* distills design experience
###2.关键内容
* Pattern name:  
increases vocabulary of designers
* Problem  
intent, context, when to apply 
* Solution  
UML-like structure, abstract code
* Consequences  
results and tradeoffs
##二、针对接口编程
###1.迭代器模式
* 类型的定义包括数据、内存访问和功能（业务规则）  
良好的设计应当暴露功能，隐藏数据和内存访问
* 然而**序列**类型会产生问题
* 控制抽象和功能抽象的分裂  
一部分专做功能，一部分专做内存访问，做内存访问的部分就是**迭代器**
####基本目的
* 访问集合类型时，掩盖其存储结构  
有一种访问方式就有一种控制抽象
* 提供统一的访问接口
####模式特征
* 数据与控制紧密耦合  
此处不适用数据支撑功能的原则
* 迭代器部分没有任何数据
####注意事项
* 关键接口
* 动态性  
创建时的问题，在顺序图上才能体现出来

		Iterator Aggregate::createIterator()
		{
			return new Iterator(this);
		}
####效果
* 针对接口编程与信息隐藏
* 对聚合对象的差异化遍历
* 简化聚集类型的接口
* 一个聚合可以进行多个遍历
###2.代理模式
####基本目的
* 与迭代器模式不同，将控制抽象放大，获取功能访问的控制权
* 使中介透明
####模式特征
* 三者引用  
客户端对代理持有引用，代理对实际对象持有引用
* 访问控制
* 具有欺骗性  
这是要有相同接口的原因
####注意事项
* 访问时，要创建的对象是代理
* 要么代理自行创建真正对象，要么原先存在一个对象  
空代理是不允许的
* 通常以工厂模式封闭实体对象的构造
####效果
* 访问对象的间接层次  
从而可以在访问过程中进行一些处理
* 透明性
####虚拟代理与保护代理
* 针对消耗资源的行为产生，虚拟代理以池的方式固定地维持一定数目的资源以备后续访问
* 保护代理用于控制访问的权限
####远程代理
* 完全用于欺骗
* 解决网络访问等问题
* 如RMI
###3.原型模式
####基本目的
节省空间和CPU时间，创建新对象时，直接复制原有的原型对象
####模式特征
客户端在创建新对象时，直接调用原型类的方法克隆其自己

######注：
实际应用中，设计模式的使用会增加复杂性，可能会影响理解，因此只需要在可能需要变更的位置考虑使用相应的模式。
##三、OCP
###1.OCP的演变
* 增量开发
* Modify closed, increment easier
* OCP
###2.OCP原则
* 对扩展开放
* 对变更关闭
* 没有100%的OCP
###3.装饰者模式
####实现方式
* Decorator对Component做继承
* Decorator中聚合Component对象  
实际继承的是最终的ConcreteDecorator，只不过Component是他们的公共父类
* 代码实现  
差异性的解决最终由聚合实现

		Decorator::operation()
		{
			component.operation();			//是其内部私有成员变量的方法，不是父类方法
			self.somethingElse();
		}

####基本问题
* 解决继承在实现OCP时的继承树爆炸问题
* 保证接口的透明性  
因为没有放弃集成的机制
####效果
* 比继承更灵活
* 避免继承树的层次爆炸
* 容易产生大量相似的小对象  
难以理解、调试、测试
* 实际上违反LSP
####可修改性的技巧
#####延迟绑定
* 动态绑定  
多态，在方法调用时绑定
* 运行时注册
* 启动绑定  
以一个配置文件，在此规则下进行绑定  
也是表驱动的一种
* 加载时绑定