> 说明：目前测试仅是本机，如果是不同机器，自选修改相关配置  
> elasticsearch5.6.10 + elasticsearch-head5 + kibana5.6.10   
> plugins: analysis-ik5.6 + analysis-pinyin5.6
> 特别注意： plugins目录，如果在mac下打开过，需要删除里面的.DS_Store文件,否则会导致es启动不起来
>          sudo find ./ -name ".DS_Store" -depth -exec rm {} \;

## docker及docker-compose安装及说明
请参考: https://yeasy.gitbooks.io/docker_practice/compose


## 安装
```
cd ~/
git clone https://github.com/viky88/docker-lnmp.git
cd docker-lnmp

```
## alias 设置
```
# bash 修改 ~/.bash_profile  
# zsh 修改 ~/.zshrc

# 修改成自己的路径
export DOCKERCOMPOS_ES_EHOME=/Users/viky/project/docker/github/viky88/docker-elasticsearch
alias godocker_es5.6="/usr/local/bin/docker-compose -f $DOCKERCOMPOS_ES_EHOME/5.6/docker-compose.yml up -d"

```
## 如何设置开机启动
```
# 根据系统先设置docker服务为开机启动
# 编辑开机启动文件
# 注意这里不用 sudo，本身是使用 root 运行的
$ sudo vim /etc/rc.local
/usr/local/bin/docker-compose -f /root/docker-elasticsearch/docker-compose.yml up -d
# 重启测试
$ sudo reboot
```
## elasticsearch-head

- 配置文件中已经配置，安装后可以直接使用
- 本机测试还有另外一种更简单安装 chrome 插件的方式:  
https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm?utm_source=chrome-app-launcher-info-dialog

## 中文分词 elasticsearch-analysis-ik 安装（已经预装）
参考： [elasticsearch-analysis-ik github](https://github.com/medcl/elasticsearch-analysis-ik)

```
# 对应好版本，不然安装不上(此功能有bug，不用)
docker exec -it elasticsearch5.6_1 /bin/bash
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.6.10/elasticsearch-analysis-ik-5.6.10.zip

# 本地安装(可将文件先下载到宿主电脑，用docker cp 命令拷贝到容器中)
# 参考1：https://github.com/medcl/elasticsearch-analysis-ik#install
# 参考2：https://blog.csdn.net/u012915455/article/details/78952068

$ docker exec -it elasticsearch5.6_1 /bin/bash
$ cd plugins
$ wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.6.10/elasticsearch-analysis-ik-5.6.10.zip
$ unzip elasticsearch-analysis-ik-5.6.10.zip
$ rm elasticsearch-analysis-ik-5.6.10.zip
```

## 拼音 elasticsearch-analysis-pinyin 安装（已经预装）
参考：  [elasticsearch-analysis-pinyin github](https://github.com/medcl/elasticsearch-analysis-pinyin)

```
# 对应好版本，不然安装不上
$ docker exec -it elasticsearch5.6_1 /bin/bash
$ cd plugins
$ wget https://github.com/medcl/elasticsearch-analysis-pinyin/releases/download/v5.6.10/elasticsearch-analysis-pinyin-5.6.10.zip
$ unzip elasticsearch-analysis-pinyin-5.6.10.zip
$ rm elasticsearch-analysis-pinyin-5.6.10.zip
```
安装完插件需要，重新启动 Elastic，就会自动加载新安装的插件

```
# 重启docker
docker restart elasticsearch5.6_1
docker restart elasticsearch5.6_2

# 查看安装的插件
docker exec -it elasticsearch5.6_1 bash ./bin/elasticsearch-plugin list

# 结果
analysis-ik
analysis-pinyin
```

## 测试

中文分词测试，于kibana>Dev Tools中输入:
```
GET _analyze
{
  "analyzer":"ik_max_word",
  "text":"中华人民"
}
```
结果：
```
{
  "tokens": [
    {
      "token": "中华人民",
      "start_offset": 0,
      "end_offset": 4,
      "type": "CN_WORD",
      "position": 0
    },
    {
      "token": "中华",
      "start_offset": 0,
      "end_offset": 2,
      "type": "CN_WORD",
      "position": 1
    },
    {
      "token": "华人",
      "start_offset": 1,
      "end_offset": 3,
      "type": "CN_WORD",
      "position": 2
    },
    {
      "token": "人民",
      "start_offset": 2,
      "end_offset": 4,
      "type": "CN_WORD",
      "position": 3
    }
  ]
}
```
出现以上内容证明中文分词生效

## 地址
- elasticsearch ： http://localhost:9200/
- elasticsearch-head : http://localhost:9100/
- kibana : http://localhost:5601/

---
## 参考：
- [Elasticsearch:5.6](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/getting-started.html)
- [Elasticsearch: 权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/cn/index.html)
- [全文搜索引擎 Elasticsearch 入门教程](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html)
- [Elasticsearch搜索服务学习之九——索引管理](https://blog.belonk.com/c/elasticsearch_index_management.html)