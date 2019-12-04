#### 下载并解压mysql

	cd /usr/local
	mkdir mysql
	cd mysql
	wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-linux-glibc2.12-x86_64.tar.gz
	tar zxf mysql-5.7.19-linux-glibc2.12-x86_64.tar.gz -C ./
	mkdir ./mysql-5.7.19-linux-glibc2.12-x86_64/data

#### 添加用户和组并赋予所有权

	groupadd mysql
	useradd -r -g mysql mysql
	cd mysql-5.7.19-linux-glibc2.12-x86_64
	chown -R mysql:mysql ./

#### 安装libaio

	yum install libaio*

#### 初始化
	./bin/mysqld --user=mysql --basedir=/usr/local/mysql/mysql-5.7.19-linux-glibc2.12-x86_64 --datadir=/usr/local/mysql/mysql-5.7.19-linux-glibc2.12-x86_64/data --initialize

- 如果报error while loading shared libraries: libaio.so.1...请执行 yum install libaio后再执行初始化

#### 将mysql添加到服务

 	cp support-files/mysql.server /etc/init.d/mysqld

#### 启动mysql

 	service mysqld start

如果报my_print_defaults: command not found，执行 vi /etc/my.cnf改为如下：

	[mysqld]
	basedir=/usr/local/mysql/mysql-5.7.19-linux-glibc2.12-x86_64
	datadir=/usr/local/mysql/mysql-5.7.19-linux-glibc2.12-x86_64/data
	query_cache_size = 100M
	query_cache_type = ON
	lower_case_table_names=1
	socket=/tmp/mysql.sock
	symbolic-links=0
	
	[mysqld_safe]
	log-error=/var/log/mariadb/mariadb.log
	pid-file=/var/run/mariadb/mariadb.pid
	!includedir /etc/my.cnf.d

- /var/log/mariadb/和/var/run/mariadb/如果不存在则创建且赋予mysql这个用户所有权

#### 设置root密码

	SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');

#### 开启远程访问

	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
	FLUSH PRIVILEGES;
