# 安装docker-compose

```cmd
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.6.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

**http://get.daocloud.io/#install-compose**：国内下载源

**https://www.runoob.com/docker/docker-dockerfile.html**：命令文档

**https://blog.51cto.com/u_4925054/2342021**：docker-compose命令详解文档

## 1. 安装redis

```yml
version: '3'
services:
  redis:
    image: redis:6.2
    container_name: redis
    command: redis-server --requirepass root #连接容器时需要设置密码
    ports:
      - "6379:6379"  #设置端口
    volumes: #映射配置文件路径
      - "/usr/local/soft/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf"
    restart: always #设置始终重启
```

## 2. 安装kafka

```yml
version: '3'
services:
  zookeeper:
    image: docker.io/bitnami/zookeeper:3.8
    restart: always
    hostname: zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes
    networks:
      - zk_kafka
  kafka: #创建kafka
    image: docker.io/bitnami/kafka:3.2
    container_name: kafka
    hostname: kafka
    ports:
      - "9092:9092"
    environment:
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
      - ALLOW_PLAINTEXT_LISTENER=yes
    depends_on:
      - zookeeper
    networks:
      - zk_kafka
# 创建kafka和zk之间在同一个网络中
networks:
  zk_kafka:
```



