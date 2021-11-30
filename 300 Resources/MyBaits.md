---
Create: 2021年 十一月 29日, 星期一 09:37
tags: 
  - Engineering/java/MyBaits
  - 大数据
---
#TODO 

# 概述

1. MyBatis是Apache的一个开源项目iBatis, 2010年6月这个项目由Apache Software Foundation 迁移到了Google Code，随着开发团队转投Google Code旗下， iBatis3.x正式更名为MyBatis ，代码于2013年11月迁移到Github
2. MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架
3. MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集
4. MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录
5. Mybatis 是一个 半自动的ORM（Object Relation Mapping）框架
6. MyBatis 下载网址 https://github.com/mybatis/mybatis-3/

# MyBaits 工程
1. 创建一个Maven版的java工程
2. 在pom.xml中加入对MyBatis的依赖

	```xml
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.1</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.37</version>
		</dependency>

		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.14</version>
		</dependency>

	```
3. 导入log4j的配置文件
4. 创建测试表
	```sql
	-- 创建库
	CREATE DATABASE test_mybatis;
	-- 使用库
	USE test_mybatis;
	-- 创建表
	CREATE TABLE tbl_employee(
	   id INT(11) PRIMARY KEY AUTO_INCREMENT,
	   last_name VARCHAR(50),
	   email VARCHAR(50),
	   gender CHAR(1)
	);

	```
5. 创建JavaBean
```java
public class Employee {

	private Integer id ; 
	private String lastName; 
	private String email ;
	private String gender ;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getLastName() {
		return lastName;
	}
	public void setLastName(String lastName) {
		this.lastName = lastName;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getGender() {
		return gender;
	}
	public void setGender(String gender) {
		this.gender = gender;
	}
	@Override
	public String toString() {
		return "Employee [id=" + id + ", lastName=" + lastName + ", email=" + email + ", gender=" + gender + "]";
	}

```
6. 创建Mybatis的全局配置文件，参考Mybatis的官方手册
	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
		<!-- 数据库连接环境的配置 -->
		<environments default="development">
			<environment id="development">
				<transactionManager type="JDBC" />

				<dataSource type="POOLED">
					<property name="driver" value="com.mysql.jdbc.Driver" />
					<property name="url" value="jdbc:mysql://localhost:3306/mybatis_1129" />
					<property name="username" value="root" />
					<property name="password" value="1234" />
				</dataSource>
			</environment>
		</environments>
		<!-- 引入SQL映射文件,Mapper映射文件 	-->
		<mappers>
			<package name="Mapper映射文件所在的包 " />
		</mappers>
	</configuration>
	```
7. 创建Mapper接口
	```java
	/**
	 * Mapper接口，实际上就是我们非常熟悉的Dao.
	 */
	public interface EmployeeMapper {
		//根据ID查询Employee
		public Employee getEmployeeById(Integer id );

	}

	```
8. 创建Mapper映射文件
	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	<!-- 
		Mapper映射文件: 定义CRUD的SQL语句
	 -->
	<mapper namespace="com.atguigu.mybatis.mapper.EmployeeMapper">

		<!-- public Employee getEmployeeById(Integer id ); -->
		<select id="getEmployeeById" resultType="com.atguigu.mybatis.beans.Employee" parameterType="java.lang.Integer">
			select id,last_name, email,gender from tbl_employee where id = #{id }
		</select>
	</mapper>
	```
9. 测试
```java
@Test
public void test()  throws Exception{
	String resource = "mybatis-config.xml";
	InputStream inputStream =Resources.getResourceAsStream(resource);
	SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
.build(inputStream);		
	SqlSession session = sqlSessionFactory.openSession();
	try {
		//Mapper接口:获取Mapper接口的 代理实现类对象
		EmployeeMapper mapper =session.getMapper(EmployeeMapper.class);		
		Employee employee = mapper.getEmployeeById(1006);
		System.out.println(employee);
	} finally {
		session.close();
	}
}


```

# CRUD