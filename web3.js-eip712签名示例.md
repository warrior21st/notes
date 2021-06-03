## web3.js调用钱包进行eip712签名
### javascript code(for uniswap-v2 removeLiquidityWithPermit)
    var userAddress = window.ethereum.selectedAddress;
    var uniV2Router02="0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D";
    var uniV2Pair="0x3f2DD184aB2d93ab3BfCbBe477d234B04A777911";
    var rinkebyChainId=4;
    var datas={
                "types":{
                    "EIP712Domain":[
                        {
                            "name":"name",
                            "type":"string"
                        },
                        {
                            "name":"version",
                            "type":"string"
                        },
                        {
                            "name":"chainId",
                            "type":"uint256"
                        },
                        {
                            "name":"verifyingContract",
                            "type":"address"
                        }
                    ],
                    "Permit":[
                        {
                            "name":"owner",
                            "type":"address"
                        },
                        {
                            "name":"spender",
                            "type":"address"
                        },
                        {
                            "name":"value",
                            "type":"uint256"
                        },
                        {
                            "name":"nonce",
                            "type":"uint256"
                        },
                        {
                            "name":"deadline",
                            "type":"uint256"
                        },
                    ]
                },
                "primaryType":"Permit",
                "domain":{
                    "name":"Uniswap V2",
                    "version":"1",
                    "chainId":rinkebyChainId,
                    "verifyingContract":uniV2Pair
                },
                "message":{
                    "owner":userAddress,
                    "spender":uniV2Router02,
                    "value":359496430,
                    "nonce":3,
                    "deadline":1622727976
                }
            };

    web3.currentProvider.sendAsync({
        method:'eth_signTypedData_v4',
        params:[userAddress,JSON.stringify(datas)]
    }, function (err, result) {
        if (result.error) {
            console.log(err);
        } else {
            console.log(result.result);
            //0xa253144ce409023b3a05a45365e88316ac02f1fd27d6dcd506778682737534b865156889d623aee9caaf3d393a94585e0d8eeb3a184b94b0f3ab2ce44b2ff6c61c
        }
    });

### 合约验证代码(UniswapV2ERC20.sol)
    bytes32 public PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
    bytes32 public DOMAIN_SEPARATOR= keccak256(
            abi.encode(
                keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)'),
                keccak256(bytes(name)),
                keccak256(bytes('1')),
                chainId,
                address(this)
            )
        );

    function permit(address owner, address spender, uint value, uint deadline, uint8 v, bytes32 r, bytes32 s) external {
        require(deadline >= block.timestamp, 'UniswapV2: EXPIRED');
        bytes32 digest = keccak256(
            abi.encodePacked(
                '\x19\x01',
                DOMAIN_SEPARATOR,
                keccak256(abi.encode(PERMIT_TYPEHASH, owner, spender, value, nonces[owner]++, deadline))
            )
        );
        address recoveredAddress = ecrecover(digest, v, r, s);
        require(recoveredAddress != address(0) && recoveredAddress == owner, 'UniswapV2: INVALID_SIGNATURE');
    }

#### 参考
- https://eips.ethereum.org/EIPS/eip-712
- https://github.com/Uniswap/uniswap-v2-core/blob/master/contracts/UniswapV2ERC20.sol