#### 安装dotnet core

见https://www.microsoft.com/net/learn/get-started/linuxcentos

#### 创建service

	vi /etc/systemd/system/xxx.service

内容如下

	[Unit]	
	Description=xxx	

	[Service]	
	WorkingDirectory=/root/wwwroot/xxx.x	
	ExecStart=/usr/bin/dotnet /root/wwwroot/xxx.x/xxx.dll	
	Restart=always	
	# Restart service after 10 seconds if dotnet service crashes	
	RestartSec=10  
	SyslogIdentifier=dotnet-xxx	
	User=root	
	Environment=ASPNETCORE_ENVIRONMENT=Production 	

	[Install]	
	WantedBy=multi-user.target

#### 启用service

	systemctl enable xxx.service

#### 运行service

	systemctl start xxx.service

#### 停止service

	systemctl stop xxx.service

#### 查看服务状态

	systemctl status xxx.service
