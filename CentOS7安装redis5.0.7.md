## CentOS7 部署redis
### 下载并编译redis
    yum -y install gcc gcc-c++ libstdc++-devel
    mkdir /redis
    cd /redis
    wget http://downloads.sourceforge.net/tcl/tcl8.6.1-src.tar.gz
    tar xzvf tcl8.6.1-src.tar.gz  -C /usr/local/
    cd  /usr/local/tcl8.6.1/unix/
    ./configure
    make
    make install
    cd /redis/redis-5.0.7
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
- 如果需要开启外网连接，修改 bind 127.0.0.1
- 设置 stop-writes-on-bgsave-error no (此处为可选项，不建议使用，如果出现(error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error．可通过修改该设置忽略持久化错误解决)
#### 启动命令
    /redis/redis-5.0.7/src/redis-server /redis/redis-5.0.7/redis.conf
    ps -A | grep redis