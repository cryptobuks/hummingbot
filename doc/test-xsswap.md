# Test hummingbot API with [xsswap exchange](https://app.xspswap.finance/pool)

## Preparations

### 1. Setup enviroment

-   Setup enviroment according to [instructions](./setup-test-enviroment.md) before test.

### 2. Mint tokens

Mint enough test tokes to your test account on blockchain explorer.

| Token |                                  address on blockchain explorer                                   |
| ----: | :-----------------------------------------------------------------------------------------------: |
| WBTC2 | https://explorer.apothem.network/address/xdc01B0500f82EF188D0410a46f2E8940133E213e83#readContract |
|  YFI2 | https://explorer.apothem.network/address/xdc22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d#readContract |
|  MKR2 | https://explorer.apothem.network/address/xdc258E445fEf3F41429e38ee124DA63aBfb08edc70#readContract |
| AAVE2 | https://explorer.apothem.network/address/xdc3042207876c47D3c206df99b3279d97813B34Ea1#readContract |
|  UNI2 | https://explorer.apothem.network/address/xdcD9e33607d06cBB1Fef59488b9969426b10F310B8#readContract |
| USDC2 | https://explorer.apothem.network/address/xdcF83B9Dc502A3F76c042b4043B6C1B5eBBE574389#readContract |

### 3. Trade pools

I created some trade pools on testnet for test:

| Token1 | Token2 | Price(Token1/Token2) |
| -----: | -----: | -------------------: |
|  WBTC2 |  USDC2 |                20000 |
|   YFI2 |  USDC2 |                 8000 |
|   MKR2 |  USDC2 |                  600 |
|  AAVE2 |  USDC2 |                   70 |
|   UNI2 |  USDC2 |                    5 |

## Test cases

### 1. Approve allowance

Set allowances to maximum for each test token.

#### 1.1 If use curl

**apothem**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xsswap",
    "token": "USDC2"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xsswap",
    "token": "WBTC2"
}' | jq
```

**xinfin**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xsswap",
    "token": "WXDC"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xsswap",
    "token": "XTT"
}' | jq
```

#### 1.2 If use httpie

**apothem**

```shell
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=USDC2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=WBTC2
```

![1666774991914](https://user-images.githubusercontent.com/7695325/197983708-1d166542-d25c-44fb-a8c0-14fc5ce3c450.png)

**xinfin**

```shell
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} spender=xsswap token=WXDC
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} spender=xsswap token=XTT
```

![1666777201581](https://user-images.githubusercontent.com/7695325/197992826-0d4168b2-a60d-41c0-9a8f-9fe0282b9308.png)

### 2. Query allowances

#### 2.1 If use curl

**apothem**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xsswap",
    "tokenSymbols": ["WBTC2", "USDC2"]
}' | jq
```

**xinfin**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "xsswap",
    "tokenSymbols": ["WXDC", "XTT"]
}' | jq
```

#### 2.2 If use httpie

**apothem**

```shell
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666775412785](https://user-images.githubusercontent.com/7695325/197985347-176d758b-ec46-47a0-8ec1-207173b038cb.png)

**xinfin**

```shell
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=xinfin address=${XDC_ADDRESS} spender=xsswap tokenSymbols:='["WXDC", "XTT"]'
```

![1666778539223](https://user-images.githubusercontent.com/7695325/197997814-dff8a1e7-2d9a-4e9c-9a4d-431a1fb9ebad.png)

### 3. fetch token price

#### 3.1 If use curl

**apothem**

```shell
# buy WBTC2, quote with USDC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

# sell WBTC2, quote with USDC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL"
}' | jq
```

**xinfin**

```shell
# buy XTT, quote with WXDC
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "xinfin",
    "base": "XTT",
    "quote": "WXDC",
    "amount": "1",
    "side": "BUY"
}' | jq

# sell XTT, quote with WXDC
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "xinfin",
    "base": "XTT",
    "quote": "WXDC",
    "amount": "1",
    "side": "SELL"
}' | jq
```

#### 3.2 If use httpie

**apothem**

```shell
# buy WBTC2, quote with USDC2
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=BUY

# sell WBTC2, quote with USDC2
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=SELL
```

![1666600887376](https://user-images.githubusercontent.com/7695325/197485115-402b76f8-645f-4a32-b09a-9af28cb0dd43.png)

**xinfin**

```shell
# buy XTT, quote with WXDC
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=xinfin quote=WXDC amount=1 base=XTT side=BUY

# sell XTT, quote with WXDC
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=xinfin quote=WXDC amount=1 base=XTT side=SELL
```

![1666777532897](https://user-images.githubusercontent.com/7695325/197994039-94414986-4563-4768-a1c3-b33f9cef8170.png)

### 4. Trade

You can check balances of base and quote tokens before and after trade. **Notice: do not send transations too quickly.** We must send next translation after last translation has been already executed. The wait time is about 3-6 seconds usually.

#### 4.1 If use curl

**apothem**

```shell
# buy WBTC2, quote with USDC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY",
    "chain": "xdc",
    "network": "apothem",
    "connector": "xsswap"
}' | jq

# sell WBTC2, quote with USDC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "SELL",
    "chain": "xdc",
    "network": "apothem",
    "connector": "xsswap"
}' | jq
```

**xinfin**

```shell
# buy XTT, quote with WXDC
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "XTT",
    "quote": "WXDC",
    "amount": "1",
    "side": "BUY",
    "chain": "xdc",
    "network": "xinfin",
    "connector": "xsswap"
}' | jq

# sell XTT, quote with WXDC
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "XTT",
    "quote": "WXDC",
    "amount": "1",
    "side": "SELL",
    "chain": "xdc",
    "network": "xinfin",
    "connector": "xsswap"
}' | jq
```

#### 4.2 If use httpie

**apothem**

```shell
# buy WBTC2, quote with USDC2
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=apothem address=${XDC_ADDRESS} quote=USDC2 amount=1 base=WBTC2 side=BUY
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666602199336](https://user-images.githubusercontent.com/7695325/197489651-0407e3c6-4151-481e-b34a-e0dee3369f27.png)

```shell

# sell WBTC2, quote with USDC2
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=apothem address=${XDC_ADDRESS} quote=USDC2 amount=1 base=WBTC2 side=SELL
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666602830052](https://user-images.githubusercontent.com/7695325/197491647-c8b24ff2-e2b2-4279-b4cc-e420dd705feb.png)

**xinfin**

```shell
# buy XTT, quote with WXDC
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "XTT"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=xinfin address=${XDC_ADDRESS} quote=WXDC amount=1 base=XTT side=BUY
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "XTT"]'
```

![1666778040514](https://user-images.githubusercontent.com/7695325/197995925-0e05a3f1-828a-4cff-8b27-f63900d017e5.png)

```shell
# sell XTT, quote with WXDC
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "XTT"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=xinfin address=${XDC_ADDRESS} quote=WXDC amount=1 base=XTT side=SELL
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "XTT"]'
```

![1666778145685](https://user-images.githubusercontent.com/7695325/197996331-b6c780c8-00c8-41d7-9488-c9bd0bfe3052.png)
