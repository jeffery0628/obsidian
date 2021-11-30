---
Create: 2021年 十一月 28日, 星期日 21:02
tags: 
  - Engineering/java/maven
  - 大数据
---
# 核心概念

## POM

Project Object Model：项目对象模型。将Java工程的相关信息封装为对象作为便于操作和管理的模型。Maven工程的核心配置。可以说学习Maven就是学习pom.xml文件中的配置。

## 约定的目录结构
现在JavaEE开发领域普遍认同一个观点：约定>配置>编码。意思就是能用配置解决的问题就不编码，能基于约定的就不进行配置。而Maven正是因为指定了特定文件保存的目录才能够对我们的Java工程进行自动化构建。

几何中的坐标：在一个平面中使用x、y两个向量可以唯一的确定平面中的一个点。在空间中使用x、y、z三个向量可以唯一的确定空间中的一个点。

Maven的坐标：使用如下三个向量在Maven的仓库中唯一的确定一个Maven工程。
1. groupId：公司或组织的域名倒序+当前项目名称
2. artifactId：当前项目的模块名称
3. version：当前模块的版本

## 依赖管理
### 基本概念

当A jar包需要用到B jar包中的类时，我们就说A对B有依赖。例如：commons-fileupload-1.3.jar依赖于commons-io-2.0.1.jar。当前工程会到本地仓库中根据坐标查找它所依赖的jar包。配置的基本形式是使用dependency标签指定目标jar包的坐标。例如：
```xml
<dependency>  
	<!--坐标-->
	<groupId>junit</groupId> 
	<artifactId>junit<artifactId> 
	<version>4.0</version>  
	<!--依赖的范围-->
	<scope>test</scope>  
</dependency>
```


### 直接依赖和间接依赖
如果A依赖B，B依赖C，那么A→B和B→C都是直接依赖，而A→C是间接依赖。
### 依赖的范围
1. compile
	- main目录下的Java代码**可以**访问这个范围的依赖
	- test目录下的Java代码**可以**访问这个范围的依赖
	- 部署到Tomcat服务器上运行时**要**放在WEB-INF的lib目录下。
2. test
	- main目录下的Java代码**不能**访问这个范围的依赖
	- test目录下的Java代码**可以**访问这个范围的依赖
	- 部署到Tomcat服务器上运行时**不会**放在WEB-INF的lib目录下

3. provided
	- main目录下的Java代码**可以**访问这个范围的依赖
	- test目录下的Java代码**可以**访问这个范围的依赖
	- 部署到Tomcat服务器上运行时**不会**放在WEB-INF的lib目录下

4. runtime
5. import
6. system

各个依赖范围的作用可以概括为下图：
![[700 Attachments/Pasted image 20211128220730.png]]
### 依赖的传递性

当存在间接依赖的情况时，主工程对间接依赖的jar可以访问吗？这要看间接依赖的jar包引入时的依赖范围——只有依赖范围为compile时可以访问。


### 依赖的原则：解决jar包冲突
1. 路径最短者优先
![[700 Attachments/Pasted image 20211128220955.png]]
2. 路径相同时先声明者优先，这里“声明”的先后顺序指的是dependency标签配置的先后顺序。
![[700 Attachments/Pasted image 20211128221003.png]]

### 依赖的排除
有的时候为了确保程序正确可以将有可能重复的间接依赖排除。请看如下的例子：
> 假设当前工程为MakeFriend，直接依赖OurFriends。OurFriends依赖commons-logging的1.1.1对于MakeFriend来说是间接依赖。当前工程MakeFriend直接依赖commons-logging的1.1.2加入exclusions配置后可以在依赖OurFriends的时候排除版本为1.1.1的commons-logging的间 接依赖.
 ```
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>OurFriends</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--依赖排除-->
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.1.2</version>
</dependency>
```

### 统一管理目标jar包的版本

以对Spring的jar包依赖为例：Spring的每一个版本中都包含spring-context，springmvc等jar包。我们应该导入版本一致的Spring jar包，而不是使用4.0.0的spring-context的同时使用4.1.1的springmvc。
```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>

```

问题是如果我们想要将这些jar包的版本统一升级为4.1.1，是不是要手动一个个修改呢？显然，我们有统一配置的方式：

```
<!--统一管理当前模块的jar包的版本-->
<properties>
    <spring.version>4.0.0.RELEASE</spring.version>
</properties>

...



<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>${spring.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>${spring.version}</version>
</dependency>

```
这样一来，进行版本调整的时候只改一改地方就行了。

## 仓库

### 分类
1. 本地仓库：为当前本机电脑上的所有Maven工程服务。
2. 远程仓库
	- 私服：架设在当前局域网环境下，为当前局域网范围内的所有Maven工程服务。
	- 中央仓库：架设在Internet上，为全世界所有Maven工程服务。
	- 中央仓库的镜像：架设在各个大洲，为中央仓库分担流量。减轻中央仓库的压力，同时更快的响应用户请求。

### 仓库中的文件
1. Maven的插件
2. 我们自己开发的项目的模块
3. 第三方框架或工具的jar包

不管是什么样的jar包，在仓库中都是按照坐标生成目录结构，所以可以通过统一的方式查询或依赖。

## 生命周期
什么是Maven的生命周期？
Maven生命周期定义了各个构建环节的执行顺序，有了这个清单，Maven就可以自动化的执行构 建命令了。
Maven有三套相互独立的生命周期，分别是：
- Clean Lifecycle在进行真正的构建之前进行一些清理工作。
- Default Lifecycle构建的核心部分，编译，测试，打包，安装，部署等等。
- Site Lifecycle生成项目报告，站点，发布站点。

再次强调一下它们是==相互独立的==，你可以仅仅调用clean来清理工作目录，仅仅调用site来生成站点。当然你也可以直接运行 ==mvn clean install site== 运行所有这三套生命周期。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行mvn clean，这个clean是Clean生命周期的一个阶段。有Clean生命周期，也有clean阶段。

### clean生命周期

Clean生命周期一共包含了三个阶段：
1. pre-clean 执行一些需要在clean之前完成的工作
2. clean 移除所有上一次构建生成的文件
3. post-clean 执行一些需要在clean之后立刻完成的工作

### Site生命周期

1. pre-site 执行一些需要在生成站点文档之前完成的工作
2. site 生成项目的站点文档
3. post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
4. site-deploy 将生成的站点文档部署到特定的服务器上

这里经常用到的是site阶段和site-deploy阶段，用以生成和发布Maven站点，这可是Maven相当强大的功能，Manager比较喜欢，文档及统计数据自动生成，很好看。

### Default生命周期
Default生命周期是Maven生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里，只解释一些比较重要和常用的阶段：
validate
generate-sources
process-sources
generate-resources
process-resources 复制并处理资源文件，至目标目录，准备打包。
==compile== 编译项目的源代码。

process-classes
generate-test-sources
process-test-sources
generate-test-resources
process-test-resources 复制并处理资源文件，至目标测试目录。
**test-compile** 编译测试源代码。
process-test-classes
==test== 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。
prepare-package
==package== 接受编译好的代码，打包成可发布的格式，如JAR。
pre-integration-test
integration-test
post-integration-test
verify
==install==将包安装至本地仓库，以让其它项目依赖。
deploy将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

### 生命周期与自动化构建
**运行任何一个阶段的时候，它前面的所有阶段都会被运行**，例如我们运行mvn install 的时候，代码会被编译，测试，打包。这就是Maven为什么能够自动执行构建过程的各个环节的原因。此外，Maven的插件机制是完全依赖Maven的生命周期的，因此理解生命周期至关重要。
## 插件和目标
Maven的核心仅仅定义了抽象的生命周期，具体的任务都是交由插件完成的。每个插件都能实现多个功能，每个功能就是一个插件目标。Maven的生命周期与插件目标相互绑定，以完成某个具体的构建任务。

> 例如：compile就是插件maven-compiler-plugin的一个功能；pre-clean是插件maven-clean-plugin的一个目标。

## 继承机制
由于非compile范围的依赖信息是不能在“依赖链”中传递的，所以有需要的工程只能单独配置。
Hello
```
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.0</version>
	<scope>test</scope>
</dependency>
```
HelloFriend
```
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.0</version>
	<scope>test</scope>
</dependency>

```
MakeFriend
```

<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.0</version>
	<scope>test</scope>
</dependency>

```
此时如果项目需要将各个模块的junit版本统一为4.9，那么到各个工程中手动修改无疑是非常不可取的。使用继承机制就可以将这样的依赖信息统一提取到父工程模块中进行统一管理。
### 创建父工程
父工程的打包方式为pom
```
<groupId>com.atguigu.maven</groupId>
<artifactId>Parent</artifactId>
<packaging>pom</packaging>
<version>1.0-SNAPSHOT</version>

```
父工程只需要保留pom.xml文件即可.

### 子工程引用父工程
格式：
```
<parent>
	<!-- 父工程坐标 -->
	<groupId>...</groupId>
	<artifactId>...</artifactId>
	<version>...</version>
	<!--指定从当前pom.xml文件出发寻找父工程的pom.xml文件的相对路径-->
	<relativePath>..</relativePath>
</parent>
```
例子：
```
<!--继承-->
<parent>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>Parent</artifactId>
    <version>1.0-SNAPSHOT</version>
	<!--指定从当前pom.xml文件出发寻找父工程的pom.xml文件的相对路径-->
	<relativePath>../Parent/pom.xml</relativePath>
</parent>

```
此时如果子工程的groupId和version如果和父工程重复则可以将子工程中重复内容删除。

###  在父工程中管理依赖
将Parent项目中的dependencies标签，用dependencyManagement标签括起来：
```
<!--依赖管理-->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</dependencyManagement>


```

在子项目中重新指定需要的依赖，==删除范围和版本号==:
```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
</dependency>

```

##  聚合
将多个工程拆分为模块后，需要手动逐个安装到仓库后依赖才能够生效。修改源码后也需要逐个手动进行clean操作。而使用了聚合之后就可以批量进行Maven工程的安装、清理工作。
### 配置聚合
在总的聚合工程中使用modules/module标签组合，指定模块工程的相对路径即可:
```
<!--聚合-->
<modules>
    <module>../MakeFriend</module>
    <module>../OurFriends</module>
    <module>../HelloFriend</module>
    <module>../Hello</module>
</modules>

```
Maven可以根据各个模块的继承和依赖关系自动选择安装的顺序

## Maven酷站
可以到[http://mvnrepository.com/](http://mvnrepository.com/)搜索需要的jar包的依赖信息.