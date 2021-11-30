---
Create: 2021年 十一月 29日, 星期一 22:23
tags: 
  - Engineering/java/spring-boot
  - 大数据
---
Spring Boot框架在集成Mybatis框架的时候依然要扫描Dao接口，SQL映射文件以及依赖数据库连接池，但是和传统SSM框架集成时稍微有一些不同。
1. 增加Mybatis 依赖
	```xml
	<project>
		...
		<dependencies>
			...
			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
			</dependency>
			<!-- 数据库连接池 -->
			<dependency>
				<groupId>com.alibaba</groupId>
				<artifactId>druid</artifactId>
				<version>1.0.5</version>
			</dependency>
			<dependency>
				<groupId>org.mybatis.spring.boot</groupId>
				<artifactId>mybatis-spring-boot-starter</artifactId>
				<version>1.1.1</version>
			</dependency>
			...
		</dependencies>
		...
	</project>

	```
2. 增加application.yml配置文件，并配置连接池和Mybatis相关配置
	```yml
	spring: 
	  datasource:
		name: mydb
		type: com.alibaba.druid.pool.DruidDataSource
		url: jdbc:mysql://127.0.0.1:3306/atcrowdfunding
		username: root
		password: root
		driver-class-name: com.mysql.jdbc.Driver
	mybatis:   
	  mapper-locations: classpath*:/mybatis/mapper-*.xml
	  type-aliases-package: com.atguigu.**.bean
	
	```
3. 扫描Dao接口和开启声明式事务
	需要在AtCrowdfundingApplication类中增加扫描注解 
	@MapperScan("com.atguigu.dao)及事务管理@EnableTransactionManagement

4. 增加Dao代码
	```java
	package com.atguigu.crowdfunding.dao;
	import org.apache.ibatis.annotations.Insert;
	import org.apache.ibatis.annotations.Select;
	import com.atguigu.crowdfunding.bean.Member;
	public interface MemberDao {
		@Select("select * from t_member where id = #{id}")
		public Member queryById(Integer id);

		@Insert("insert into t_member (loginacct) values (#{loginacct})")
		public int insertMember(Member member);
	}

	```

5. 增加Member实体类
	```java
	package com.atguigu.crowdfunding.bean;
	public class Member {
		public Integer id;
		private String name;

		public String getName() {
				return name;
		}
		public void setName(String name) {
				this.name= name;
		}
		public Integer getId() {
				return id;
		}
		public void setId(Integer id) {
				this.id = id;
		}

	}

	
	```
6. 增加事务注解 @Transactional
	传统的SSM架构中采用的是声明式事务，需要在配置文件中增加AOP事务配置，Spring Boot框架中简化了这种配置，可以在Service接口中增加注解@Transactional 
	```java
	package com.atguigu.crowdfunding.service.impl;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;
	import org.springframework.transaction.annotation.Transactional;
	import com.atguigu.crowdfunding.bean.Member;
	import com.atguigu.crowdfunding.dao.MemberDao;
	import com.atguigu.crowdfunding.service.MemberService;
	@Service
	@Transactional(readOnly=true)
	public class MemberServiceImpl implements MemberService {
		@Autowired
		private MemberDao memberDao;

		public Member queryById(Integer id) {
				return memberDao.queryById(id);
		}
		@Transactional
		public int insertMember(Member member) {
				return memberDao.insertMember(member);
		}
	}

	
	```

7. 修改MemberController进行测试
	```java
	package com.atguigu.crowdfunding.controller;
	import java.util.HashMap;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RestController;
	import org.springframework.transaction.annotation.EnableTransactionManagement;
	import com.atguigu.crowdfunding.bean.Member;
	import com.atguigu.crowdfunding.service.MemberService;
	@RestController
	@RequestMapping("/member")
	public class MemberController {
		@Autowired
		private MemberService memberService;

		@RequestMapping("/index/{id}")
		public Object index(@PathVariable("id") Integer id ) {
				Member member = memberService.queryById(id);
				return member;
		}

		@RequestMapping("/insert")
		public Object insert( Member member ) {
				memberService.insertMember(member);
				return new HashMap();
		}
	}

	```
	
8. 测试:重启服务，访问路径http://127.0.0.1:8080[/应用路径名称]/member/index观察效果
