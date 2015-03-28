#Ch7 Software Architectures Evaluation
*(Analysis)*
*了解即可，不作考核，未来也不一定会用到*
##一、基本的设计评估
###1.测试
主要体现在系统测试上
###2.评审
####Checklist方法
有人在用，但效果并不好，因为设计方案过于抽象
####场景方法
###3.度量
##二、ATAM的步骤
###1.Phase 1
自我评价

* Present the ATAM  
解释评估的原因和技术关键点
* Present business drivers  
寻找哪些要素会影响体系结构，包括业务背景、高层功能需求、质量属性，尤其是**关键需求**（主要是质量）
* Present architecture  
由架构师解释体系结构，向评估人员告知体系结构为什么是这样的，同时完成下一个步骤  
架构师使用哪些风格来满足相应的关注点  
*不妨认为是体系结构决策的评估*
* Identify architectural approaches  
将体系结构的决策固定下来  
*前四个步骤在当今体系结构设计环境中实际上可以忽略*
* Generate quality attribute utility tree  
Utility tree将非功能性需求变成场景，逐层分解，可以认为就是场景集合  
场景代表了利益攸关者的需要
* Analyze architectural approaches  
将场景与决策一一比较，判定风险、折衷和敏感点  
对每个决策检查风险、折衷和敏感点，再由架构师自行判断是否继续使用此体系结构  

评估实际到此结束，风险、折衷、敏感点的列表已经可以交给架构师自行审视
###2.Phase 2
由利益攸关者再次评估

* Brainstorm and prioritize scenarios  
可以向用户提供一些头脑风暴的引导
* Analyze architectural approaches  
将头脑风暴新增的场景与之前的合并
* Present results  
除了场景和决策等1~4步的产物，还要包含风险、敏感点和折衷