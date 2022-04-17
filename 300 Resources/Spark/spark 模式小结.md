---
Create: 2022年 三月 10日, 星期四 10:25
tags: 
  - Engineering/spark
  - 大数据
---

# 几种模式对比
| 模式       | Spark安装机器数 | 需启动的进程   | 所属者 |
| ---------- | --------------- | -------------- | ------ |
| Local      | 1               | 无             | Spark  |
| Standalone | 3               | Master及Worker | Spark  |
| Yarn       | 1               | Yarn及HDFS     | Hadoop |

# 端口号总结
1. Spark历史服务器端口号：18080		（类比于Hadoop历史服务器端口号：19888）
2. Spark Master Web端口号：8080（类比于Hadoop的NameNode Web端口号：9870(50070)）
3. Spark Master内部通信服务端口号：7077	（类比于Hadoop的8020(9000)端口）
4. Spark查看当前Spark-shell运行任务情况端口号：4040
5. Hadoop YARN任务运行情况查看端口号：8088



