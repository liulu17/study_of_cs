# hdfs
## 端口情况
- 9870 查看namenode，以及查看集群
- 9866 datanode
- 8042
## 格式化namenode
hdfs namenode -format

## 启动hdfs
start-dfs.sh

## 关闭hdfs
stop-dfs.sh


# yarn
## 端口情况
- 8032 ResourceManager
- 8088 查看yarn情况
  
## 启动yarn
$HADOOP_HOME/sbin/start-yarn.sh

## 关闭yarn
$HADOOP_HOME/sbin/stop-yarn.sh
