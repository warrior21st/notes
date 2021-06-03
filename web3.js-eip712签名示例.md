## web3.js调用钱包进行eip712签名
### uniswap-v2 removeLiquidityWithPermit sample code
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

#### 参考
- https://eips.ethereum.org/EIPS/eip-712