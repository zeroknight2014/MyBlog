---
layout: default
title: ant自动化部署总结
date: 2016-10-08 15:45:04
tags: [ant,构建部署,自动化]
description: <!--more-->
---

## ant介绍

### 什么是ant 
Apache Ant是一个基于Java的构建(Build)工具。理论上讲，类似Unix/Linux C程序员经常使用的Make工具。与Make相比，Ant完全由Java实现，具有跨平台的好处。

### ant诞生背景
Ant的称呼，颇有点意思。据Ant原作者James Duncan Davidson本人介绍，Ant是“Another Neat Tool”的缩写，意思是“另一个简洁的工具”，意思是比Make更简洁，适用。不过，人们更愿意接受Ant即“蚂蚁”这样的理解。众所周知，蚂蚁是动物世界赫赫有名的建筑师(ants do an extremely good job at building things);蚂蚁体格虽小，但力气超强(ants are very small and can carry a weight dozens of times their own)。


说起Ant，就不得不说另一个Apache开源项目Tomcat。Tomcat作为轻量级Web容器，早已声名鹊起。最初，Ant其实是Tomcat的一部分，Ant的唯一目的就是build Tomcat。Ant原作者James Duncan Davidson也是Tomcat的创始人。


不久，很多Java开源项目意识到Ant的简洁适用，更重要的是弥补Makefile的不足。自从Jakarta以及Apache项目开始采用Ant以来， 作为构建工具，Ant就像病毒一样，传播到各种各样的项目。
2000年1月，Ant脱离了Tomcat，成为独立的Apache开源项目，由独立的CVS模块维护，正式更名为Apache Ant。2000年7月，Apache Ant的第一个版本1.1正式亮相。至今，Apache Ant的最新版本为1.9.7。

### ant vs make
学习一门技术，要了解技术诞生的背景，还有它用来解决什么问题。ant诞生前，已有make工具在广泛运用，但是makefile的编写有不少缺点。来看看[stackoverflow](http://stackoverflow.com/questions/2209827/why-is-no-one-using-make-for-java)上是如何评价运用makefile来编译java的。总结起来就是几点：
1. 当有非常多的.java文件时，makefile的编写很蛋疼。
```make
javac Main.java
javac This.java
javac That.java
javac Other.java
or
javac Main.java This.java That.java Other.java
```
2. java的源文件组织是用目录来进行区分的，对于多级目录这种结构，make支持不好，而一般的c文件组织都是扁平的。
3. 这段没看懂，说的应该是编译时对于有改动过的文件进行重新编译时的处理策略不同，ant和maven处理上更优。直接上英文：Make also isn't very good at determining what files are out of date, at a collection level.With Ant, it will go through and sum up all of the files that are out of date, and then compile them in one go. Make will simply call the java compiler on each individual compiler. Having make NOT do this requires enough external tooling to really show that Make is not quite up to the task.

## ant安装
不多说，看[官方教程](http://ant.apache.org/manual/index.html)，需要先安装jdk

## 如何使用ant去编译部署一个项目
ant是工具，要描述如何去编译部署一个项目，通过一个xml配置文件去描述具体的流程。默认在项目根目录下，名字为build.xml。
编写好了后，直接`ant -f build.xml`或者`ant`就可以开始执行了。

## 如何编写build.xml

### build.xml结构
简单示例：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="project_name" default="default_task" basedir="./">
	<task name="default_task">
		...
	</task>
	...
</project>
```
build.xml中根节点为project，用于描述项目。每个build.xml只允许有一个project节点。project节点中可以定义多个目标target，表示不同的任务模块。而Target又是一些列Task的集合，而每一个Task是一段可执行的代码。

### xml元素描述
1. project元素，为xml的根节点
   ● name 属性：用于指定 project 元素的名称。 
   ● default 属性：用于指定 project 默认执行时所执行的 target 的名称。 
   ● basedir 属性：用于指定基路径的位置。该属性没有指定时，使用 Ant 的构件文件的附目录作为基准目录。

2. target元素
   target为ant的基本执行单元或是任务，它可以包含一个或多个具体的单元/任务。多个target 可以存在相互依赖关系。它有如下属性： 
   ● name 属性：指定 target 元素的名称，这个属性在一个 project 元素中是唯一的。我们可以通过指定 target 元素的名称来指定某个 target 。 
   ● depends 属性：用于描述 target 之间的依赖关系，若与多个 target 存在依赖关系时，需要以“,”间隔。 Ant 会依照 depends 属性中 target 出现的顺序依次执行每个 target ，被依赖的target 会先执行。 
   ● if 属性：用于验证指定的属性是存在，若不存在，所在 target 将不会被执行。 
   ● unless 属性：该属性的功能与 if 属性的功能正好相反，它也用于验证指定的属性是否存在，若不存在，所在 target 将会被执行。 
   ● description 属性：该属性是关于 target 功能的简短描述和说明。 

3. property元素
   property元素可看作参量或者参数的定义，project 的属性可以通过 property 元素来设定，也可在 Ant 之外设定。若要在外部引入某文件，例如 build.properties 文件，可以通过如下内容将其引： 
   `<property file="build.properties"/> `
   property 元素可用作 task 的属性值。在 task 中是通过将属性名放在${属性名}之间，并放在 task 属性值的位置来实现的。 
   Ant 提供了一些内置的属性，它能得到的系统属性的列表与 Java 文档中 System.getProperties() 方法得到的属性一致，这些系统属性可参考 sun 网站的说明。同时， Ant 还提供了一些它自己的内置属性，如下：
   
| Properties                  | Description |
| ------------- | ------------- |
| ant.file                    | The full location of the build file.     |
| ant.version                 | The version of the Apache Ant installation. |
| basedir                     | The basedir of the build, as specified in the basedirattribute of the project element. |
| ant.java.version            | The version of the JDK that is used by Ant. |
| ant.project.name            | The name of the project, as specified in the nameatrribute of the project element. |
| ant.project.default-target  | The default target of the current project. |
| ant.project.invoked-targets | Comma separated list of the targets that were invoked in the current project. |
| ant.core.lib                | The full location of the Ant jar file.   |
| ant.home                    | The home directory of Ant installation.  |
| ant.library.dir             | The home directory for Ant library files - typically ANT_HOME/lib folder. |


### 所有ant tasks
build.xml的编写是过程式，由于task是具体的可执行的代码，那么就需要了解如何编写task。
ant的所有tasks[在这里](http://ant.apache.org/manual/index.html)
分类有：
* Pre-process tasks：预处理相关task，有ANTLR，AntStructure，Import，Include，JavaCC，Javah，JJDoc，JJTree，Macrodef，Native2Ascii，Presetdef，Translate，XSLT，

* Compile Tasks：编译相关的task，有Depend，Javac，Apt，JspC，NetRexxC，Rmic，Wljspc，

* Documentation Tasks：文档生成相关task，有Javadoc/Javadoc2

* Property Tasks：属性设置相关的task，有Available，Basename，BuildNumber，Condition，Dirname，Echoproperties，LoadFile，LoadProperties，MakeURL，PathConvert，* Property，PropertyFile，Uptodate，Whichresource，XmlProperty，

* Archive Tasks ： 压缩相关task，有BUnzip2，BZip2，Cab，Ear，GUnzip，GZip，Jar，Jlink，Manifest，Rpm，SignJar，Tar，Unjar，Untar，Unwar，Unzip，War，Zip

* File Tasks：文件操作相关task，有Attrib，Checksum，Chgrp，Chmod，Chown，Concat，Copy，Copydir，Copyfile，Delete，Deltree，Filter，FixCRLF，Get，Mkdir，Move，* Patch，Rename，

* Java2 Extensions Tasks

* Execution Tasks：调度执行相关的task，有Ant，AntCall，Apply/ExecOn，Dependset，Exec，Java，Parallel，Sequential，Sleep，Subant，Waitfor，

* Deployment Tasks ：部署相关task，有ServerDeploy

* Testing Tasks：单元测试相关，有Junit、JunitReport

* Remote Tasks：远程控制相关task，有FTP，Rexec，Scp，setproxy，Sshexec，Telnet，

* Logging Tasks：日志输出监听相关的task，有Record

* SCM Tasks：版本管理相关task，有Cvs，CvsChangeLog，CVSPass，CvsTagDiff，ClearCase，Continuus/Synergy，Microsoft Visual SourceSafe，Pvcs，SourceOffSite，

* Miscellaneous Tasks：其他各种杂七杂八的task，有Defaultexcludes，Echo，Fail，GenKey，HostInfo，Input，Script，Sound，Splash，Sql，Taskdef，TStamp，Typedef，XmlValidate，

* Mail Tasks：：邮件相关task，有Mail、MimeMail

* Audit/Coverage Tasks：

* EJB Tasks：

  ​

需要用到什么功能，就到这里来查找是否有具体功能的task可以配置。

## 重要task详解
* taskdef：
  This task is a form of Typedef with the attributes "adapter" and "adaptto" set to the values "org.apache.tools.ant.TaskAdapter" and "org.apache.tools.ant.Task" respectively.
* property：
* ant/antcall：

## 流程控制
任何语言都有流程控制，ant也不例外。ant原生条件判断的task有：
condition、还有target中的if字段、还有ant-contrib中的扩展if/else等

## ant扩展
**ant-contrib**
当使用Ant编写一些较为复杂的逻辑功能，比如循环和流程判断时，自然希望 Ant 能支持这种编程能力。然而 Ant 核心任务中并没有提供 <if> 任务，只是在 <target> 任务的属性中支持 if 属性，比如 <target name="targetA" if="property-A-present"/>，即表示只有 property-A-present 属性存在才执行targetA 目标。但是，必须注意的一点是，这里的 if 并不是判断 module-A-present 属性是否设置为特定值，而仅仅是检查该属性是否被设置了，因而其可编程性并不是很强。 
Ant-contrib 为 Ant 提供了与通常所使用的编程语言功能相同的 <if> 、 <for>、<switch>等逻辑判断任务，支持对字符串的排序<sortlist>任务，甚至还支持常见的数学运算，如加、减、乘、除、求余等功能。在构建过程中灵活运用这个扩展包，将大大增强 Ant 的可编程性，这其实就是一种基于 XML 脚本的编程。

## ant 调试
听说eclipse可以进行ant调试，没用过，暂不介绍


## ant API
Ant的强大之处在于，不仅可以调用Ant脚本进行各种文件部署、管理工作，也可以调用Ant丰富的API，甚至扩展Ant API编程，定义自己的task。具体用法后续再了解。
