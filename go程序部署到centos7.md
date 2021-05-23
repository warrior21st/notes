## go程序部署到centos7
### 下载go&安装go环境
    wget https://golang.org/dl/go1.16.4.linux-amd64.tar.gz
    rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.4.linux-amd64.tar.gz
    export PATH=$PATH:/usr/local/go/bin
    go version
### 编译
    //-o为指定输出文件名
    cd /root/goservices/blockscanner-code/src
    go build -o /root/goservices/blockscanner/scanner

### systemd管理
    [Unit]	
    Description=blockscanner-scanner

    [Service]	
    WorkingDirectory=/root/goservices/blockscanner
    ExecStart=/root/goservices/blockscanner/scanner
    Restart=always	
    RestartSec=10  # Restart service after 10 seconds if dotnet service crashes	
    SyslogIdentifier=blockscanner-scanner	
    User=root 	

    [Install]	
    WantedBy=multi-user.target

### 参考
- 环境安装：https://golang.org/doc/install?download=go1.16.4.linux-amd64.tar.gz#download
- 编译：http://c.biancheng.net/view/5725.html