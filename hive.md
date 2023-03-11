## 客户端连接服务器
beeline -u jdbc:hive2://hive-server:10000 -n hadoop


## 服务器启动和关闭
```
nohup hive --service metastore &
nohup hive --service hiveserver2 &
kill -9 $(jps -lm | grep -i 'metastore.HiveMetaStore' | awk '{print $1}')
kill -9 $(jps -lm | grep -i 'HiveServer2' | awk '{print $1}')
```