# 内网172ip与docker冲突

**windows:**

配置docker-desktop

**linux：**

修改/etc/docker/daemon.json
* docker network prune
* systemctl stop docker
* systemctl daemon-reload
* systemctl restart docker

```properties
"default-address-pools" : [
    {
      "base" : "172.31.0.0/16",
      "size" : 24
    }
  ]
```



# docker-compse

```yaml
version: "3"
services:
  redis:
    container_name: redis
    image: redis:latest
    restart: always
    ports:
      - "6379:6379"
    volumes:
      - /home/kevin/redis.conf:/usr/local/etc/redis/redis.conf
    command: redis-server redis.conf --requirepass 123456

  oss:
    container_name: my_oss
    build: .
    ports:
      - "9090:9090"
    restart: always
    environment:
      TZ: "Asia/Shanghai"
```



