# Test API of hummingbot v1.8.0-xdc

[toc]

## preparations

### 1. Install dependent packages

```shell
sudo apt install -y curl httpie jq
```

![1665380461924](https://user-images.githubusercontent.com/7695325/194804452-4490db01-2ac8-4011-a46d-2b7086cc2706.png)

httpie is recommended to send https request in our test cases. You can use curl instead of httpie.

### 2. Set environment variables

Set the following environment variables according to the actual situation:

```shell
# address of gateway, maybe different in your environment
export SERVER="https://127.0.0.1:15888"

# POST command use JSON format
export HEADER="Content-Type: application/json"

# public key of test account
export ETH_ADDRESS="0xD4CE02705041F04135f1949Bc835c1Fe0885513c"
export XDC_ADDRESS="xdcD4CE02705041F04135f1949Bc835c1Fe0885513c"

# path of certs
CERT=$(ls -Frt ${HOME}/.hummingbot-gateway | grep -E '^hummingbot-gateway-[0-9a-z]{8}/$' | tail -n 1)
export CERTS_PATH="${HOME}/.hummingbot-gateway/${CERT:0:-1}/certs"
export GATEWAY_CERT="${CERTS_PATH}/client_cert.pem"
export GATEWAY_KEY="${CERTS_PATH}/client_key.pem"
echo "GATEWAY_CERT=${GATEWAY_CERT}"
echo "GATEWAY_KEY=${GATEWAY_KEY}"
```

![1665380778799](https://user-images.githubusercontent.com/7695325/194804904-e5c53b7a-a085-4163-8ce5-ebf3425c741f.png)

### 3. Config httpie

Remember to delete this file after test.

```shell
mkdir -p ${HOME}/.config/httpie

cat > ${HOME}/.config/httpie/config.json <<EOF
{
    "default_options": [
        "--verify=no",
        "--cert=${GATEWAY_CERT}",
        "--cert-key=${GATEWAY_KEY}"
    ]
}

EOF

# check file content
cat ${HOME}/.config/httpie/config.json
```

![1665381018492](https://user-images.githubusercontent.com/7695325/194805206-19d9cdf2-7b6b-4631-85aa-fb249f67deab.png)

### 4. tokens for test

-   I created some test tokens on apothem blockchain with [factory contract 0xfD9F33ab143b1717D4784A420eE5A93A8CbBcABd](https://explorer.apothem.network/address/xdcfD9F33ab143b1717D4784A420eE5A93A8CbBcABd#readContract). You can create new tokens, and mint any quantity to any account.

| Symbol | Decimals | Address                                                                                                                                         | price |
| :----- | -------: | :---------------------------------------------------------------------------------------------------------------------------------------------- | ----: |
| WBTC2  |        8 | [0x01B0500f82EF188D0410a46f2E8940133E213e83](https://explorer.apothem.network/address/xdc01b0500f82ef188d0410a46f2e8940133e213e83#readContract) | 20000 |
| YFI2   |       18 | [0x22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d](https://explorer.apothem.network/address/xdc22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d#readContract) |  8000 |
| MKR2   |       18 | [0x258E445fEf3F41429e38ee124DA63aBfb08edc70](https://explorer.apothem.network/address/xdc258E445fEf3F41429e38ee124DA63aBfb08edc70#readContract) |   600 |
| AAVE2  |       18 | [0x3042207876c47D3c206df99b3279d97813B34Ea1](https://explorer.apothem.network/address/xdc3042207876c47D3c206df99b3279d97813B34Ea1#readContract) |    70 |
| UNI2   |       18 | [0xD9e33607d06cBB1Fef59488b9969426b10F310B8](https://explorer.apothem.network/address/xdcD9e33607d06cBB1Fef59488b9969426b10F310B8#readContract) |     5 |
| USDC2  |        6 | [0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389](https://explorer.apothem.network/address/xdcF83B9Dc502A3F76c042b4043B6C1B5eBBE574389#readContract) |     1 |

For each token:

-   mint 1000000000000000000000000000000000000 to ${ETH_ADDRESS}
-   approve 1000000000000000000000000000000000000 to spender: [XdcSwap 0x49dc40FC5708376Cff6De423561cD5a9176FA8BA](https://explorer.apothem.network/address/xdc49dc40FC5708376Cff6De423561cD5a9176FA8BA#readContract)

### 5. Trade pools in XdcSwap

You can call function addLiquidity to create trade pools. I have created 3 pools:

#### 5.1 WBTC2 / USDC2

-   tokenA: 0x01B0500f82EF188D0410a46f2E8940133E213e83
-   tokenB: 0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389
-   amountADesired: 110000000000000000
-   amountBDesired: 22000000000000000000
-   amountAMin: 100000000000000000
-   amountBMin: 20000000000000000000
-   to: 0x85f33E1242d87a875301312BD4EbaEe8876517BA
-   deadline: 115792089237316195423570985008687907853269984665640564039457584007913129639935

#### 5.2 YFI2 / USDC2

-   tokenA: 0x22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d
-   tokenB: 0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389
-   amountADesired: 1100000000000000000000000000
-   amountBDesired: 8800000000000000000
-   amountAMin: 1000000000000000000000000000
-   amountBMin: 8000000000000000000
-   to: 0x85f33E1242d87a875301312BD4EbaEe8876517BA
-   deadline: 115792089237316195423570985008687907853269984665640564039457584007913129639935

#### 5.3 MKR2 / USDC2

-   tokenA: 0x258E445fEf3F41429e38ee124DA63aBfb08edc70
-   tokenB: 0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389
-   amountADesired: 1100000000000000000000000000
-   amountBDesired: 660000000000000000
-   amountAMin: 1000000000000000000000000000
-   amountBMin: 600000000000000000
-   to: 0x85f33E1242d87a875301312BD4EbaEe8876517BA
-   deadline: 115792089237316195423570985008687907853269984665640564039457584007913129639935

## Test cases

### 1. query gateway status

```shell
# curl -s -X GET -k --key ${GATEWAY_KEY} --cert ${GATEWAY_CERT} ${SERVER} | jq
https ${SERVER}
```

![1665381110082](https://user-images.githubusercontent.com/7695325/194805324-568809bc-7351-4043-b2e6-027a2e316777.png)

### 2. query network config

```shell
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/config" | jq
https ${SERVER}/network/config
```

![1665381248109](https://user-images.githubusercontent.com/7695325/194805532-fa31070e-c000-4f3b-a5ce-ee24ca809683.png)

### 3. query connectors

```shell
# curl -s -X GET -k --key ${GATEWAY_KEY} --cert ${GATEWAY_CERT} ${SERVER}/connectors | jq
https ${SERVER}/connectors
```

![1665381363116](https://user-images.githubusercontent.com/7695325/194805717-fa66a212-db04-47c3-9387-470950301762.png)

### 4. query network status

```shell
# xinfin
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=xdc&network=xinfin" | jq
https ${SERVER}/network/status chain==xdc network==xinfin

# apothem
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=xdc&network=apothem" | jq
https ${SERVER}/network/status chain==xdc network==apothem

# ethereum
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=ethereum&network=mainnet" | jq
https ${SERVER}/network/status chain==ethereum network==mainnet

# polygon
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=polygon&network=mainnet" | jq
https ${SERVER}/network/status chain==polygon network==mainnet

# mumbai
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=polygon&network=mumbai" | jq
https ${SERVER}/network/status chain==polygon network==mumbai

# all networks
# curl -s -X GET -k --key $GATEWAY_KEY --cert "$GATEWAY_CERT" "${SERVER}/network/status" | jq
https ${SERVER}/network/status
```

![1665382026407](https://user-images.githubusercontent.com/7695325/194806773-b92bb5e2-d51a-4edb-81ad-2ee52e1d9c79.png)
![1665381657854](https://user-images.githubusercontent.com/7695325/194806166-9aeb84ed-da1a-42de-8e41-2dad95432b0f.png)

### 5. fetch token list

```shell
# xinfin
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=xdc&network=xinfin" | jq
https ${SERVER}/network/tokens chain==xdc network==xinfin

# apothem
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=xdc&network=apothem" | jq
https ${SERVER}/network/tokens chain==xdc network==apothem

# ethereum
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=ethereum&network=mainnet" | jq
https ${SERVER}/network/tokens chain==ethereum network==mainnet

# polygon
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=polygon&network=mainnet" | jq
https ${SERVER}/network/tokens chain==polygon network==mainnet

# mumbai
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=polygon&network=mumbai" | jq
https ${SERVER}/network/tokens chain==polygon network==mumbai
```

![1665381735138](https://user-images.githubusercontent.com/7695325/194806278-4acfba5c-1458-4607-bb9a-727c5073b603.png)
![1665381818732](https://user-images.githubusercontent.com/7695325/194806411-caf24e0b-f7fc-470b-bcf8-c5c9290b3af0.png)
![1665381889596](https://user-images.githubusercontent.com/7695325/194806559-a20f7ff1-5333-45cf-b0c4-5a3c5e5e1817.png)

### 6. query transaction

#### 6.1 If use curl

```shell
# xinfin
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "txHash": "0xf49e741720e3f6c464e7bfbdcaefddb6f9a4cc39d4b367476727e95be735b350"
}' | jq

# apothem
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "xdc",
    "network": "apothem",
    "txHash": "0x1114601d48ebc3afa5e06af13e759560e74b3d5d1837d951919bc57727a623dd"
}' | jq

# ethereum
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "ethereum",
    "network": "mainnet",
    "txHash":"0xafd9365584cb2938dbf258e1f3e96dbc0794c5b30168f904b90296a23848632e"
}' | jq

# polygon
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "polygon",
    "network": "mainnet",
    "txHash": "0xfc5b36a77417915891c3182900a016748e9e730d50058fbe09126ee20c213479"
}' | jq

# mumbai
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "polygon",
    "network": "mumbai",
    "txHash":"0x2c50b52369f23763ec635cc505f970af367c6e81788522c81ca6184d106cc38f"
}' | jq
```

#### 6.2 If use httpie

```shell
# xinfin
https POST ${SERVER}/network/poll chain=xdc network=xinfin txHash=0xf49e741720e3f6c464e7bfbdcaefddb6f9a4cc39d4b367476727e95be735b350

# apothem
https POST ${SERVER}/network/poll chain=xdc network=apothem txHash=0x1114601d48ebc3afa5e06af13e759560e74b3d5d1837d951919bc57727a623dd

# ethereum
https POST ${SERVER}/network/poll chain=ethereum network=mainnet txHash=0xafd9365584cb2938dbf258e1f3e96dbc0794c5b30168f904b90296a23848632e

# polygon
https POST ${SERVER}/network/poll chain=polygon network=mainnet txHash=0xfc5b36a77417915891c3182900a016748e9e730d50058fbe09126ee20c213479

# mumbai
https POST ${SERVER}/network/poll chain=polygon network=mumbai txHash=0x2c50b52369f23763ec635cc505f970af367c6e81788522c81ca6184d106cc38f
```

![1665382299452](https://user-images.githubusercontent.com/7695325/194807163-89b72386-62b6-4a62-a085-717143b0cb30.png)
![1665382392522](https://user-images.githubusercontent.com/7695325/194807291-03438647-9a13-4931-b8cd-53e601bdb12a.png)

### 7 Add private key

The length of private key is 67 characters with `xdc` prefix or 66 characters with `0x` prefix, such as:

-   xdc prefix: xdc0123456789012345678901234567890123456789012345678901234567890123
-   0x prefix: 0x0123456789012345678901234567890123456789012345678901234567890123

Remember to execute `unset ETH_PRIVATE_KEY` and `unset XDC_PRIVATE_KEY` for security after test.

```shell
# export ETH_PRIVATE_KEY="0x0123456789012345678901234567890123456789012345678901234567890123"
export ETH_PRIVATE_KEY="<YOUR_ETH_PRIVATE_KEY>"
# export XDC_PRIVATE_KEY="xdc0123456789012345678901234567890123456789012345678901234567890123"
export XDC_PRIVATE_KEY="<YOUR_XDC_PRIVATE_KEY>"
```

#### 7.1 If use curl

```shell
# xinfin
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/wallet/add" -d '{
    "privateKey": "'"${XDC_PRIVATE_KEY}"'",
    "chain": "xdc",
    "network":"xinfin"
}' | jq

# apothem
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/wallet/add" -d '{
    "privateKey": "'"${XDC_PRIVATE_KEY}"'",
    "chain": "xdc",
    "network":"apothem"
}' | jq

# ethereum
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/wallet/add" -d '{
     "privateKey": "'"${ETH_PRIVATE_KEY}"'",
    "chain": "ethereum",
    "network":"mainnet"
}' | jq

# polygon
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/wallet/add" -d '{
    "privateKey": "'"${ETH_PRIVATE_KEY}"'",
    "chain": "polygon",
    "network":"mainnet"
}' | jq

# polygon
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/wallet/add" -d '{
    "privateKey": "'"${ETH_PRIVATE_KEY}"'",
    "chain": "polygon",
    "network":"mumbai"
}' | jq
```

#### 7.2 If use httpie

```shell
# xinfin
https ${SERVER}/wallet/add privateKey=${XDC_PRIVATE_KEY} chain=xdc network=xinfin

# apothem
https ${SERVER}/wallet/add privateKey=${XDC_PRIVATE_KEY} chain=xdc network=apothem

# ethereum
https ${SERVER}/wallet/add privateKey=${ETH_PRIVATE_KEY} chain=ethereum network=mainnet

# polygon
https ${SERVER}/wallet/add privateKey=${ETH_PRIVATE_KEY} chain=polygon network=mainnet

# mumbai
https ${SERVER}/wallet/add privateKey=${ETH_PRIVATE_KEY} chain=polygon network=mumbai
```

![image](https://user-images.githubusercontent.com/7695325/194810513-f81aa2dc-653f-420e-9abc-29b37a8b5c47.png)

### 8. query token balances

#### 8.1 If use curl

```shell
# xinfin
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/network/balances" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"${XDC_ADDRESS}"'",
    "tokenSymbols": ["XDC","WXDC"]
}' | jq

# apothem
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/network/balances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"${XDC_ADDRESS}"'",
    "tokenSymbols": ["XDC","WXDC", "DAI", "WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]
}' | jq

# ethereum
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/network/balances" -d '{
    "chain": "ethereum",
    "network": "mainnet",
    "address": "'"${ETH_ADDRESS}"'","tokenSymbols": ["ETH","USDC"]}
' | jq

# polygon
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/network/balances" -d '{
    "chain": "polygon",
    "network": "mainnet",
    "address": "'"${ETH_ADDRESS}"'","tokenSymbols": ["MATIC","DB"]
}' | jq

# mumbai
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/network/balances" -d '{
    "chain": "polygon",
    "network": "mumbai",
    "address": "'"${ETH_ADDRESS}"'",
    "tokenSymbols": ["MATIC", "WETH", "DAI", "WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]
}' | jq
```

#### 8.2 If use httpie

```shell
# xinfin
https POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} \
tokenSymbols:='["XDC", "WXDC"]'

# apothem
https POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} \
tokenSymbols:='["XDC", "WXDC", "WBTC2", "YFI2", "MKR2", "USDC2"]'

# ethereum
https POST ${SERVER}/network/balances chain=ethereum network=mainnet address=${ETH_ADDRESS} \
tokenSymbols:='["ETH", "USDC"]'

# polygon
https POST ${SERVER}/network/balances chain=polygon network=mainnet address=${ETH_ADDRESS} \
tokenSymbols:='["MATIC", "DB"]'

# mumbai
https POST ${SERVER}/network/balances chain=polygon network=mumbai address=${ETH_ADDRESS} \
tokenSymbols:='["MATIC", "WBTC2", "YFI2", "MKR2", "USDC2"]'
```

![1665385674773](https://user-images.githubusercontent.com/7695325/194814031-1a0ce2c9-532a-46b6-831e-8f7eb11bc430.png)

### 9. fetch token price

#### 9.1 If use curl

```shell
# xdc apothem buy
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xdcswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xdcswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "YFI2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xdcswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "MKR2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

# xdc apothem sell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xdcswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xdcswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "YFI2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xdcswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "MKR2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL"
}' | jq

# quickswap mumbai buy
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "quickswap",
    "chain": "polygon",
    "network": "mumbai",
    "base": "MKR2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

# quickswap mumbai sell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "quickswap",
    "chain": "polygon",
    "network": "mumbai",
    "base": "MKR2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL"
}' | jq
```

#### 9.2 If use httpie

```shell
# xdc apothem buy
https POST ${SERVER}/amm/price connector=xdcswap chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=BUY
https POST ${SERVER}/amm/price connector=xdcswap chain=xdc network=apothem quote=USDC2 amount=1 base=YFI2 side=BUY
https POST ${SERVER}/amm/price connector=xdcswap chain=xdc network=apothem quote=USDC2 amount=1 base=MKR2 side=BUY

# xdc apothem sell
https POST ${SERVER}/amm/price connector=xdcswap chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=SELL
https POST ${SERVER}/amm/price connector=xdcswap chain=xdc network=apothem quote=USDC2 amount=1 base=YFI2 side=SELL
https POST ${SERVER}/amm/price connector=xdcswap chain=xdc network=apothem quote=USDC2 amount=1 base=MKR2 side=SELL

# quickswap mumbai buy
https POST ${SERVER}/amm/price connector=quickswap chain=polygon network=mumbai quote=USDC2 amount=1 base=MKR2 side=BUY

# quickswap mumbai sell
https POST ${SERVER}/amm/price connector=quickswap chain=polygon network=mumbai quote=USDC2 amount=1 base=MKR2 side=SELL
```

![1665386133136](https://user-images.githubusercontent.com/7695325/194815028-d6441e1f-9838-4813-b522-0b565d3b1fa0.png)
![1665386266312](https://user-images.githubusercontent.com/7695325/194815258-7ebb31aa-27da-4548-b534-4e24c1269630.png)
![1665386356223](https://user-images.githubusercontent.com/7695325/194815449-419cce40-df40-43db-98f0-dd283d3dd0c7.png)

### 10. Trade

You can check balances of base and quote tokens before and after trade. Notice: do not send transations too quickly. We must send next translation after last translation has been executed successfully. The wait time is about 3-6 seconds usually.

#### 10.1 If use curl

```shell
# apothem xdcswap buy
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY",
    "chain": "xdc",
    "network": "apothem",
    "connector": "xdcswap"
}' | jq

# apothem xdcswap sell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL",
    "chain": "xdc",
    "network": "apothem",
    "connector": "xdcswap"
}' | jq

# mumbai quickswap buy
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$ETH_ADDRESS"'",
    "base": "MKR2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY",
    "chain": "polygon",
    "network": "mumbai",
    "connector": "quickswap"
}' | jq

# mumbai quickswap sell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$ETH_ADDRESS"'",
    "base": "MKR2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL",
    "chain": "polygon",
    "network": "mumbai",
    "connector": "quickswap"
}' | jq
```

#### 10.2 If use httpie

```shell
#  apothem xdcswap buy
https POST ${SERVER}/amm/trade connector=xdcswap chain=xdc network=apothem address=${XDC_ADDRESS} \
quote=USDC2 amount=1 base=WBTC2 side=BUY

https POST ${SERVER}/amm/trade connector=xdcswap chain=xdc network=apothem address=${XDC_ADDRESS} \
quote=USDC2 amount=1 base=YFI2 side=BUY

https POST ${SERVER}/amm/trade connector=xdcswap chain=xdc network=apothem address=${XDC_ADDRESS} \
quote=USDC2 amount=1 base=MKR2 side=BUY

# apothem xdcswap sell
https POST ${SERVER}/amm/trade connector=xdcswap chain=xdc network=apothem address=${XDC_ADDRESS} \
quote=USDC2 amount=1 base=WBTC2 side=SELL

https POST ${SERVER}/amm/trade connector=xdcswap chain=xdc network=apothem address=${XDC_ADDRESS} \
quote=USDC2 amount=1 base=YFI2 side=SELL

https POST ${SERVER}/amm/trade connector=xdcswap chain=xdc network=apothem address=${XDC_ADDRESS} \
quote=USDC2 amount=1 base=MKR2 side=SELL

# mumbai quickswap buy
https POST ${SERVER}/amm/trade connector=quickswap chain=polygon network=mumbai address=${ETH_ADDRESS} \
quote=USDC2 amount=1 base=MKR2 side=BUY

# mumbai quickswap sell
https POST ${SERVER}/amm/trade connector=quickswap chain=polygon network=mumbai address=${ETH_ADDRESS} \
quote=USDC2 amount=1 base=MKR2 side=SELL
```

![1665386537251](https://user-images.githubusercontent.com/7695325/194815870-19f70703-fcf8-4015-acbb-84ddd12730d7.png)
![1665386852412](https://user-images.githubusercontent.com/7695325/194816573-ea761970-61ec-420b-bd81-6c58567e430d.png)

### 11. query account nonce

#### 11.1 If use curl

```shell
# xinfin
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/nonce" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address":"'"$XDC_ADDRESS"'"
}' | jq

# apothem
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/nonce" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address":"'"$XDC_ADDRESS"'"
}' | jq

# ethereum
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/nonce" -d '{
    "chain": "ethereum",
    "network": "mainnet",
    "address":"'"$ETH_ADDRESS"'"
}' | jq

# polygon
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/nonce" -d '{
    "chain": "polygon",
    "network": "mainnet",
    "address": "'"$ETH_ADDRESS"'"
}' | jq

# mumbai
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/nonce" -d '{
    "chain": "polygon",
    "network": "mumbai",
    "address": "'"$ETH_ADDRESS"'"
}' | jq
```

#### 11.2 If use httpie

```shell
# xinfin
https POST ${SERVER}/evm/nonce chain=xdc network=xinfin address=${XDC_ADDRESS}

# apothem
https POST ${SERVER}/evm/nonce chain=xdc network=apothem address=${XDC_ADDRESS}

# ethereum
https POST ${SERVER}/evm/nonce chain=ethereum network=mainnet address=${ETH_ADDRESS}

# polygon
https POST ${SERVER}/evm/nonce chain=polygon network=mainnet address=${ETH_ADDRESS}

# mumbai
https POST ${SERVER}/evm/nonce chain=polygon network=mumbai address=${ETH_ADDRESS}
```

![1665387013788](https://user-images.githubusercontent.com/7695325/194816939-a37e1235-2355-425f-9694-61933fffd3f1.png)

### 12. Query allowances

#### 12.1 If use curl

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xdcswap",
    "tokenSymbols": ["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xdc41c0ad06c98951e9bf7172cd1d285c6e34537170",
    "tokenSymbols": ["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xdc85f33E1242d87a875301312BD4EbaEe8876517BA",
    "tokenSymbols": ["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "polygon",
    "network": "mumbai",
    "address": "'"$ETH_ADDRESS"'",
    "spender": "quickswap",
    "tokenSymbols": ["MKR2", "USDC2"]
}' | jq
```

#### 12.1 If use httpie

```shell
# apothem xdcswap 0x41c0ad06c98951e9bf7172cd1d285c6e34537170
https POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdcswap tokenSymbols:='["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]'

https POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc49dc40FC5708376Cff6De423561cD5a9176FA8BA tokenSymbols:='["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]'

https POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc85f33E1242d87a875301312BD4EbaEe8876517BA tokenSymbols:='["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]'

# mumbai quickswap
https POST ${SERVER}/evm/allowances chain=polygon network=mumbai address=${ETH_ADDRESS} \
spender=quickswap tokenSymbols:='["MKR2", "USDC2"]'
```

![1665387251735](https://user-images.githubusercontent.com/7695325/194817479-ad210ba4-d8d9-4980-90ea-8f2fcd790166.png)
![1665387867446](https://user-images.githubusercontent.com/7695325/194819189-c684e271-5bb9-4e5d-a11c-64fb31cf4af1.png)
