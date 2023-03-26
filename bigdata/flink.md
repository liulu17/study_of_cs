## flink standalone 部署
1. flink.conf.yml 配置


## 启动问题
   
1. taskmanager启动了，但是web ui可用的slots为0，这个主要是由于不是本地部署，所以一些配置参数需要改成对应机器的ip或者hostname
```
taskmanager.bind-host: 0.0.0.0
jobmanager.bind-host: 0.0.0.0

```

2. 日志报错信息

Error: VM option 'UseG1GC' is experimental and must be enabled via -XX:+UnlockExperimentalVMOptions.
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.

将bin/taskmanager.sh中的-Xx:G1GC删掉


## 运行example




# 部署方式
## standalone 模式
   
   此模式就是flink集群不依赖其他资源管理器，flink自成一个集群

### session模式（伪集群）
此方式就是在本地启动两个jvm进程，一个是jobmanager，一个是jobmanager

session 模式会预先启动一个session集群，每个submit任务都是在这个集群中，共享JobManager和TaskManager. 不利于资源隔离，不适用生产环境


1. 启动session集群
```
start-cluster.sh
```
2. 提交任务
```
./bin/flink run ./examples/streaming/TopSpeedWindowing.jar
```
3. 关闭集群
```
stop-cluster.sh
```

### application模式
applicaion 模式在每次提交任务时会启动一个flink集群，并且cli端代码的main方法不会在本地执行。同时为了加快jar包等资源上传，flink允许提交时执行jar包的远端路径，如hadoop，从而避免的jar包的上传。同时还可以把cli的jar包上传的远端的hadoop集群，让flink集群直接从hadoop集群拉取jar包，避免了jar包的上传。如果是基于yarn的flink集群部署，那么hadoop上的jar包可能都不需要经过网络传输，直接在yarn集群的本地都可以拿到jar包。

1. 将cli端jar包上传至classpath下
```
cp ./examples/streaming/TopSpeedWindowing.jar lib/

```

2. 启动jobmanager
```
standalone-job.sh start --job-classname org.apache.flink.streaming.examples.windowing.TopSpeedWindowing
```
这个时候任务状态为创建，但是还没有启动TaskManager，任务跑不起来
3. 启动taskmanager
```
taskmanager.sh start
```
任务开始运行

4. 关闭
```
standalone-job.sh stop
taskmanager.sh stop
```

注意： jobmanager.sh 和 standalone-job.sh 都是启动JobManager,一个是application模式，一个是session模式


5. 查看job

flink list -yid application_1678572364271_0002

flink cancel -t yarn-application c48307e9a2823b280b61c30facfe335d