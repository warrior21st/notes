#### 安装命令（将命令行工作目录cd到{mysql目录}\bin）  
    ./mysqld.exe --initialize-insecure
    mysqld install
    net start mysql

#### my.ini
    [mysqld]
    user=mysql
    basedir=d:\mysql-8.0.19
    datadir=d:\mysql-8.0.19\data
    bind-address = 0.0.0.0
    port = 3306
    max_connections=1024
    max_connect_errors=10
    lower_case_table_names=1
    socket=/tmp/mysql.sock
    max_allowed_packet=20M
    #默认使用“mysql_native_password”插件认证(兼容较老版本的ui工具连接)
    default_authentication_plugin=mysql_native_password
    #转换查询为缓慢查询
    slow_query_log=true
    #对应上一条，如果一个查询超过了下条设定的时间则执行上一条。
    long_query_time=2

    [mysql]
    default-character-set=utf8
    [mysql.server]
    default-character-set=utf8
    [mysql_safe]
    default-character-set=utf8
