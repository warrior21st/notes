## geth私有链部署(ubuntu20.04LTS)
### 创建目录
    mkdir /geth && chmod 755 /geth
    mkdir /geth/private-chain
### 下载golang
    wget https://golang.org/dl/go1.17.2.linux-amd64.tar.gz -P /geth
    tar -xzf /geth/go1.17.2.linux-amd64.tar.gz -C /geth 
    export PATH=$PATH:/geth/go/bin

    //设置go代理(可选)
    go env -w GOPROXY=https://goproxy.cn 
### 克隆go-ethereum代码
    //安装git & 克隆go-ethereum源代码
    apt-get install git
    git clone -b v1.10.9 https://github.com/ethereum/go-ethereum.git /geth/go-ethereum

### 修改挖矿难度计算逻辑
    vi /geth/go-ethereum/consensus/ethash/consensus.go

    //将
    func CalcDifficulty(config *params.ChainConfig, time uint64, parent *types.Header) *big.Int {
        next := new(big.Int).Add(parent.Number, big1)
        switch {
        case config.IsCatalyst(next):
            return big.NewInt(1)
        case config.IsLondon(next):
            return calcDifficultyEip3554(time, parent)
        case config.IsMuirGlacier(next):
            return calcDifficultyEip2384(time, parent)
        case config.IsConstantinople(next):
            return calcDifficultyConstantinople(time, parent)
        case config.IsByzantium(next):
            return calcDifficultyByzantium(time, parent)
        case config.IsHomestead(next):
            return calcDifficultyHomestead(time, parent)
        default:
            return calcDifficultyFrontier(time, parent)
        }
    }
    //修改为
    func CalcDifficulty(config *params.ChainConfig, time uint64, parent *types.Header) *big.Int {
        //always use genesis block's difficulty
        return parent.Difficulty
	}

### 编译
    //编译go-ethereum
    make -C /geth/go-ethereum/ geth

    //查看geth版本
    /geth/go-ethereum/build/bin/geth version

<!-- #### 方式2：下载geth可执行文件（稳定版）
    mkdir -p /geth/go-ethereum/build/bin

    wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.10.9-eae3b194.tar.gz -P /geth/go-ethereum/build/bin

    tar -xzf /geth/go-ethereum/build/bin/geth-linux-amd64-1.10.9-eae3b194.tar.gz -C /geth/go-ethereum/build/bin

    mv /geth/go-ethereum/build/bin/geth-linux-amd64-1.10.9-eae3b194/* /geth/go-ethereum/build/bin
    
    rm -rf /geth/go-ethereum/build/bin/geth-linux-amd64-1.10.9-eae3b194 -->

### 编辑创世块配置文件
    vi /geth/genesis.json

    //genesis.json内容
    {
        "config": {
        "chainId": 555,
        "homesteadBlock": 0,
        "eip150Block": 0,
        "eip155Block": 0,
        "eip158Block": 0,
        "byzantiumBlock": 0,
        "constantinopleBlock": 0,
        "petersburgBlock": 0,
        "ethash": {}
        },
        "difficulty": "0xe6665",
        "gasLimit": "80000000",
        "alloc": {
            "5948AA49f236a063E24EA42853c989E7055BDf81": { "balance": "1000000000000000000000000000000000" }
        },
        "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "timestamp": "0x00"
    }
### 编辑config文件
    vi /geth/config.toml

    //config.toml内容
    [Eth]
    NetworkId = 555

    [Eth.TxPool]
    NoLocals = true
    PriceLimit = 1000000000
    AccountQueue = 1024
    GlobalQueue = 10240

    [Eth.Miner]
    Etherbase = "0x5948AA49f236a063E24EA42853c989E7055BDf81"
    GasPrice = 1000000000
    GasFloor = 1
    GasCeil = 80000000

    [Node]
    DataDir = '/geth/private-chain'
    HTTPHost = 'localhost'
    HTTPPort = 5055
    HTTPModules = ["net", "web3", "eth", "debug"]
    HTTPCors = ["*"]
    WSPort = 5056
    WSModules = ["net", "web3", "eth", "debug"]

    [Node.P2P]
    MaxPeers = 25
    NoDiscovery = true

    [Node.HTTPTimeouts]
    ReadTimeout = 30000000000
    WriteTimeout = 30000000000
    IdleTimeout = 120000000000

### 初始化
     /geth/go-ethereum/build/bin/geth init --datadir /geth/private-chain /geth/genesis.json
### 启动
    //--mine 为启动挖矿
    //--miner.threads=4 为使用4个cpu线程挖矿
    /geth/go-ethereum/build/bin/geth --config /geth/config.toml --mine --miner.threads=4 console 2>>/geth/private-chain/geth.log 

### 使用systemd启动
    //geth.service内容
    [Unit]	
    Description=geth
    After=network.target

    [Service]
    Type=simple	
    ExecStart=/geth/go-ethereum/build/bin/geth --config /geth/config.toml --mine --miner.threads=4
    TimeoutStopSec=90
    Restart=on-failure
    RestartSec=10s
    User=root
    SyslogIdentifier=geth
    # StandardOutput=append:/geth/private-chain/geth_std_output.log
    # StandardError=append:/geth/private-chain/geth_output.log

    [Install]	
    WantedBy=multi-user.target    

### nginx代理配置(nginx.conf)
    user  nginx;
    worker_processes  auto;

    # error_log  /var/log/nginx/error.log notice;
    # pid        /var/run/nginx.pid;

    events {
        worker_connections  1024;
    }

    http {
        map $http_upgrade $connection_upgrade {
            default upgrade;
            ''   close;
        }

        sendfile off;
        keepalive_timeout 65; # Adjust to the lowest possible value that makes sense for your use case.
        client_body_timeout 10; client_header_timeout 10; send_timeout 10;

        server {
            listen 8045;

            location / {
                proxy_pass  http://localhost:8045;
            }
        }

        server {
            listen 8046;

            location / {
                proxy_pass  http://localhost:8046;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection $connection_upgrade;
            }
        }
    }

### 参考
- 官方文档：https://geth.ethereum.org/docs/getting-started
- 源代码中的配置项定义：https://github.com/ethereum/go-ethereum/blob/master/cmd/geth/config.go#L88