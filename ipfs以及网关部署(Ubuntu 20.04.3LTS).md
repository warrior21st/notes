## ipfs以及网关部署(Ubuntu 20.04.3LTS)
### 下载golang
    wget https://golang.org/dl/go1.17.2.linux-amd64.tar.gz -P /geth
    tar -xzf /geth/go1.17.2.linux-amd64.tar.gz -C /geth 
    export PATH=$PATH:/geth/go/bin

    //设置go代理(可选)
    go env -w GOPROXY=https://goproxy.cn 
### 安装go-ipfs
    wget https://dist.ipfs.io/go-ipfs/v0.10.0/go-ipfs_v0.10.0_linux-amd64.tar.gz

    tar -xvzf go-ipfs_v0.10.0_linux-amd64.tar.gz

    cd go-ipfs

    sudo bash install.sh

    //查看ipfs version
    ipfs --version

### 初始化
    ipfs init
### 使用systemd管理ipfs
    //ipfs.service内容
    [Unit]
    Description=IPFS daemon
    After=network.target

    [Service]
    ExecStart=/usr/bin/ipfs daemon

    [Install]
    WantedBy=multiuser.target

### nginx.conf
    #user  nobody;
    worker_processes  auto;

    events {
        worker_connections  1024;
    }

    http {
        include       mime.types;
        default_type  application/octet-stream;

        sendfile        on;
        keepalive_timeout  65;

        gzip  on;

        server {
            listen       1001;

            location /ipfs {
                proxy_pass http://127.0.0.1:8080/ipfs;
            }

            error_page  404              /404.html;
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }
    }