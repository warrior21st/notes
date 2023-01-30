# geth POA

### 下载geth文件
    mkdir ./geth
    cd ./geth
    wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.10.26-e5eb32ac.tar.gz
    tar -zxvf geth-linux-amd64-1.10.26-e5eb32ac.tar.gz
    mv ./geth-linux-amd64-1.10.26-e5eb32ac/geth ./
    mkdir ./data

### 创建地址
    geth account new --datadir=./data

### `genesis.json`
    {
        "config": {
            "chainId": 20000,
            "homesteadBlock": 0,
            "eip150Block": 0,
            "eip155Block": 0,
            "eip158Block": 0,
            "byzantiumBlock": 0,
            "constantinopleBlock": 0,
            "petersburgBlock": 0,
            "istanbulBlock": 0,
            "berlinBlock": 0,
            "londonBlock": 0,
            "clique": {
                    "period": 1, // 多少秒产一个块
                    "epoch": 30000
                }
        },
        "nonce":"0x00",
        "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "coinbase": "0x0000000000000000000000000000000000000000",
        "difficulty": "1",
        "gasLimit": "99999999",
        "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000{account0 without 0x}0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "timestamp": "0x00",
        "alloc": {
            "{address with out 0x}": { "balance": "1000000000000000000000000000000000" }
        }
    }

### `config.toml`
    [Eth]
    NetworkId = 20000

    [Eth.TxPool]
    NoLocals = true
    PriceLimit = 1000000000 // 入池最低gas price
    AccountQueue = 1024
    GlobalQueue = 10240

    [Eth.Miner]
    Etherbase = "{account0}"
    GasPrice = 900000000 // 执行交易的最低gas price
    GasFloor = 99999999
    GasCeil = 99999999

    [Node]
    DataDir = '/opt/geth/data'
    HTTPHost = '0.0.0.0'
    HTTPPort = 5001
    HTTPModules = ["net", "web3", "eth", "debug"]
    HTTPCors = ["*"]
    WSPort = 5002
    WSModules = ["net", "web3", "eth", "debug"]

    [Node.P2P]
    MaxPeers = 25
    NoDiscovery = true

    [Node.HTTPTimeouts]
    ReadTimeout = 30000000000
    WriteTimeout = 30000000000
    IdleTimeout = 120000000000

### 启动
    ./geth --config /opt/geth/config.toml --unlock "account0" --password="pwd.txt" --allow-insecure-unlock --mine --miner.threads=1 console