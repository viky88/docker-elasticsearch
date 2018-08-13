> 说明：目前测试仅是本机，如果是不同机器，自选修改相关配置  
> elasticsearch5.6.10 + elasticsearch-head5 + kibana5.6.10   
> plugins: analysis-ik5.6 + analysis-pinyin5.6

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

## 已经安装的插件
- 中文分词 elasticsearch-analysis-ik 安装
- 拼音 elasticsearch-analysis-pinyin 

> 特别注意： plugins目录，如果在mac下打开过，需要删除里面的.DS_Store文件,否则会导致es启动不起来，执行下面命令，删除.DS_Store    
>          sudo find ./ -name ".DS_Store" -depth -exec rm {} \;


```
# 自行安装方法
# 对应好版本，不然安装不上
# (官方elasticsearch-plugins功能有bug，不要用)
# 集群中每个容器都要安装
# 下面以安装 analysis-ik 为例
docker exec -it es5.6_1 /bin/bash
cd plugins
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.6.10/elasticsearch-analysis-ik-5.6.10.zip
unzip elasticsearch-analysis-ik-5.6.10.zip
rm elasticsearch-analysis-ik-5.6.10.zip

# 安装完插件需要，重新启动 Elastic，就会自动加载新安装的插件
# 重启docker
docker restart es5.6_1
docker restart es5.6_2

# 查看安装的插件
docker exec -it es5.6_1 bash ./bin/elasticsearch-plugin list

# 结果
analysis-ik
analysis-pinyin
```

## 测试中文分词

中文分词测试，于[kibana>Dev Tools](http://localhost:5601/app/kibana#/dev_tools)中输入:
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

## 通过模板设置全局默认分词器

```
curl -XDELETE http://localhost:9200/_template/rtf

curl -XPUT http://localhost:9200/_template/rtf -d'
{
  "template":   "*", 
  "settings": { "number_of_shards": 1 }, 
  "mappings": {
    "_default_": {
      "_all": { 
        "enabled": true
      },
      "dynamic_templates": [
        {
          "strings": { 
            "match_mapping_type": "string",
            "mapping": {
              "type": "text",
              "analyzer":"ik_max_word",
              "ignore_above": 256,
              "fields": {
                "keyword": {
                  "type":  "keyword"
                }
              }
            }
          }
        }
      ]
    }
  }
}
'
```

## 地址
- elasticsearch ： http://localhost:9200/
- elasticsearch-head : http://localhost:9100/
- kibana : http://localhost:5601/

---
## 参考：
- [Elasticsearch:5.6](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/getting-started.html)
- [Elasticsearch: 权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/cn/index.html)
- [elasticsearch-analysis-ik github](https://github.com/medcl/elasticsearch-analysis-ik)
- [elasticsearch-analysis-pinyin github](https://github.com/medcl/elasticsearch-analysis-pinyin)
- [elasticsearch-rtf](https://github.com/medcl/elasticsearch-rtf)
