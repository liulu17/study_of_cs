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

