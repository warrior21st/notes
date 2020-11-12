## CentOS7 部署redis
### 下载并编译redis
    mkdir /redis
    cd /redis
    wget http://download.redis.io/releases/redis-5.0.7.tar.gz
    tar -zvxf ./redis-5.0.7.tar.gz
    cd ./redis-5.0.7
    make MALLOC=libc
### 添加组和用户
    groupadd redisgroup
    useradd -r -g redisgroup redis
    chown -R redis:redisgroup /redis/redis-5.0.7
### 修改redis.conf
    vi /redis/redis-5.0.7/redis.conf
- 设置 daemonize yes
- 设置 supervised auto
- 设置 requirepass 访问密码
- 如果需要开启外网连接，重写 bind 127.0.0.1
#### 启动命令
    /redis/redis-5.0.7/src/redis-server /redis/redis-5.0.7/redis.conf