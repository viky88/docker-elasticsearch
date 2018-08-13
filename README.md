> 说明：目前测试仅是本机，如果是不同机器，自选修改相关配置  
> elasticsearch5.6.10 + elasticsearch-head5 + kibana5.6.10   
> plugins: analysis-ik5.6 + analysis-pinyin5.6

## docker及docker-compose安装及说明
请参考: https://yeasy.gitbooks.io/docker_practice/compose

# docker常用命令
```
# 查看当前启动的容器
sudo docker ps

# 后台启动所有服务
sudo docker-compose up -d  

# 启动部分服务在后边加服务名，不加表示启动所有，-d 表示在后台运行
sudo docker-compose up [NAME] -d

# 停止和启动类似
sudo docker-compose stop [NAME]

# 停止所有正在运行的容器
docker stop $(docker ps -q)

# 停止所有容器
sudo docker-compose kill

# 强制重新build镜像
sudo docker-compose up -d --build 

# 进入到某个镜像中
sudo docker exec -it [NAME] bash

# 删除已经停止的容器
sudo docker-compose rm  [NAME]  

# 删除所有未运行的容器
sudo docker rm $(docker ps -a -q)

# 删除所有镜像，-f 可以强制删除
sudo docker rmi $(docker images -q)

# 删除none等无用的镜像
sudo docker image prune -f

# 查看容易的详细信息
docker inspect [NAME]/[CONTAINER ID]

# 宿主机器重启docker内部复制内容
docker cp /path/file [NAME/CONTAINER ID]:/path/file
```