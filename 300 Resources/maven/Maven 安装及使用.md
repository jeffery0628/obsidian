---
Create: 2021年 十一月 28日, 星期日 20:53
tags: 
  - Engineering/java/maven
  - 大数据
---
#TODO 


# Maven 安装
## 安装步骤

1. 检查JAVA_HOME环境变量。Maven是使用Java开发的，所以必须知道当前系统环境中JDK的安装目录。
2. 解压Maven的核心程序。将apache-maven-x.x.x-bin.zip解压到一个**非中文无空格**的目录下。
3. 配置环境变量。M2_HOME 和 path(%M2_HOME%\\bin)

## Maven 联网问题
1. Maven的核心程序并不包含具体功能，仅负责宏观调度。具体功能由插件来完成。Maven核心程序会到本地仓库中查找插件。如果本地仓库中没有就会从远程中央仓库下载。此时如果不能上网则无法执行Maven的具体功能。为了解决这个问题，我们可以将Maven的本地仓库指向一个在联网情况下下载好的目录。
2.  Maven默认的本地仓库：~.m2\repository目录。
3.  Maven的核心配置文件位置：maven_dier\conf\settings.xml
4.  设置方式<localRepository>以及准备好的仓库位置</localRepository>
5.  为了以后下载jar包方便，配置阿里云镜像
	```      
	<mirror>
		<id>nexus-aliyun</id>
		<mirrorOf>central</mirrorOf>
		<name>Nexus aliyun</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public</url>
	</mirror>
	```
	
	
## 在Idea中配置Maven
### 设置maven的安装目录及本地仓库
设置maven的安装目录及本地仓库
Build,Execution,Deployment - Build Tools - Maven
Maven home directory：可以指定本地 Maven 的安装目录所在，配置了 M2_HOME 系统参数, IntelliJ IDEA 可以找到的。但是假如你没有配置的话，这里可以选择你的 Maven 安装目录。
User settings file: 指定Maven安装目录conf/settings.xml
Local repository: 指定Maven本地仓库位置。
### 配置Maven自动导入依赖的jar包
==Import Maven projects automatically==：表示 IntelliJ IDEA 会实时监控项目的 pom.xml 文件，进行项目变动设置，勾选上。
==Automatically download==：在 Maven 导入依赖包的时候是否自动下载源码和文档。默认是没有勾选的，也不建议勾选，原因是这样可以加快项目从外网导入依赖包的速度，如果我们需要源码和文档的时候我们到时候再针对某个依赖包进行联网下载即可。IntelliJ IDEA 支持直接从公网下载源码和文档的。
==VM options for importer==：可以设置导入的 VM 参数。一般这个都不需要主动改，除非项目真的导入太慢了再增大此参数。
