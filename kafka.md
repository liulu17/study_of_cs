
# 开始生产
kafka-console-producer.sh --topic frist --bootstrap-server localhost:9092

# 开始消费
kafka-console-consumer.sh --topic frist --bootstrap-server localhost:9092 --from-beginning

--property print.key=true 显示键值



# 创建topic
kafka-topics.sh  --create --topic test-kafka --bootstrap-server  localhost:9092,localhost:9093,localhost:9094 --partitions 3 relication-factor 2


# 查看topic
kafka-topics.sh --list --bootstrap-server localhost:9092

# 查看topic的分区情况
kafka-topics.sh --describe  --bootstrap-server localhost:9092


# 消费组
## 查看消费者组
kafka-consumer-groups.sh --bootstrap-server xxxx:9090 --list

## 查看某个组
kafka-consumer-groups.sh --bootstrap-server xxxx:9090 --describe --group group_name

## 查看所有组
kafka-consumer-groups.sh --bootstrap-server xxxx:9090 --describe --all-groups


## 查看组的成员
kafka-consumer-groups.sh --bootstrap-server xxxx:9090 --describe --members --group group_name

## 查看组的消费状态
kafka-consumer-groups.sh --bootstrap-server xxxx:9090 --describe --state --group group_name

