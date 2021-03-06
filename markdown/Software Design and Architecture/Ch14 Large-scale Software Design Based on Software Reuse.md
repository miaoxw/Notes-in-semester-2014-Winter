#Ch14 Large-scale Software Design Based on Software Reuse
*不会考*
##一、软件复用基础
###1.关于复用的实践
* 将来的代码只有15%左右是新代码  
理论上的复用率为85%
* 复用在实践中表现出了很好的效果  
提高了30%~35%的生产效率
* 现代软件开发没有不做复用的
###2.软件复用的定义
####机会主义复用
* 复用完全依赖于个人，知道多少，就复用多少  
与程序员个人的代码范围有关
* 复用的过程不可重复，不可复制
* 不是好的实践
####系统化复用
* 系统化服用带有强制性  
以组织为主，有一定的组织过程与规定，以复用价值为标准
* 复用基本的构建单位，在需求和体系结构上有相似性  
需求和体系结构上有相似性才是复用价值
* 在生产力、质量、业务表现等方面上取得一些好处
###3.复用的类型
####传统——代码复用
* ^C&^V
* 函数的复制
* 类库
* OO的开发
####趋势
* 基于构件的开发  
对详细设计的复用
* 框架的复用  
带有体系结构的复用
* 产品线  
到需求层次的复用
###4.简单的软件复用过程
* Create  
找到一些值得复用的部分，对其进行进一步优化
* Support  
将可复用部分分类存放
* Reuse  
复用与下一个产品
###5.复用的成本-收益分析
	软件成本=复用部分成本*复用比例+新开发成本*新开发比例
	
	两部分的比例之和为1
* 复用代码的成本包含将代码集成进来的成本，和开发复用部分的分摊成本  

		CFReuse=b+E/n
	* 要复用得有效益，b必须小于1
	* b值最乐观的估计值为0.08，在复用需求时产生
	* 如果只做代码复用，b值大约为0.85
	* 体系结构级别的复用，b值在0.2左右
	* E值大于1  
	要使其可复用，需要更大的成本
	* 分摊成本的E值通常为1.5
	* n低于4，复用通常没有效益
####结论
* 复用级别越高越好
* 复用比例越高越好
##二、构件模型与CBSE
详细设计级别的复用，将软件的小模块或某几个类集中起来，做成可复用的形式
###1.CBSE的动机
* 将软件各个部分拼装起来，实现拼装式生产
* 目前在局部领域是可以实现的
###2.基于构件的系统
	Application+Components+Glue/Addware

* “胶水”主要是适配器、包装器、委托器和Mediator
* Application是无法买到的构件，需要自己开发
* 目前的开发中，构件所占比例达到了80%
###3.构件
* 独立生产、独立购买、独立部署的单位
* 可以由第三方进行组装
* 是一个黑盒  
确保第三方不能修改此部分代码，通常提供说明书
###4.构件环境
* 各构件接口不同
* 胶水代码多了之后非常困难
* 由此提出通用构件模型
####通用构件模型
* 构件实现、文档化、部署的标准定义
* 明确了接口如何定义，以及各元素如何被包括在接口定义中
* 所有框架中都会自带一个构件模型  
这也是学习框架的很大时间消耗所在
###5.CBSE的流程
见ppt
###6.构件模型的设计
略
##三、框架
###1.框架
* 暗含着一个特定的体系结构和构件模型
* 可重用的应用程序或子系统部分
* 被表示为一组抽象类和这些类中对象的协作方式  
通常使用继承或反射机制
###2.框架与重用
* 体系结构
* 设计模式
* 构件
* 类库
###3.框架的部分
* 框架核心  
通常是必须的部分，一定会出现在程序中
* 框架的库
* 没有用过的库
* 加上一些自己写的代码，就成为应用程序
###4.通用化的处理方式
* 共性变成抽象类
* 差异部分提供参数化配置
* 参数化配置的部分通常很小
* 大量地使用继承和接口
###5.Hooks与Hot Spots
* Hooks
* Hot Spots
* Frozen Spots
###6.框架限定在特定的领域
* 框架只解决一个领域的一类问题
* 没有通用的框架
* 一个人在一个领域内达到能归纳出共性与差异性时，才可能写款监管i啊
##四、产品线
###1.产品线
* 一系列软件系统
* 有一些共同点  
具有复用价值
* 为了满足特定的任务或特定市场领域中的需求
* 构造一系列核心技术来创建新的软件
* 一次开发，多次销售
####共性
一批软件的相同特征

* 相同的体系结构
* 相同的业务领域
####核心资产
* 体系结构
* 构件
* 一系列开发工具
* 装配说明书  
如何选择构件，如何选择接口，如何组合
###2.产品线开发
见ppt