#Ch12 Design Patterns(4)
##一、递归数据类型
###1.Composite
####目的
* Compose objects into tree structures to represent part-whole hierarchies  
未必是树状结构，只要是递归结构，就都能使用
* Composite lets clients treat individual objects and compositions of objects in a uniform way
####结构
* 子结点中一部分递归，一部分不递归
* 有侧重**透明性**与**安全性**的两种结构
* 将各个具体的Decorator去除，Composite与装饰者模式就很接近
###2.Interpreter
####目的
定义一个语言的语法，符合语法树的规则
####动态结构
* Context提供一个操作环境
* 本质与Composite没有区别
* 同样是调用持有的属性的interpret方法  
父节点的interpret方法只可能在TerminalExpression中被使用
####参与者
* Client
* Context
* Expression
##二、数据类型
###1.空对象——不确定委托
####目的
避免直接使用`null`来表达不存在的对象

* 减少对空值的测试
* 实现默认行为
####解决方案
* 在源类下继承普通类与空类
* 空类实现默认行为，同时也具备演化的能力
###2.不可变模式
####解决方案
* 只在构造函数中对值修改
* 确保所有方法没有副作用
* 如果需要返回一个修改的值，返回一个新的实例
* 更极端的情况可以考虑单例模式
###3.只读模式
###4.实体
代表一个持久化的业务对象
###5.VO
* 完全模拟了网络包  
未必只是一个对象
* 包含create和update两种操作
* VO由实体创建
##三、对象的创建问题
###1.简单的对象创建
* 规则类型的创建与撤销语言可以轻易地解决，但面对复杂类型，语言的机制本身无能为力
* 构造函数就是语言提供的创建对象的接口
####构造函数的约定
* 能创建的实例数量不受限制
* 一次只能创建一个类型
* 实例化与初始化相对简单
####方法
* 创建模式
* 耦合模式
* 内聚模式
###2.复杂的对象创建
构造函数无法达成问题时，就要自行提供新的构造
####基本思路
* 消灭语言的构造函数  
将其变为private
* 自定义新的构造函数  
只有定义为static的，才有可能被得到调用
* 在构造函数的实现上满足各种条件
####一些对象只允许创建一个实例  
单例模式
####限制对象创建的数量
在单例模式基础上修改
####创建的对象类型有差异
* 由于new无法多态，因此策略模式无法在这里使用
* 语言没有多类型的构造函数  
因而自定义一个多类型的构造函数单独存放  
即为**工厂模式**
#####解决方法
* 将构造的职责委托给真正的对象进行
* 将构造方法外置
#####目的
使实例化延迟到子类中进行
#####注意事项
* Factory Method实际传递factory message
* 创建对象方法的对象中还包含大量其它功能
#####抽象工厂模式
######目的
提供一个创建有差异性对象的对象
######解决方式
* 要构造一个新的系列，就增加一个新的方法
* AbstractFactory  
Declares an interface for operations that create abstract products
* ConcreteFactory  
Implements the operations to create concrete product objects: usually instantiated as a Singleton
* AbstractProduct  
Declares an interface for a type of product object; Concrete Factories produce the concrete products
* ConcreteProduct  
Defines a product object to be created by the corresponding concrete factory
####极其复杂的实例化与初始化
* 如运行时实例化与大量不同值的初始化  
* 完全从头开始代价非常高

使用**原型模式**
##四、设计模式总结
###1.目标
* 生产性语言  
模仿建筑的永恒之道，以各种设计模式拼凑出整个建筑物来
* 在计算机领域中完全达不到生产性语言的程度
###2.实际目的
* 弥补载体缺陷  
由于四人帮的设计模式从Smalltalk的研究中产生，语言的缺陷自然被发现
* 提高质量
* 解决特定问题
###3.不要过度使用设计模式
* 一些设计模式违反了设计原则  
这是受语言限制产生的问题
* 设计模式的使用会产生代价
###4.注意设计模式的代码细节