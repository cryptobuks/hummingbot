# Test hummingbot API with xdc chain

## Setup enviroment

-   Setup enviroment according to [instruction](./setup-test-enviroment.md) before test.
-   Mint some test tokens to your test account.

## Test cases

### 1. Query gateway status

```shell
# curl -s -X GET -k --key ${GATEWAY_KEY} --cert ${GATEWAY_CERT} ${SERVER} | jq
https ${SERVER}
```

![1665381110082](https://user-images.githubusercontent.com/7695325/194805324-568809bc-7351-4043-b2e6-027a2e316777.png)

### 2. Query network config

```shell
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/config" | jq
https ${SERVER}/network/config
```

![1665381248109](https://user-images.githubusercontent.com/7695325/194805532-fa31070e-c000-4f3b-a5ce-ee24ca809683.png)

### 3. Query connectors

```shell
# curl -s -X GET -k --key ${GATEWAY_KEY} --cert ${GATEWAY_CERT} ${SERVER}/connectors | jq
https ${SERVER}/connectors
```

![1665381363116](https://user-images.githubusercontent.com/7695325/194805717-fa66a212-db04-47c3-9387-470950301762.png)

### 4. Query network status

```shell
# xinfin
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=xdc&network=xinfin" | jq
https ${SERVER}/network/status chain==xdc network==xinfin

# apothem
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/status?chain=xdc&network=apothem" | jq
https ${SERVER}/network/status chain==xdc network==apothem

# all networks
# curl -s -X GET -k --key $GATEWAY_KEY --cert "$GATEWAY_CERT" "${SERVER}/network/status" | jq
https ${SERVER}/network/status
```

![1666581309920](https://user-images.githubusercontent.com/7695325/197441729-ddb4d9b4-8486-4f1c-83f2-c45bb166c251.png)

### 5. Fetch token list

**xinfin**

```shell
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=xdc&network=xinfin" | jq
https ${SERVER}/network/tokens chain==xdc network==xinfin
```

![1666581478445](https://user-images.githubusercontent.com/7695325/197442054-641e4d93-75f0-440c-84e4-7c3937876077.png)

**apothem**

```shell
# curl -s -X GET -k --key $GATEWAY_KEY --cert $GATEWAY_CERT "${SERVER}/network/tokens?chain=xdc&network=apothem" | jq
https ${SERVER}/network/tokens chain==xdc network==apothem
```

![1666581678634](https://user-images.githubusercontent.com/7695325/197442327-51bc9c5b-66fb-43ef-809f-ce53ede5e36e.png)

### 6. Query transaction

#### 6.1 If use curl

**xinfin**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "txHash": "0xf49e741720e3f6c464e7bfbdcaefddb6f9a4cc39d4b367476727e95be735b350"
}' | jq
```

**apothem**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}"  "${SERVER}/network/poll" -d '{
    "chain": "xdc",
    "network": "apothem",
    "txHash": "0x1114601d48ebc3afa5e06af13e759560e74b3d5d1837d951919bc57727a623dd"
}' | jq
```

#### 6.2 If use httpie

**xinfin**

```shell
https POST ${SERVER}/network/poll chain=xdc network=xinfin txHash=0xf49e741720e3f6c464e7bfbdcaefddb6f9a4cc39d4b367476727e95be735b350
```

![1665382299452](https://user-images.githubusercontent.com/7695325/194807163-89b72386-62b6-4a62-a085-717143b0cb30.png)

**apothem**

```shell
https POST ${SERVER}/network/poll chain=xdc network=apothem txHash=0x1114601d48ebc3afa5e06af13e759560e74b3d5d1837d951919bc57727a623dd
```

![1665382392522](https://user-images.githubusercontent.com/7695325/194807291-03438647-9a13-4931-b8cd-53e601bdb12a.png)

### 7 Add private key

The length of private key is 67 characters with `xdc` prefix or 66 characters with `0x` prefix, such as:

-   xdc prefix: xdc0123456789012345678901234567890123456789012345678901234567890123
-   0x prefix: 0x0123456789012345678901234567890123456789012345678901234567890123

We will use xdc-prefix in below tests.

```shell
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
```

#### 7.2 If use httpie

```shell
# xinfin
https ${SERVER}/wallet/add privateKey=${XDC_PRIVATE_KEY} chain=xdc network=xinfin

# apothem
https ${SERVER}/wallet/add privateKey=${XDC_PRIVATE_KEY} chain=xdc network=apothem
```

![image](https://user-images.githubusercontent.com/7695325/197444416-110f6989-0ea3-42be-bae7-8c1e155d7ed5.png)

### 8. Query token balances

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
```

#### 8.2 If use httpie

```shell
# xinfin
https POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} \
tokenSymbols:='["XDC", "WXDC"]'

# apothem
https POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} \
tokenSymbols:='["XDC", "WXDC", "WBTC2", "YFI2", "MKR2", "USDC2"]'
```

![1666583273337](https://user-images.githubusercontent.com/7695325/197444977-364a4237-6167-4e5f-943a-bdb1d96b382e.png)

### 9. Query account nonce

#### 9.1 If use curl

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
```

#### 9.2 If use httpie

```shell
# xinfin
https POST ${SERVER}/evm/nonce chain=xdc network=xinfin address=${XDC_ADDRESS}

# apothem
https POST ${SERVER}/evm/nonce chain=xdc network=apothem address=${XDC_ADDRESS}
```

![1666583481122](https://user-images.githubusercontent.com/7695325/197445347-5879d83c-b231-47d9-8cb0-a7f21a795328.png)

### 10. Query allowances

#### 10.1 If use curl

**apothem**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xdc41c0ad06c98951e9bf7172cd1d285c6e34537170",
    "tokenSymbols": ["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]
}' | jq
```

#### 10.2 If use httpie

**apothem**

```shell
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc85f33E1242d87a875301312BD4EbaEe8876517BA tokenSymbols:='["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]'
```

![1666773526480](https://user-images.githubusercontent.com/7695325/197977624-0410d540-3c30-4982-a1aa-c3f8265ee50f.png)

**xinfin**

```shell
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=xinfin address=${XDC_ADDRESS} \
spender=xdc85f33E1242d87a875301312BD4EbaEe8876517BA tokenSymbols:='["WXDC", "SRX", "XTT", "xUSDT"]'
```

![1666773582879](https://user-images.githubusercontent.com/7695325/197977864-c4609ec3-c862-46e7-aa71-5eb01769b350.png)

### 11. Approve allowance

#### 11.1 If use curl

**apothem**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xdc41c0ad06c98951e9bf7172cd1d285c6e34537170",
    "token": "USDC2"
}' | jq
```

**xinfin**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xdc41c0ad06c98951e9bf7172cd1d285c6e34537170",
    "token": "SRX"
}' | jq
```

#### 11.2 If use httpie

**apothem**

```shell
# clear allowances
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 token=USDC2 amount=0

# query allowances
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 tokenSymbols:='["USDC2"]'

# set allowances to maximum
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 token=USDC2

# query allowances
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 tokenSymbols:='["USDC2"]'
```

![1666773765723](https://user-images.githubusercontent.com/7695325/197978588-b0744215-7a64-404a-90c9-1561f833602f.png)

**xinfin**

```shell
# query allowances
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=xinfin address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 tokenSymbols:='["SRX"]'

# set allowances to maximum
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 token=SRX

# query allowances
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=xinfin address=${XDC_ADDRESS} \
spender=xdc41c0ad06c98951e9bf7172cd1d285c6e34537170 tokenSymbols:='["SRX"]'
```

![1666773893275](https://user-images.githubusercontent.com/7695325/197979153-8eb127e4-1099-4546-ac84-875c81d15749.png)
