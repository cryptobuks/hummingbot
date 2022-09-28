# Test hummingbot API with xsswap

## Preparations

### Setup enviroment

-   Setup enviroment according to [instruction](./test-enviroment.md) before test.

### Mint tokens

Mint enough test tokes to your test account on blockchain explorer.

| Token |                                  address on blockchain explorer                                   |
| ----: | :-----------------------------------------------------------------------------------------------: |
| WBTC2 | https://explorer.apothem.network/address/xdc01B0500f82EF188D0410a46f2E8940133E213e83#readContract |
|  YFI2 | https://explorer.apothem.network/address/xdc22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d#readContract |
|  MKR2 | https://explorer.apothem.network/address/xdc258E445fEf3F41429e38ee124DA63aBfb08edc70#readContract |
| AAVE2 | https://explorer.apothem.network/address/xdc3042207876c47D3c206df99b3279d97813B34Ea1#readContract |
|  UNI2 | https://explorer.apothem.network/address/xdcD9e33607d06cBB1Fef59488b9969426b10F310B8#readContract |
| USDC2 | https://explorer.apothem.network/address/xdcF83B9Dc502A3F76c042b4043B6C1B5eBBE574389#readContract |

### Approve allowance

Approve xsswap router with maximum allowance for each token.

**apothem**

```shell
# approve allowance
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=WBTC2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=YFI2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=MKR2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=AAVE2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=UNI2
https --print=h POST ${SERVER}/evm/approve chain=xdc network=apothem address=${XDC_ADDRESS} spender=xsswap token=USDC2

# query allowance
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=apothem address=${XDC_ADDRESS} \
spender=xsswap tokenSymbols:='["WBTC2", "YFI2", "MKR2", "AAVE2", "UNI2", "USDC2"]'
```

![1666598749470](https://user-images.githubusercontent.com/7695325/197477822-0584632a-da56-4c06-87f2-0c4b8b8aceec.png)

![1666599004689](https://user-images.githubusercontent.com/7695325/197478601-1d870861-0171-4b12-81aa-a95ec95d7526.png)

**xinfin**

```shell
# approve allowance
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} spender=xsswap token=WXDC
https --print=h POST ${SERVER}/evm/approve chain=xdc network=xinfin address=${XDC_ADDRESS} spender=xsswap token=SRX

# query allowance
https --print=b POST ${SERVER}/evm/allowances chain=xdc network=xinfin address=${XDC_ADDRESS} \
spender=xsswap tokenSymbols:='["WXDC", "SRX"]'
```

![1666599173085](https://user-images.githubusercontent.com/7695325/197479200-9aac83c5-835e-4d1c-9881-9c54f70f3073.png)

### Trade pools

I have created some trade pools in [xsswap](https://app.xspswap.finance/pool) for below tests:

|                      TokenA                       |                      TokenB                       | Price(TokenA/TokenB) |
| :-----------------------------------------------: | :-----------------------------------------------: | -------------------: |
| WBTC2(0x01B0500f82EF188D0410a46f2E8940133E213e83) | USDC2(0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389) |                20000 |
| YFI2(0x22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d)  | USDC2(0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389) |                 8000 |
| MKR2(0x258E445fEf3F41429e38ee124DA63aBfb08edc70)  | USDC2(0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389) |                  600 |
| AAVE2(0x3042207876c47D3c206df99b3279d97813B34Ea1) | USDC2(0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389) |                   70 |
| UNI2(0xD9e33607d06cBB1Fef59488b9969426b10F310B8)  | USDC2(0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389) |                    5 |

## Test cases

### 1. fetch token price

#### 1.1 If use curl

**apothem**

```shell
# buy WBTC2
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "apothem",
    "base": "WBTC2",
    "quote": "USDC2",
    "amount": "1",
    "side": "BUY"
}' | jq

# sell WBTC2
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
# buy SRX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "xinfin",
    "base": "SRX",
    "quote": "WXDC",
    "amount": "1",
    "side": "BUY"
}' | jq

# sell SRX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/price" -d '{
    "connector": "xsswap",
    "chain": "xdc",
    "network": "xinfin",
    "base": "SRX",
    "quote": "WXDC",
    "amount": "1",
    "side": "SELL"
}' | jq
```

#### 1.2 If use httpie

**apothem**

```shell
# buy WBTC2
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=BUY

# sell WBTC2
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=apothem quote=USDC2 amount=1 base=WBTC2 side=SELL
```

![1666600887376](https://user-images.githubusercontent.com/7695325/197485115-402b76f8-645f-4a32-b09a-9af28cb0dd43.png)

**xinfin**

```shell
# buy SRX
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=xinfin quote=WXDC amount=1 base=SRX side=BUY

# sell SRX
https --print=b POST ${SERVER}/amm/price connector=xsswap chain=xdc network=xinfin quote=WXDC amount=1 base=SRX side=SELL
```

![1666600987566](https://user-images.githubusercontent.com/7695325/197485443-006df96b-1f86-4897-81ff-e40b082392fd.png)

### 2. Trade

You can check balances of base and quote tokens before and after trade. **Notice: do not send transations too quickly.** We must send next translation after last translation has been already executed. The wait time is about 3-6 seconds usually.

#### 2.1 If use curl

**apothem**

```shell
# buy WBTC2
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

# sell WBTC2
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
# buy SRX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "SRX",
    "quote": "WXDC",
    "amount": "1",
    "side": "BUY",
    "chain": "xdc",
    "network": "xinfin",
    "connector": "xsswap"
}' | jq

# sell SRX
curl -s -X POST -k --key $GATEWAY_KEY --cert $GATEWAY_CERT -H "${HEADER}" "${SERVER}/amm/trade" -d '{
    "address": "'"$XDC_ADDRESS"'",
    "base": "SRX",
    "quote": "WXDC",
    "amount": "1",
    "side": "SELL",
    "chain": "xdc",
    "network": "xinfin",
    "connector": "xsswap"
}' | jq
```

#### 2.2 If use httpie

**apothem**

```shell
# buy WBTC2
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=apothem address=${XDC_ADDRESS} quote=USDC2 amount=1 base=WBTC2 side=BUY
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'

# sell WBTC2
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=apothem address=${XDC_ADDRESS} quote=USDC2 amount=1 base=WBTC2 side=SELL
https --print=b POST ${SERVER}/network/balances chain=xdc network=apothem address=${XDC_ADDRESS} tokenSymbols:='["WBTC2", "USDC2"]'
```

![1666602199336](https://user-images.githubusercontent.com/7695325/197489651-0407e3c6-4151-481e-b34a-e0dee3369f27.png)

![1666602830052](https://user-images.githubusercontent.com/7695325/197491647-c8b24ff2-e2b2-4279-b4cc-e420dd705feb.png)

**xinfin**

```shell
# buy SRX
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "SRX"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=xinfin address=${XDC_ADDRESS} quote=WXDC amount=1 base=SRX side=BUY
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "SRX"]'

# sell SRX
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "SRX"]'
https --print=h POST ${SERVER}/amm/trade connector=xsswap chain=xdc network=xinfin address=${XDC_ADDRESS} quote=WXDC amount=1 base=SRX side=SELL
https --print=b POST ${SERVER}/network/balances chain=xdc network=xinfin address=${XDC_ADDRESS} tokenSymbols:='["WXDC", "SRX"]'
```

![1666603016720](https://user-images.githubusercontent.com/7695325/197492300-e65a5eff-447c-4664-88b1-32366e39ca99.png)

![1666603117463](https://user-images.githubusercontent.com/7695325/197492635-ae177ec4-ce5c-4188-a8dd-4f66e54b5a28.png)
