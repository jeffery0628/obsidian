---
Create: 2022年 一月 20日, 星期四 22:55
tags: 
  - Engineering/zookeeper
  - 大数据
---

## 环境搭建

1. 创建一个maven工程
2. 添加pom文件
	```xml
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.apache.logging.log4j</groupId>
			<artifactId>log4j-core</artifactId>
			<version>2.8.2</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
		<dependency>
			<groupId>org.apache.zookeeper</groupId>
			<artifactId>zookeeper</artifactId>
			<version>3.5.7</version>
		</dependency>
	</dependencies>

	```

3. 拷贝log4j.properties文件到项目根目录
	```yaml
	log4j.rootLogger=INFO, stdout  
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender  
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout  
	log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n  
	log4j.appender.logfile=org.apache.log4j.FileAppender  
	log4j.appender.logfile.File=target/spring.log  
	log4j.appender.logfile.layout=org.apache.log4j.PatternLayout  
	log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n  

	```


## 创建ZooKeeper客户端
```java
private static String connectString =
 "hadoop102:2181,hadoop103:2181,hadoop104:2181";
	private static int sessionTimeout = 2000;
	private ZooKeeper zkClient = null;

	@Before
	public void init() throws Exception {

	zkClient = new ZooKeeper(connectString, sessionTimeout, new Watcher() {

			@Override
			public void process(WatchedEvent event) {

				// 收到事件通知后的回调函数（用户的业务逻辑）
				System.out.println(event.getType() + "--" + event.getPath());

				// 再次启动监听
				try {
					zkClient.getChildren("/", true);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		});
	}
}

```

## 创建子节点
```java
/ 创建子节点
@Test
public void create() throws Exception {

		// 参数1：要创建的节点的路径； 参数2：节点数据 ； 参数3：节点权限 ；参数4：节点的类型
		String nodeCreated = zkClient.create("/atguigu", "jinlian".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
}


```

## 获取子节点并监听节点变化
```java
// 获取子节点
@Test
public void getChildren() throws Exception {

		List<String> children = zkClient.getChildren("/", true);

		for (String child : children) {
			System.out.println(child);
		}

		// 延时阻塞
		Thread.sleep(Long.MAX_VALUE);
}

```

## 判断Znode是否存在
```java
// 判断znode是否存在
@Test
public void exist() throws Exception {

	Stat stat = zkClient.exists("/eclipse", false);

	System.out.println(stat == null ? "not exist" : "exist");
}

```