#### 安装dotnet core

见https://www.microsoft.com/net/learn/get-started/linuxcentos

#### 创建service

	vi /etc/systemd/system/zkor.service

内容如下

	[Unit]	
	Description=Zkor.COM	

	[Service]	
	WorkingDirectory=/root/wwwroot/zkor2.x	
	ExecStart=/usr/bin/dotnet /root/wwwroot/zkor2.x/Zkor.COM.dll	
	Restart=always	
	# Restart service after 10 seconds if dotnet service crashes	
	RestartSec=10  
	SyslogIdentifier=dotnet-zkor	
	User=root	
	Environment=ASPNETCORE_ENVIRONMENT=Production 	

	[Install]	
	WantedBy=multi-user.target

#### 启用service

	systemctl enable zkor.service

#### 运行service

	systemctl start zkor.service

#### 停止service

	systemctl stop zkor.service

#### 查看服务状态

	systemctl status zkor.service
