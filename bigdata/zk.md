1. zk 端口
- 2181：对cline端提供服务
- 3888：选举leader使用
- 2888：集群内机器通讯使用（Leader监听此端口）

2. zkCli使用
./zkCli.sh -server localhost:2181