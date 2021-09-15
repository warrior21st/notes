## Ubuntu 20.04.3 LTS graph-node搭建
### 安装docker
    sudo apt-get remove docker docker-engine docker.io containerd runc

    sudo apt-get update

    sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    echo \
    "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt-get update

    sudo apt-get install docker-ce docker-ce-cli containerd.io
### 安装docker-compose
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

    sudo chmod +x /usr/local/bin/docker-compose

    sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
    
    docker-compose --version

    //或
    apt-get install docker-compose

### 安装git & 克隆仓库
    apt-get install git

    git clone https://github.com/graphprotocol/graph-node.git /root/graph-node

### 修改docker-compose.yml
    //以heco为例，将 ethereum: 'mainnet:http://host.docker.internal:8545'    
    //改为：ethereum: 'heco:https://pub002.hg.network/rpc'
    //注意：rpc节点必须是归档节点（`syncmode`为`full`,`gcmode`为`archive`）

### 初始化容器
    docker-compose -f /root/graph-node/docker/docker-compose.yml up --no-start

### 启动容器
    docker-compose -f /root/graph-node/docker/docker-compose.yml start

### 关闭容器
    docker-compose -f /root/graph-node/docker/docker-compose.yml stop

### graph-node api
    //create
    graph create [username]/[subgraph name] --node http://127.0.0.1:8020

    //deploy
    graph deploy [username]/[subgraph name] --debug --ipfs http://127.0.0.1:5001 --node http://127.0.0.1:8020    

    //query
    http://127.0.0.1:8000/subgraphs/name/[username]/[subgraph name]

### 说明
    //ipfs 端口：5001
    //ipfs webui: http://127.0.0.1:5001/webui
    //ipfs 数据保存位置：/data/ipfs

    //postgresql 端口：5432
    //postgresql 数据保存位置：/var/lib/postgresql/data

    //graph-node 创建/部署端口：8020
    //graphql 端口：8000

### systemd管理
    [Unit]	
    Description=graph-node-heco	

    [Service]	
    WorkingDirectory=/root/graph-node/docker
    ExecStart=/usr/bin/docker-compose -f /root/graph-node/docker/docker-compose.yml start
    ExecStop=/usr/bin/docker-compose -f /root/graph-node/docker/docker-compose.yml stop
    Restart=on-failure	
    # Restart service after 10 seconds if dotnet service crashes	
    RestartSec=10  
    SyslogIdentifier=graph-node-heco
    User=root	

    [Install]	
    WantedBy=multi-user.target

### 参考
- docker安装：https://docs.docker.com/engine/install/ubuntu/
- docker-compose安装：https://docs.docker.com/compose/install/
- docker方式启动graph-node：https://github.com/graphprotocol/graph-node/blob/v0.24.0/docker/README.md