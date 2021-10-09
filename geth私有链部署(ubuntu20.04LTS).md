## geth私有链部署(ubuntu20.04LTS)
### 创建目录
    mkdir /geth && chmod 755 /geth
    mkdir /geth/private-chain
### 下载golang
    wget https://golang.org/dl/go1.17.2.linux-amd64.tar.gz /geth/golang
    tar -xzf /geth/golang/go1.17.2.linux-amd64.tar.gz -C /geth 
    export PATH=$PATH:/geth/go/bin

    //设置go代理(可选)
    go env -w GOPROXY=https://goproxy.cn 
### 安装git & 克隆go-ethereum源代码
    apt-get install git
    git clone https://github.com/ethereum/go-ethereum.git /geth/go-ethereum
### 编译go-ethereum
    make -C /geth/go-ethereum/ geth
    //查看geth版本
    /geth/go-ethereum/build/bin/geth version
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
        "difficulty": "629145",
        "gasLimit": "80000000",
        "alloc": {
        "5948AA49f236a063E24EA42853c989E7055BDf81": { "balance": "1000000000000000000000000000000000" }
        }
    }
### 编辑config文件
    vi /geth/config.toml

    //config.toml内容
    [Eth]
    NetworkId = 555

    [Eth.TxPool]
    NoLocals = true
    PriceLimit = 1000000000 # tx入池的最小gasprice(wei)
    AccountQueue = 1024
    GlobalQueue = 10240

    [Eth.Miner]
    Etherbase = "0x5948AA49f236a063E24EA42853c989E7055BDf81" # 矿工地址
    GasPrice = 1000000000
    GasFloor = 1
    GasCeil = 80000000

    [Node]
    DataDir = '/geth/private-chain'
    HTTPHost = '0.0.0.0'
    HTTPPort = 8545
    HTTPModules = ["net", "web3", "eth", "debug"]
    HTTPCors = ["*"]
    WSPort = 8546
    WSModules = ["net", "web3", "eth", "debug"]

    [Node.P2P]
    MaxPeers = 25
    NoDiscovery = true # 单节点设置为不启用节点发现

    [Node.HTTPTimeouts]
    ReadTimeout = 30000000000
    WriteTimeout = 30000000000
    IdleTimeout = 120000000000

### 初始化
     /geth/go-ethereum/build/bin/geth init --datadir /geth/private-chain /geth/genesis.json
### 启动
    //--mine 为启动挖矿
    //--miner.threads=4 为使用cpu 4个线程挖矿
    /geth/go-ethereum/build/bin/geth --config /geth/config.toml --mine --miner.threads=4 console 2>>/geth/private-chain/geth.log     

### 参考
- https://geth.ethereum.org/docs/getting-started
- https://github.com/ethereum/go-ethereum/blob/master/cmd/geth/config.go#L88