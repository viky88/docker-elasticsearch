### elastic安装 X-Pack

1.注册免费的 license， https://register.elastic.co


2.运行安装命令
```
# 进入docker
docker exec -it es5.6_1 bash

# 执行安装命令
./bin/elasticsearch-plugin install x-pack 
# 修改配置文件 elasticsearch.yml
# 添加如下(否则会一直提示用户名和密码)
# 如果需要用户权限，可不禁用
xpack.security.enabled: false

#退出docker
exit

# 重启使插件生效
docker restart es5.6_1

# 其他节点同以上操作
```

3.安装证书，参见：https://www.elastic.co/guide/en/x-pack/current/installing-license.html
```
curl -XPUT -u elastic 'http://<host>:<port>/_xpack/license' -H "Content-Type: application/json" -d @license.json

curl -XPUT -u elastic 'http://localhost:9200/_xpack/license' -H "Content-Type: application/json" -d @license.json
```
### kibana安装 X-Pack

```
# 进入docker
docker exec -it kibana5.6 bash

# 执行安装命令
./bin/kibana-plugin install x-pack 

#退出docker
exit

# 重启使插件生效
docker restart kibana5.6

```
说明：在安装时，可能会卡在`Optimizing and caching browser bundles...` ,网上找了很长时间，说是内存不够，至少2G。(可能真正的服务器会比较快)

我尝试的方法是将其他的es全部先关掉，速度快很多，一会就完成了

![](http://pcaxu1myu.bkt.clouddn.com/4bd07e0fdc552a54b408597738443e57.jpg!watermark)

另外，kibana 在线安装 X-Pack，可能下载会比较慢，可尝试，在宿主机器先将文件下载下来，再采取离线安装的方式安装，安装命令参考上图中第一行命令

-----
## 参考：
- [ELK 集群 Kibana 使用 X-Pack 权限控制，监控集群状态，警报，监视,cpu，内存，磁盘空间，报告和的可视化图形](https://segmentfault.com/a/1190000010981283)