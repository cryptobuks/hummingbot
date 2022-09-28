# Test hummingbot API with [globiance exchange](https://app.xspswap.finance/pool)

**Notice: The contracts of globiance exchange on testnet(apothem) are not ready now. Do not run test cases on testnet!**

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
| :----: | -----: | -------------------: |
| WBTC2  |  USDC2 |                20000 |
|  YFI2  |  USDC2 |                 8000 |
|  MKR2  |  USDC2 |                  600 |
| AAVE2  |  USDC2 |                   70 |
|  UNI2  |  USDC2 |                    5 |

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
    "spender": "globiance",
    "token": "USDC2"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "globiance",
    "token": "WBTC2"
}' | jq
```

**xinfin**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "globiance",
    "token": "GBEX"
}' | jq

curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/approve" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "globiance",
    "token": "SRX"
}' | jq
```

#### 1.2 If use httpie

**apothem**

```shell
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=globiance token=USDC2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=globiance token=WBTC2
```

![1666774991914](https://user-images.githubusercontent.com/7695325/197983708-1d166542-d25c-44fb-a8c0-14fc5ce3c450.png)

**xinfin**

```shell
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} spender=globiance token=GBEX
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} spender=globiance token=SRX
```

![1666779725755](https://user-images.githubusercontent.com/7695325/198002274-fb670684-481b-4c5e-a57c-c857d9196ac4.png)

### 2. Query allowances

#### 2.1 If use curl

**apothem**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "apothem",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "globiance",
    "tokenSymbols": ["WBTC2", "USDC2"]
}' | jq
```

**xinfin**

```shell
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/evm/allowances" -d '{
    "chain": "xdc",
    "network": "xinfin",
    "address": "'"$XDC_ADDRESS"'",
    "spender": "globiance",
    "tokenSymbols": ["GBEX", "SRX"]
}' | jq
```

#### 2.2 If use httpie

**apothem**

```shell
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} spender=globiance tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666775412785](https://user-images.githubusercontent.com/7695325/197985347-176d758b-ec46-47a0-8ec1-207173b038cb.png)

**xinfin**

```shell
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=xinfin address=${XDC_ADDRESS} spender=globiance tokenSymbols:='["GBEX", "SRX"]'
```

![1666779804001](https://user-images.githubusercontent.com/7695325/198002507-528d438f-3450-4ee2-abc3-be41d39fb280.png)

### 3. fetch token price

#### 3.1 If use curl

**apothem**

```shell
# buy WBTC2, quote with USDC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "globiance",
    "chain": "xdc",
    "network": "apothem",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

# sell WBTC2, quote with USDC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "globiance",
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
# buy SRX, quote with GBEX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "globiance",
    "chain": "xdc",
    "network": "xinfin",
    "base": "SRX",
    "quote": "GBEX",
    "amount": "1",
    "side": "BUY"
}' | jq

# sell SRX, quote with GBEX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "globiance",
    "chain": "xdc",
    "network": "xinfin",
    "base": "SRX",
    "quote": "GBEX",
    "amount": "1",
    "side": "SELL"
}' | jq
```

#### 3.2 If use httpie

**apothem**

```shell
# buy WBTC2, quote with USDC2
https --print=b POST ${SERVER}/amm/price connector=globiance chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=BUY

# sell WBTC2, quote with USDC2
https --print=b POST ${SERVER}/amm/price connector=globiance chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=SELL
```

![1666600887376](https://user-images.githubusercontent.com/7695325/197485115-402b76f8-645f-4a32-b09a-9af28cb0dd43.png)

**xinfin**

```shell
# buy SRX, quote with GBEX
https --print=b POST ${SERVER}/amm/price connector=globiance chain=xdc network=xinfin quote=GBEX amount=1 base=SRX side=BUY

# sell SRX, quote with GBEX
https --print=b POST ${SERVER}/amm/price connector=globiance chain=xdc network=xinfin quote=GBEX amount=1 base=SRX side=SELL
```

![1666779895385](https://user-images.githubusercontent.com/7695325/198002823-963eb1ef-183e-4e81-a233-a14145678575.png)

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
    "connector": "globiance"
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
    "connector": "globiance"
}' | jq
```

**xinfin**

```shell
# buy SRX, quote with GBEX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "SRX",
    "quote": "GBEX",
    "amount": "1",
    "side": "BUY",
    "chain": "xdc",
    "network": "xinfin",
    "connector": "globiance"
}' | jq

# sell SRX, quote with GBEX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "SRX",
    "quote": "GBEX",
    "amount": "1",
    "side": "SELL",
    "chain": "xdc",
    "network": "xinfin",
    "connector": "globiance"
}' | jq
```

#### 4.2 If use httpie

**apothem**

```shell
# buy WBTC2, quote with USDC2
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
https --print=h POST ${SERVER}/amm/trade connector=globiance chain=xdc network=apothem address=${XDC_ADDRESS} quote=USDC2 amount=1 base=WBTC2 side=BUY
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666602199336](https://user-images.githubusercontent.com/7695325/197489651-0407e3c6-4151-481e-b34a-e0dee3369f27.png)

```shell

# sell WBTC2, quote with USDC2
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
https --print=h POST ${SERVER}/amm/trade connector=globiance chain=xdc network=apothem address=${XDC_ADDRESS} quote=USDC2 amount=1 base=WBTC2 side=SELL
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666602830052](https://user-images.githubusercontent.com/7695325/197491647-c8b24ff2-e2b2-4279-b4cc-e420dd705feb.png)

**xinfin**

```shell
# buy SRX, quote with GBEX
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["GBEX", "SRX"]'
https --print=h POST ${SERVER}/amm/trade connector=globiance chain=xdc network=xinfin address=${XDC_ADDRESS} quote=GBEX amount=1 base=SRX side=BUY
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["GBEX", "SRX"]'
```

![1666780493107](https://user-images.githubusercontent.com/7695325/198004848-25381332-e345-4275-bd5b-556dab7c16d5.png)

```shell
# sell SRX, quote with GBEX
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["GBEX", "SRX"]'
https --print=h POST ${SERVER}/amm/trade connector=globiance chain=xdc network=xinfin address=${XDC_ADDRESS} quote=GBEX amount=1 base=SRX side=SELL
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["GBEX", "SRX"]'
```

![1666780585280](https://user-images.githubusercontent.com/7695325/198005148-a03db32b-686d-4efa-ad85-3217ebac1271.png)
