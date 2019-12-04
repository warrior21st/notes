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
    chown -R redisgroup:redis /redis/redis-5.0.7
#### redis.service
    [Unit]
    Description=redis server 5.0.7
    After=network.target

    [Service]    
    Type=notify
    User=redis
    Group=redisgroup
    ExecStart=/redis/redis5.0.7/src/redis-server /redis/redis5.0.7/redis.conf
    ExecStop=/redis/redis5.0.7/src/redis-cli -h 127.0.0.1 -p 6379 shutdown
    Restart=always

    [Install]
    WantedBy=multi-user.target