# android-ci
android持续集成

简要介绍：
android客户端接续集成(Continous Integration,简称CI)服务的主要目的是建立客户端程序的CI系统，帮助Dev提高代码质量和工程效率，提高持续交付的能力。
根据作用域的不同， CI系统支持两种触发机制：定时触发的CI和Change Record(CR)触发的CI。
定时CI：即每天定时触发CI服务。这种CI是较重的CI，包括编译打包/测试等一系列的动作。
trigger CI:  即每次CR时触发CI服务。这种CI是轻量级的CI(为了保证时效性)，包括代码检查/编译打包等。

服务构成：
客户端CI服务，包括的几个要素：
版本控制：git/gerrit
构建工具:  代码检查、编译打包服务，此处可以添加多种构建服务。
CI服务器:  不适用jenkins的gerrit trigger plugin,自实现gerrit trigger。
数据展示:  ci system/service。

User Case:
Demo 1: CR触发CI
用户提交CR-》gerrit trigger发现-》checkout patch-》代码检查服务 && 编译打包服务-》更新CR

Demo 2: 每天定时触发CI
每天定时-》pull代码-》编译打包服务-》测试服务-》展示结果

设计详情
![](https://github.com/smithallen/android-ci/blob/master/screenshots/ci-design.png)
a.gerrit trigger
    获取gerrit的 stream-event，筛选符合条件的event，放到redis的队列中。
b.web service
    项目创建及分支CI的操作。
    页面展示，分项目分别展示CR及定时CI的信息
c.调度编译打包
    从redis中获取gerrit event，触发Demo1中的任务流。
    定时触发各个项目的CI，Demo2中的任务流。
    将进展及结果写入mongodb
d.static code check
    使用checkstyle 进行增量代码检查
    使用findbugs 进行增量代码检查
    使用PMD 进行增量代码检查
    将comments添加到gerrit平台
    已在build.gradle加入了checkstyle/findbugs/pmd的任务，可以根据自身项目的需要， 决定具体的检查规则。
