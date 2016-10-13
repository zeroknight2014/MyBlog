---
title: maven学习总结
date: 2016-10-10 20:20:31
tags: [maven, 部署, 构建]
keywords: maven 部署
description: <!--more-->
---

## Maven简介

### Maven是什么？
一般认为Maven是一个‘构建工具’：一个将源代码构建成可发布的构件的工具。但是也可以说它是一个项目管理工具。区别是：类似Ant这样的工具仅仅关注预处理、编译、打包、测试、分发，而像Maven不仅包括这些，生成报告，生成web站点等。

更正式的 Apache Maven 1 的定义： Maven是一个项目管理工具，它包含了一个项目对象模型 (Project Object Model)，一组标准集合，一个项目生命周期(Project Lifecycle)，一个依赖管理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中插件(plugin)目标(goal)的逻辑。 


### Maven的原则
**约定优于配置**
例如：
> src/main/java下放源码
> src/main/resources下放资源文件
> src/test下放测试代码
> target目录下放输出文件

当然，如果你不喜欢也可以自己配置目录。Ant就是需要自己配置一个个的目录，而Maven则设置一个默认值，所以如果按照它的默认值来组织你的项目，可以节省不少配置功夫。

### Maven的插件
Maven 的核心其实不做什么实际的事情，除了解析一些 XML 文档，管理生命周期与插件之外，它什么也不懂。Maven 被设计成将主要的职责委派给一组 Maven 插件，这些插件可以影响 Maven 生命周期，提供对目标的访问。
个人觉得插件产生的初衷应该是为了封装构建部署过程中的一些通用行为，并提供配置给人进行动态配置运用，以实现在pom.xml中简介的去描述项目。看起来好像是简单了不少，但是实际上，当你想要去实现某个功能，你要去先找Maven有没有这种插件，有的话需要查看这个插件的具体用法，没有的话就蛋疼了，难道得自己写一个？不过这种概率应该很小，但是找插件学用法也是件很麻烦的事情。很可能还不如直接用python脚本来实现它！当然如果你很熟悉python的话！


### Maven的特点
Maven和Ant一样，都是使用xml来描述构建流程的具体操作行为，然后两者的思想却完全不一样。
Ant的build.xml脚本的编写是过程式的，可以理解为一个基于xml格式的语法的语言，但是这种脚本的编写，所有的功能都是封装在task中的，build.xml中相当于是给每一个task配置对应的参数。
Maven的pom.xml编写则是连这种过程式的展现都给去掉了，取而代之的是，Maven中设计了一套生命周期的概念，而在pom.xml中，需要做的就是配置你要用到的插件在哪个生命周期运行及运行参数等。所以Maven的pom.xml看起来就像是描述生成最终目标需要的配置，至于目标生成的逻辑流程就需要更深层次的分析xml，乍看之下难以直观看到。或许这就是pom项目对象模型这个命名的原因。


### Maven的项目对象模型
Maven 维护了一个项目的模型，你不仅仅需要把源码编译成字节码，你还需要开发软件项目的描述信息，为项目指定一组唯一的坐标。你要描述项目的的属性。项目的许可证是什么？谁开发这个项目，为这个项目做贡献？这个项目依赖于其它什么项目没有？Maven不仅仅是一个“构建工具”，它不仅仅是在类似于 make 和 Ant 的工具的基础上的改进，它是包含了一组关于软件项目和软件开发的语义规则的平台。
> **每一个项目模型都包含以下特征：**
> 依赖管理
> 远程仓库
> 全局性构建逻辑重用
> 工具可移植、集成
> 便于搜索和过滤构件
> 总之，Maven的产生 为软件项目的语义一致性描述的开端提供了一个基础。

### Maven与Ant
两者设计的实现目标不一样。Maven是用来描述项目，不仅仅是项目的构建过程。而Ant的初衷就是实现构建过程。甚至，你可以在Maven中使用以Ant的方式来实现你的构建过程。个人觉得，Ant在构建过程上明显强于Maven，使用Maven的插件来实现构建过程实在是有点繁琐、复杂！

## Maven安装和配置
[官网安装教程](http://maven.apache.org/install.html)

### Maven配置
在Maven安装目录下的conf目录下有：
* settings.xml：该文件应用于所有用户，包含了Maven相关的认证，仓库和其它信息的配置，用来自定义Maven的行为。

### 用户配置和仓库
在 ~/.m2 目录下有：
* settings.xml：该文件包含了用户相关的认证，仓库和其它信息的配置，用来自定义Maven的行为。
* repository：该目录是你本地的仓库。当你从远程Maven仓库下载依赖的时候，Maven在你本地
  仓库存储了这个依赖的一个副本。

### Maven插件的下载逻辑
本地仓库——共享仓库、远程仓库——Maven中央仓库


## Maven使用
### 创建一个简单的maven项目
命令行创建一个简单的maven项目
` mvn archetype:generate -DgroupId=com.test.hellomaven -DartifactId=hello  -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`
* groupId：团体，公司，小组，组织，项目，或者其它团体。团体标识的约定是，它以创建这个项目的组织名称的逆向域名(reverse domain name)开头。
* artifactId：在 groupId 下的表示一个单独项目的唯一标识符。
* version：项目版本号。
* packaging：项目的类型，默认是 jar ，描述了项目打包后的输出。类型为 jar 的项目产生一个JAR文件，类型为 war 的项目产生一个web应用。


### pom.xml简介
上述命令执行后会生成一个目录hello，并在hello目录下有一个pom.xml，具体内容如下：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.test.hellomaven</groupId>
  <artifactId>hello</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>hello</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```


### Maven的生命周期
生命周期是包含在一个项目构建中的一系列有序的阶段。插件目标可以附着在生命周期阶段上。随着Maven沿着生命周期的阶段移动，它会执行附着在特定阶段上的目标。Maven可以支持许多不同的生命周期
Maven中有三种标准的生命周期：清理（clean），默认（default）(有时候也称为构建)，和站点（site）。
1. 清理生命周期 (clean)：
> pre-clean，clean，post-clean三个阶段

2. 默认生命周期 (default)

| 名称                      | 描述                                       |
| ----------------------- | ---------------------------------------- |
| validate                | 验证项目是否正确，以及所有为了完整构建必要的信息是否可用             |
| generate-sources        | 生成所有需要包含在编译过程中的源代码                       |
| process-sources         | 处理源代码，比如过滤一些值                            |
| generate-resources      | 生成所有需要包含在打包过程中的资源文件                      |
| process-resources       | 复制并处理资源文件至目标目录，准备打包                      |
| compile                 | 编译项目的源代码                                 |
| process-classes         | 后处理编译生成的文件，例如对Java类进行字节码增强               |
| generate-test-sources   | 生成所有包含在测试编译过程中的测试源码                      |
| process-test-sources    | 处理测试源码，比如过滤一些值                           |
| generate-test-resources | 生成测试需要的资源文件                              |
| process-test-resources  | 复制并处理测试资源文件至测试目标目录                       |
| test-compile            | 编译测试源码至测试目标目录                            |
| test                    | 使用合适的单元测试框架运行测试。这些测试应该不需要代码被打包或发布        |
| prepare-package         | 在真正的打包之前，执行一些准备打包必要的操作。这通常会产生一个包的展开的处理过的版本 |
| package                 | 将编译好的代码打包成可分发的格式，如JAR，WAR，或者EAR          |
| pre-integration-test    | 执行一些在集成测试运行之前需要的动作。如建立集成测试需要的环境          |
| integration-test        | 如果有必要的话，处理包并发布至集成测试可以运行的环境               |
| post-integration-test   | 执行一些在集成测试运行之后需要的动作。如清理集成测试环境。            |
| verify                  | 执行所有检查，验证包是有效的，符合质量规范                    |
| install                 | 安装包至本地仓库，以备本地的其它项目作为依赖使用                 |
| deploy                  | 复制最终的包至远程仓库，共享给其它开发人员和项目（通常和一次正式的发布相关）   |

3. 站点生命周期 (site)
> pre-site
> site
> post-site
> site-deploy


大部分生命周期将 resources:resources 目标绑定到 process-resources 阶段。 process-
resources 阶段处理资源并将资源复制到输出目录。

### 定制pom.xml
1. 添加组织、法律和开发人员信息
```xml
<project ...
  <licenses>
      <license>
          <name>Apache 2</name>
          <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
          <distribution>repo</distribution>
          <comments>A business-friendly OSS license</comments>
      </license>
  </licenses>
  <organization>
      <name>company_name</name>
      <url>company_url</url>
  </organization>
  <developers>
      <developer>
          <id>1</id>
          <name>zero</name>
          <email>his_email</email>
          <url>company_url</url>
          <organization>company_name</organization>
          <organizationUrl>company_url</organizationUrl>
          <roles>
              <role>developer</role>
          </roles>
          <timezone>-6</timezone>
      </developer>
  </developers>
...
</project>
```

2. 添加新的依赖
```xml
<project>
[...]
	<dependencies>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.14</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>
	</dependencies>
[...]
</project>
```

### 给项目添加资源

### 编写单元测试
添加一个测试范围依赖
添加单元测试资源
执行单元测试
忽略测试失败
跳过单元测试

### 配置web应用


## 多模块项目

### 项目关系

### 项目继承
• 定义符（ groupId 和 artifactId 中至少有一个必须被覆盖）
• 依赖
• 开发者和贡献者
• 插件列表
• 报告列表
• 插件执行 （id匹配的执行会被合并）
• 插件配置

### 优化和重构POM
1. POM清理，分析重复插件和依赖
2. 优化依赖
3. 优化插件
4. Maven Dependency插件




## 项目对象模型POM
POM包含了四类描述和配置：
1. 项目总体信息
2. 构建设置
3. 构建环境
4. POM关系

### 超级POM
所有的Maven项目的POM都扩展自超级POM。超级POM定义了一组被所有项目共享的默认设置。它是Maven安装的一部分，可以在lib 中的 maven-2.0.9-uber.jar 文件中找到


这个超级POM定义了一些由所有项目继承的标准配置变量。对这些变量的简单解释如下：
* 默认的超级POM定义了一个单独的远程Maven仓库，ID为 central 。这是所有Maven客户端默认配置访问的中央Maven仓库。该配置可以通过一个自定义的 settings.xml 文件来覆盖。注意这个默认的超级POM关闭了从中央Maven仓库下载snapshot构件的功能。如果你需要使用一个snapshot仓库，你就要在你的 pom.xml 或者 settings.xml 中自定义仓库设置。

* 中央Maven仓库同时也包含Maven插件。默认的插件仓库就是这个中央仓库。Snapshot被关闭了，而且更新策略被设置成了“从不”，这意味着Maven将永远不会自动更新一个插件，即使新版本的插件发布了。

* build 元素设置Maven标准目录布局中那些目录的默认值。
* 从Maven 2.0.9开始，超级POM为核心插件提供了默认版本。

### 项目依赖
1. 依赖范围
> compile（编译范围）、provided（已提供范围）、runtime（运行时范围）、test（测试范围）、system（系统范围）

2. 可选依赖
3. 依赖版本界限
> (, ) 不包含量词，开区间；[, ]包含量词，闭区间；逗号前后的版本都非必须

4. 传递性依赖
> 传递性依赖和范围、冲突解决、依赖管理

5. 项目关系
> 坐标：groupId、artifactId、version
> **Maven中央仓库会根据以上字段来作为项目的唯一标识！**
> 还有一个classifier，很少用到

6. 依赖归类

### Profile
通过profiles可以合理进行pom.xml中的功能分离，如果有必要的话

## Maven属性
1. Maven提供了三个隐式的变量，可以用来访问环境变量，POM信息，和Maven Settings：
> * evn.*：env.PATH、env.HOME、env.JAVA_HOME、env.M2_HOME
> * project.*：project 变量暴露了POM。你可以使用点标记（.）的路径来引用POM元素的值。
> * settings.*：settings 变量暴露了Maven settings信息。可以使用点标记（.）的路径来引用 settings.xml 文件中元素的值。

2. Java系统属性
   Maven暴露所有来自于 java.lang.System 的属性。任何你能从 System.getProperty() 获取
   的属性都能以Maven属性的形式引用。

3. 用户定义的属性
> 可以在 pom.xml 或者 Profile 中定义自己需要的属性，引用方法为${xxx}
> 例1，在pom.xml中定义
```xml
<project>
	...
	<properties>
		<arbitrary.property.a>This is some text</arbitrary.property.a>
		<hibernate.version>3.3.0.ga</hibernate.version>
	</properties>
	...
	<dependencies>
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate</artifactId>
			<version>${hibernate.version}</version>
		</dependency>
	</dependencies>
	...
</project>
```

例2，在Profile中定义
```xml
<project>
	...
	<profiles>
		<profile>
			<id>some-profile</id>
			<properties>
				<arbitrary.property>This is some text</arbitrary.property>
			</properties>
		</profile>
	</profiles>
	...
</project>
```

## settings.xml配置详解

### 顶层元素
```xml
<localRepository>~/workspace/.m2/repository</localRepository>
<interactiveMode>true</interactiveMode>
<usePluginRegistry>false</usePluginRegistry>
<offline>false</offline>
<pluginGroups>
	<pluginGroup>org.codehaus.mojo</pluginGroup>
</pluginGroups>
```
localRepository：该值表示构建系统本地仓库的路径。
interactiveMode：如果Maven需要和用户交互以获得输入，则设置成 true ，反之则应为 false 。默认为 true 。
usePluginRegistry：如果需要让Maven使用本地文件workspace/.m2/plugin-registry.xml 来管理插件版本，则设为 true 。默认为 false 。
offline：如果构建系统需要在离线模式下运行，则为 true ，默认为 false 。
pluginGroups：该元素包含一个 pluginGroup 元素列表，每个子元素包含了一个 groupId 。

### 服务器（Servers）
POM中的 distributionManagement 元素定义了部署的仓库。然而，一些设置如安全证书不应该和 pom.xml 一起分发。这种类型的信息应该存在于构建服务器上的 settings.xml 文件中。
```xml
<servers>
  <server>
  <id>server001</id>
  <username>my_login</username>
  <password>my_password</password>
  <privateKey>${usr.home}/.ssh/id_dsa</privateKey>
  <passphrase>some_passphrase</passphrase>
  <filePermissions>664</filePermissions>
  <directoryPermissions>775</directoryPermissions>
  <configuration></configuration>
  </server>
</servers>
```
> id：这是server的 id 
> username, password：这对元素表示服务器认证所需要的登录名和密码。
> privateKey, passphrase：和前两个元素类似，这一对元素指定了一个私钥的路径（默认是 ~/.ssh/id_dsa ）以及如果需要的话，一个密语。
> filePermissions, directoryPermissions：如果在部署的时候会创建一个仓库文件或者目录，这时候就可以使用权限（permission）。这两个元素合法的值是一个三位数字，其对应了*nix文件系统的权限，如664，或者775。

### 镜像（Mirrors）
settings.xml中的Mirror配置
```xml
<mirrors>
  <mirror>
  <id>planetmirror.com</id>
  <name>PlanetMirror Australia</name>
  <url>http://downloads.planetmirror.com/pub/maven2</url>
  <mirrorOf>central</mirrorOf>
  </mirror>
</mirrors>
```
> id, name：该镜像的唯一定义符。id用来区分不同的mirror元素。
> url：该镜像的URL。构建系统会优先考虑使用该URL，而非使用默认的服务器URL。
> mirrorOf：被镜像的服务器的id。例如，如果我们要设置了一个Maven中央仓库（http://repo1.maven.org/maven2）的镜像，就需要将该元素设置成central。这必须和中央仓库的id central完全一致。

### 代理（Proxies）
settings.xml中的代理配置
```xml
<proxies>
  <proxy>
  <id>myproxy</id>
  <active>true</active>
  <protocol>http</protocol>
  <host>proxy.somewhere.com</host>
  <port>8080</port>
  <username>proxyuser</username>
  <password>somepassword</password>
  <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>
  </proxy>
</proxies>
```
> id：该代理的唯一定义符，用来区分不同的proxy元素。
> active：true 则激活代理。当我们声明了一组代理，而某个时候只需要激活一个代理的时
> 候，该元素就可以派上用处。
> protocol, host, port：该代理的 protocol://host:port ，（协议://主机名:端口），分隔成离散的元素以方便配置。
> username, password：这一对元素表示代理服务器认证的登录名和密码。
> nonProxyHosts：这里定义一个不该被代理的主机名列表。该列表的分隔符由代理服务器指定；上述的例子中使用了竖线分隔符，使用逗号分隔也很常见。

### 仓库（Repositories）
settings.xml中的仓库配置
```
<profiles>
  <profile>
  ...
  <repositories>
    <repository>
    <id>codehausSnapshots</id>
    <name>Codehaus Snapshots</name>
    <releases>
    <enabled>false</enabled>
    <updatePolicy>always</updatePolicy>
    <checksumPolicy>warn</checksumPolicy>
    </releases>
    <snapshots>
    <enabled>true</enabled>
    <updatePolicy>never</updatePolicy>
    <checksumPolicy>fail</checksumPolicy>
    </snapshots>
    <url>http://snapshots.maven.codehaus.org/maven2</url>
    <layout>default</layout>
    </repository>
  </repositories>
  <pluginRepositories>
  ...
  </pluginRepositories>
  ...
  </profile>
</profiles>
```
> releases, snapshots：这里配置了两种构件，发布版（Release）和快照版（Snapshot）的策略。
> enabled：true 或者 false 表示该仓库是否为某种类型构件（发布版或者快照版）开启。
> updatePolicy：该元素指定更新发生的频率。选项是：always（一直），daily（默认，每日），interval：X（这里X是以分钟为单位的时间间隔），或者never（从不）。
> checksumPolicy：当Maven将构件部署到仓库中时，它也会部署对应的校验和文件。选项有ignore（忽略），fail（失败），或者warn（警告）。
> layout：在上面的仓库描述中，它们都遵循一个共同的布局。我们可以使用该元素指定布局是default（默认）还是legacy（遗留）。

### Profile
在settings.xml中设置激活profile
```xml
<settings ...>
  <activeProfiles>
  <activeProfile>env-test</activeProfile>
</activeProfiles>
</settings>
```



### Maven的pom.xml中所有配置节点详解
看这里[Maven中pom.xml配置节点详解]()



。。。
。。。
。。。
未完待续
Maven的概念和运用太多了，以后慢慢学习