## 1.安装docker 和docker-compose
参考：https://www.yuque.com/docs/share/9c00c3e6-c7e1-4881-98b4-58b8a397f157?# 《k8s部署》密码：gack
备注：查看折叠全部的命令详情：docker ps --no-trunc

## 2.安装es前的配置
sudo sysctl -w vm.max_map_count=262144
cd /data/es
sudo mkdir data logs
sudo chown -R 1000:1000  data logs(docker 里es的用户uid)
discovery.seed_hosts: 必须有一个，集群的节点即可

## 2.定义docker-compose.yaml
三个节点的集群
es1 
```|json
version: '3.6'
services:

 elasticsearch:
    ports:
    - "9200:9200"
    - "9300:9300"
    container_name: clearml-elastic-111
    environment:
      ES_JAVA_OPTS: -Xms6g -Xmx6g
      cluster.name: clearml-111
      node.name: node1-es111
      network.publish_host: self_ip
      discovery.seed_hosts: "other_ip"
      cluster.initial_master_nodes: node1-es111
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.2
    restart: unless-stopped
    volumes:
      -  /data/clearml/data/:/usr/share/elasticsearch/data
      - /data/clearml/logs/:/usr/share/elasticsearch/logs

 kibana:
    container_name: kibana
    image: docker.elastic.co/kibana/kibana:7.6.2
    restart: always
    environment:
      - ELASTICSEARCH_HOSTS=http://100.121.130.235:9200    # address of elasticsearch docker container which kibana will connect
    ports:
      - 5601:5601

 cerebro:
  image: lmenezes/cerebro:0.8.4
  container_name: cerebro
  ports:
    - "9000:9000"
  command:
    - -Dhosts.0.host=http://100.121.130.235:9200
```

在es1 上安装 kibana 和 cerebro 工具，用来提供es可视化管理。
执行如下命令安装即可：
* docker-compose up -d elasticsearch
* docker-compose up -d xxx

如果要删除，执行：
```|json
 docker-compose rm -s -v elasticsearch
```

es2
```|json
version: '3.6'
services:
 elasticsearch:
    ports:
    - "9200:9200"
    - "9300:9300"
    container_name: clearml-elastic-222
    environment:
      ES_JAVA_OPTS: -Xms6g -Xmx6g
      cluster.name: clearml-111
      node.name: node1-es222
      network.publish_host: xxx
      discovery.seed_hosts: "xxx,xxx"
      cluster.initial_master_nodes: node1-es111
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.2
    restart: unless-stopped
    volumes:
      -  /data/clearml/data/:/usr/share/elasticsearch/data
      - /data/clearml/logs/:/usr/share/elasticsearch/logs
```

es3
```|json
version: '3.6'
services:
 elasticsearch:
    ports:
    - "9200:9200"
    - "9300:9300"
    container_name: clearml-elastic-333
    environment:
      ES_JAVA_OPTS: -Xms6g -Xmx6g
      cluster.name: clearml-111
      node.name: node1-es333
      network.publish_host: xxx
      discovery.seed_hosts: "xxx"
      cluster.initial_master_nodes: node1-es111
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    image: docker.elastic.co/elasticsearch/elasticsearch:7.6.2
    restart: unless-stopped
    volumes:
      -  /data/clearml/data/:/usr/share/elasticsearch/data
      - /data/clearml/logs/:/usr/share/elasticsearch/logs
```

## 3.查看es健康命令
```
curl http://127.0.0.1:9200/_cluster/health

```

## 4.性能测试
参考，https://cloud.tencent.com/developer/article/1595636
版本有点问题

## 备注：
* es和kibana版本对比