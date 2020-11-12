#### 下载并解压mysql

	mkdir /usr/local
	wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz -O /root/mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz
	tar xvf /root/mysql-8.0.18-linux-glibc2.12-x86_64 /usr/local
	mv /usr/local/mysql-8.0.18-linux-glibc2.12-x86_64 /usr/local/mysql-8.0.18
	mkdir /usr/local/mysql-8.0.18/data
	mkdir /var/log/mysql
	mkdir /var/run/mysql

#### 添加用户和组并赋予所有权

	groupadd mysqlgroup
	useradd -r -g mysqlgroup mysql
	chown -R mysql:mysqlgroup /usr/local/mysql-8.0.18
	chmod -R 755 /var/log/mysql
	chmod -R 755 /var/run/mysql
	chown -R mysql:mysqlgroup /var/log/mysql
	chown -R mysql:mysqlgroup /var/run/mysql

#### 安装libaio

	yum install libaio*

#### 初始化
	/usr/local/mysql-8.0.18/bin/mysqld --defaults-file=/usr/local/mysql-8.0.18/my.cnf --user=mysql --initialize

- 如果报error while loading shared libraries: libaio.so.1...请执行 yum install libaio后再执行初始化

#### my.cnf
	[mysqld]
	user=mysql
	basedir=/usr/local/mysql-8.0.18
	datadir=/usr/local/mysql-8.0.18/data
	port=3306
	max_connections=1024
	max_connect_errors=10
	lower_case_table_names=1
	socket=/tmp/mysql.sock
	max_allowed_packet=32M
	#默认使用“mysql_native_password”插件认证
	default_authentication_plugin=mysql_native_password
	#转换查询为缓慢查询
	slow_query_log=true
	#对应上一条，如果一个查询超过了下条设定的时间则执行上一条。
	long_query_time=2

	[mysqld_safe]
	log-error=/var/log/mysql/mariadb.log
	pid-file=/var/run/mysql/mysqld.pid
	!includedir /etc/my.cnf.d

#### mysqld.service
	[Unit]
	Description=MySQL Server
	Documentation=man:mysqld(8)
	Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
	After=network.target
	After=syslog.target

	[Install]
	WantedBy=multi-user.target

	[Service]
	User=mysql
	PIDFile=/var/run/mysql/mysqld.pid
	# Disable service start and stop timeout logic of systemd for mysqld service.
	TimeoutSec=0
	# Execute pre and post scripts as root
	PermissionsStartOnly=true
	# Needed to create system tables
	#ExecStartPre=/usr/bin/mysqld_pre_systemd
	# Start main service
	ExecStart=/usr/local/mysql-8.0.18/bin/mysqld --defaults-file=/usr/local/mysql-8.0.18/my.cnf --user=mysql --pid-file=/var/run/mysql/mysqld.pid --daemonize
	# 注意这里要加上 --daemonize 
	# Use this to switch malloc implementation
	#EnvironmentFile=-/etc/sysconfig/mysql
	# Sets open_files_limit
	LimitNOFILE = 5000
	Restart=on-failure
	RestartPreventExitStatus=1
	PrivateTmp=false

#### 设置root密码

	SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass#123'); -- 8.0+版本密码必须要包含符号

#### 开启远程访问
	USE mysql
	UDPATE USER SET Host='%' WHERE User='root';
	FLUSH PRIVILEGES;

#### 修改密码插件（解决 plugin caching_sha2_password could not be loaded）
	alter user 'root'@'%' identified by 'newpass#123' password expire never;
	alter user 'root'@'%' identified with  mysql_native_password by  'newpass#123';
	flush privileges;
	alter  user 'root'@'%' identified by 'newpass#123';
	flush privileges;

