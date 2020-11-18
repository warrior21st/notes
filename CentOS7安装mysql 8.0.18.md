#### 下载并解压mysql

	mkdir /mysql
	cd /mysql
	wget https://cdn.mysql.com/archives/mysql-8.0/mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz
	tar xvf ./mysql-8.0.18-linux-glibc2.12-x86_64.tar.xz
	mv /mysql/mysql-8.0.18-linux-glibc2.12-x86_64 /mysql/mysql-8.0.18
	mkdir /mysql/mysql-8.0.18/data
	mkdir /var/log/mysql
	mkdir /var/run/mysql

#### 添加用户和组并赋予所有权

	groupadd mysqlgroup
	useradd -r -g mysqlgroup mysql
	chown -R mysql:mysqlgroup /mysql/mysql-8.0.18
	chmod -R 755 /var/log/mysql
	chmod -R 755 /var/run/mysql
	chown -R mysql:mysqlgroup /var/log/mysql
	chown -R mysql:mysqlgroup /var/run/mysql

#### 添加tmpfiles.d
	vi /usr/lib/tmpfiles.d/mysql.conf
#### tmpfiles.d/mysql.conf
	d /var/run/mysql 0755 mysql mysqlgroup -	
#### 安装libaio
	yum install -y libaio*
#### 修改my.cnf
	vi /mysql/mysql-8.0.18/my.cnf
#### my.cnf
	[mysqld]
	basedir=/mysql/mysql-8.0.18
	datadir=/mysql/mysql-8.0.18/data
	port=3306
	max_connections=1024
	max_connect_errors=10
	lower_case_table_names=1
	max_allowed_packet=32M
	#默认使用“mysql_native_password”插件认证
	default_authentication_plugin=mysql_native_password
	#转换查询为缓慢查询
	slow_query_log=true
	#对应上一条，如果一个查询超过了下条设定的时间则执行上一条。
	long_query_time=2
#### 初始化
	/mysql/mysql-8.0.18/bin/mysqld --defaults-file=/mysql/mysql-8.0.18/my.cnf --user=mysql --initialize

- 如果报error while loading shared libraries: libaio.so.1...请执行 yum install libaio后再执行初始化

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
	Type=notify
	User=mysql
	Group=mysqlgroup
	# Disable service start and stop timeout logic of systemd for mysqld service.
	TimeoutSec=0
	# Execute pre and post scripts as root
	PermissionsStartOnly=true
	# Start main service
	# 注意这里不要加 --daemonize 
	ExecStart=/mysql/mysql-8.0.18/bin/mysqld --defaults-file=/mysql/mysql-8.0.18/my.cnf --user=mysql --pid-file=/var/run/mysql/mysqld.pid --socket=/tmp/mysql.sock	
	PIDFile=/var/run/mysql/mysqld.pid
	# Use this to switch malloc implementation
	#EnvironmentFile=-/etc/sysconfig/mysql
	# Sets open_files_limit
	LimitNOFILE = 5000
	Restart=on-failure
	RestartPreventExitStatus=1
	PrivateTmp=false

#### 设置root密码

	ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new#Pwd111'; -- 8.0+版本密码必须要包含符号

#### 开启远程访问
	USE mysql
	UPDATE USER SET Host='%' WHERE User='root';
	FLUSH PRIVILEGES;

#### 修改密码插件（解决 plugin caching_sha2_password could not be loaded）
	alter user 'root'@'%' identified with  mysql_native_password by  'new#Pwd111' password expire never;
	flush privileges;

