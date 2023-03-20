# 镜像操作
```
搜索：docker search ${镜像}

拉取：docker pull ${镜像}

查看：docker image list

删除：docker image rm ${镜像}

保存：docker image save ${镜像} > ${镜像}.tar.gz

加载：docker image load -i ${镜像}.tar.gz
```

# 容器操作
```
查看：docker ps -a

启动：docker start ${容器}

停止：docker stop ${容器}

进入：docker exec -it ${容器} /bin/bash

删除所有：docker rm -f  \`docker ps -a -q\`

保存：docker commit ${容器} ${镜像}

日志：docker logs -f ${容器}
```

# 运行命令
```
docker run
    -it #可交互终端
    -d #后台运行
    -p 8080:8080 #端口映射
    -v ${本机目录}:${容器目录}
    -m 内存设置
    --cpuset-cpus #使用哪个cpu运行
    --net #容器网络
    --ip #容器启动的ip地址
    --name #容器命名
    --restart always #自动启动
    --add-host 域名：ip #添加hosts
```

eg:
docker run -it --cpuset-cpus 0,1 -m 8G --net docker-br0  --ip 172.172.0.14 -v ~/bigdata/:/home/hadoop/bigdata/ --name client --restart always hadoop-base-02 /bin/bash /etc/start.sh


# 停止
```
docker stop 5921cb1f1a87
```
# 网络服务
```
创建网络
docker network create --subnet=172.172.0.0/24 docker-br0

查看网络
docker network ls

查看网络明细
docker network inspect net_id
```



# docker-compose
```
docker-compose up 创建cluster
docker-compose start 开启cluser
docker-compose down 关闭cluster
```