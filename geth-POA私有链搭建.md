# geth POA

### 下载geth文件
    sudo mkdir /opt/geth_private
    sudo chown ubuntu /opt/geth_private
    cd /opt/geth_private
    mkdir ./geth
    mkdir ./geth/data
    mkdir ./clef
    mkdir ./clef/config

    wget https://gethstore.blob.core.windows.net/builds/geth-alltools-linux-amd64-1.10.26-e5eb32ac.tar.gz
    tar -zxvf geth-alltools-linux-amd64-1.10.26-e5eb32ac.tar.gz
    cp ./geth-alltools-linux-amd64-1.10.26-e5eb32ac/geth ./geth/
    cp ./geth-alltools-linux-amd64-1.10.26-e5eb32ac/clef ./clef/

### 创建地址
    cd /opt/geth_private/geth/
    ./geth account new --datadir=./data

    // 建议创建好之后立即备份地址0的keystore和密码

### 创建`genesis.json`
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

### 初始化
    ./geth init --datadir ./data ./genesis.json

### 初始化clef
    cd /opt/geth_private/clef/
    ./clef --keystore /opt/geth_private/geth/data/keystore --configdir ./config --chainid 20000 --suppress-bootwarn init

    // 初始化完成之后需要立即备份 config/masterseed.json 和 master密码

### 将keystore存储到clef
    ./clef --keystore /opt/geth_private/geth/data/keystore --configdir ./config --chainid 20000 --suppress-bootwarn setpw {地址0}

### 创建clef规则文件`rules.js`
    function OnSignerStartup(info) {}

    function ApproveListing() {
        return 'Approve';
    }

    function ApproveSignData(r) {
        if (r.content_type == 'application/x-clique-header') {
            for (var i = 0; i < r.messages.length; i++) {
            var msg = r.messages[i];
            if (msg.name == 'Clique header' && msg.type == 'clique') {
                return 'Approve';
            }
            }
        }
        return 'Reject';
    }

### 更新clef规则文件md5
    ./clef --keystore /opt/geth_private/geth/data/keystore --configdir ./config --chainid 20000 --suppress-bootwarn  attest  `sha256sum rules.js | cut -f1`

### 启动clef
    ./clef --keystore /opt/geth_private/geth/data/keystore --configdir ./config --chainid 20000 --suppress-bootwarn --nousb --rules ./rules.js
    
### 创建`config.toml`
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
    DataDir = '/opt/geth_private/geth/data'
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
    ./geth --config /opt/geth_private/geth/config.toml --signer /opt/geth_private/clef/config/clef.ipc --mine --mine --miner.threads=1 console

    // 不安全（可不配置clef）
    ./geth --config /opt/geth_private/geth/config.toml --unlock "account0" --password="pwd.txt" --allow-insecure-unlock --mine --miner.threads=1 console

### 参考
- https://geth.ethereum.org/docs/getting-started
- clef: https://geth.ethereum.org/docs/tools/clef/clique-signing
- geth private networks: https://geth.ethereum.org/docs/fundamentals/private-network
