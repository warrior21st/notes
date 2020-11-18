## CentOS7 部署redis
### 安装编译环境
    yum -y install gcc gcc-c++ libstdc++-devel
### 创建redis目录
    mkdir /redis
    cd /redis
### 下载并编译安装tcl8.6.1（如果需要运行make test的话）
    wget http://downloads.sourceforge.net/tcl/tcl8.6.1-src.tar.gz
    tar xzvf tcl8.6.1-src.tar.gz  -C /usr/local/
    cd  /usr/local/tcl8.6.1/unix/
    ./configure
    make
    make install
### 下载并编译redis
    cd /redis
    wget http://download.redis.io/releases/redis-5.0.7.tar.gz
    tar -zvxf ./redis-5.0.7.tar.gz
    cd /redis/redis-5.0.7
    make MALLOC=libc
### 添加组和用户
    //groupadd redisgroup
    //useradd -r -g redisgroup redis
    //chown -R redis:redisgroup /redis/redis-5.0.7
    //mkdir /var/log/redis
    //chown -R redis:redisgroup /var/log/redis
### 修改redis.conf
    vi /redis/redis-5.0.7/redis.conf
- 设置 daemonize yes （如果使用systemd管理则设为no）
- 设置 supervised no
- 设置 requirepass 访问密码
- 如果需要开启外部连接，修改 bind 内网ip 127.0.0.1
- 设置 stop-writes-on-bgsave-error no (此处为可选项，不建议使用，如果出现(error) MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error．可通过修改该设置忽略持久化错误解决)
#### 启动命令
    /redis/redis-5.0.7/src/redis-server /redis/redis-5.0.7/redis.conf
    ps -A | grep redis
#### 使用systemd管理
    [Unit]
    Description=redis-server-5.0.7
    After=network.target

    [Service]    
    Type=notify
    User=root
    ExecStart=/redis/redis-5.0.7/src/redis-server /redis/redis-5.0.7/redis.conf --daemonize no --supervised systemd
    ExecStop=/redis/redis-5.0.7/src/redis-cli -h 127.0.0.1 -p 6379 -a 访问密码 shutdown
    Restart=always
    RestartSec=10  # Restart service after 10 seconds if service crashes

    [Install]
    WantedBy=multi-user.target