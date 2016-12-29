---
title: Maven学习总结2
date: 2016-10-13 18:33:22
tags: maven
keywords: maven
description: <!--more-->

---

## profile声明
profile可以声明在以下这三个文件中：
pom.xml：很显然，这里声明的profile只对当前项目有效
用户settings.xml：.m2/settings.xml中的profile对该用户的Maven项目有效
全局settings.xml：conf/settings.xml，对本机上所有Maven项目有效

1. 在项目对应的pom.xml中配置插件的下载仓库
   pom.xml中：
```xml
<repositories>  
    <repository>  
        <id>central.maven.com</id>  
        <name>mapbar central mirror.</name>  
        <url>http://192.168.1.252:8081/nexus/content/repositories/central/</url>  
    </repository>  
    <repository>  
        <id>3rd.mapbar.com</id>  
        <name>mapbar thirdparty central mirror.</name>  
        <url>http://192.168.1.252:8081/nexus/content/repositories/thirdparty/</url>  
    </repository>  
    <repository>  
        <id>public.mapbar.com</id>  
        <name>mapbar tech API maven mirror.</name>  
        <url>http://192.168.1.252:8081/nexus/content/groups/public/</url>  
    </repository>  
    <repository>  
        <id>releases.mapbar.com</id>  
        <name>mapbar thirdparty central mirror.</name>  
        <url>http://192.168.1.252:8081/nexus/content/repositories/releases/</url>  
    </repository>  
</repositories>
```
此种方式只针对本项目有效，其他maven项目无效

2. 对所有maven项目都生效的配置方法：
   settings.xml中：
```xml
   <profiles>  
    <profile>  
        <id>nexus</id>  
        <repositories>  
            <repository>  
                <id>nexus</id>  
                <name>Nexus</name>  
                <url>http://192.168.53.55:8081/nexus/content/groups/public/</url>  
                <releases>  
                    <enabled>true</enabled>  
                </releases>  
                <snapshots>  
                    <enabled>true</enabled>  
                </snapshots>  
            </repository>  
        </repositories>  
        <pluginRepositories>  
            <pluginRepository>  
                <id>nexus</id>  
                <name>Nexus</name>  
                <url>http://192.168.53.55:8081/nexus/content/groups/public/</url>  
                <releases>  
                    <enabled>true</enabled>  
                </releases>  
                <snapshots>  
                    <enabled>true</enabled>  
                </snapshots>  
            </pluginRepository>  
        </pluginRepositories>  
    </profile>  
   </profiles>  
   <activeProfiles>  
    <activeProfile>nexus</activeProfile>  
   </activeProfiles>  
```

## 构建发布仓库配置
pom.xml中如下配置
```xml
<distributionManagement>  
      <repository>  
        <id>releases</id>  
        <url>http://localhost:8081/nexus/content/repositories/thirdparty/</url>  
      </repository>  
</distributionManagement> 
```
在mvn deploy时，则构件自动发布到指定仓库

需要注意：
settings.xml文件必须配置servers，其中id必须和repository下的id一致
```xml
<servers>  
	<server>  
		<id>releases</id>  
		<username>admin</username>  
		<password>admin123</password>  
	</server>  
</servers>  
```

## maven中使用key=value形式的变量声明
maven中如何引用其他properties文件中key=value形式的变量？
使用插件maven-properties-plugin
方法如下：
```xml
<project>
  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>maven-properties-plugin</artifactId>
        <version>1.0-SNAPSHOT</version>
        <executions>
          <execution>
            <phase>validate</phase>
            <goals>
              <goal>read-project-properties</goal>
            </goals>
            <configuration>
              <files>
                <file>etc/config/dev.properties</file>
              </files>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project>
```

或者
如果需要引用外部propertiest文件中的属性值，需要在pom.xml中添加对外部文件的引用。例如，创建一个外部资源文件pom.properties.


## 一些小坑汇总
1. assembly插件打包压缩zip时，zip包的根目录多了一级目录，这个目录名为finalname，why?
   解决方法：<includeBaseDirectory>false</includeBaseDirectory>  在descriptor对应的xml中加上这个配置就可以解决

2. maven只能定义属性，无法在build过程中引用插件时去修改

3. 当需要自定义assembly插件的打包行为时，配置中descriptors对应的xml文件，是用来描述打包的文件的相关行为
```xml
<descriptors>
	<descriptor>myassembly.xml</descriptor>
</descriptors>
```

4. 当需要自定义deploy这个生命周期的行为时，需要在使用过程中屏蔽自带的maven-deploy-plugin的行为，方法如下：
```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-deploy-plugin</artifactId>
	<version>2.8.2</version>
	<configuration>
		<skip>true</skip>
	</configuration>
</plugin>
```

5. 使用wagon-maven-plugin插件通过scp上传文件到服务器，默认插件的phase设置为deploy，方法如下：
   pom.xml修改如下：
```xml
<build>
	<extensions>
		<extension>
			<groupId>org.apache.maven.wagon</groupId>
			<artifactId>wagon-ssh</artifactId>
			<version>1.0-beta-6</version>
		</extension>
	</extensions>
	......
	<plugins>
		......
		<plugin>
			<groupId>org.codehaus.mojo</groupId>
			<artifactId>wagon-maven-plugin</artifactId>
			<version>1.0-beta-3</version>
			<executions>
				<execution>
					<id>upload</id>
					<phase>deploy</phase>
					<goals>
						<goal>upload</goal>
					</goals>
					<configuration>
						<serverId>testserver</serverId>
						<fromDir>${deploy_file_dir}/</fromDir>
						<url>scp://server_host/</url>
						<toDir>${server_dir}</toDir>
					</configuration>
				</execution>
			</executions>
			</plugin>
			<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-deploy-plugin</artifactId>
			<version>2.8.2</version>
			<configuration>
				<skip>true</skip>
			</configuration>
		</plugin>
	</plugins>
</build>
```

6. maven的settings.xml也需要设置，需要设置插件中引用的serverId，方法如下：
```xml
<servers>
	<server>
	  <id>10.96.153.201</id>
	  <username>server_username</username>
	  <privateKey>/path/to/private/key</privateKey>
	  <!-- <passphrase>optional; leave empty if not used.</passphrase> -->
	</server>
</servers>
```