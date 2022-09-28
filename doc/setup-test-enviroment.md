# Prepare test enviroment

## 1. Install dependent packages

```shell
sudo apt install -y curl httpie jq
```

![1665380461924](https://user-images.githubusercontent.com/7695325/194804452-4490db01-2ac8-4011-a46d-2b7086cc2706.png)

httpie is recommended to send https request in our test cases. You can use curl instead of httpie.

## 2. Set environment variables

Set the following environment variables:

```shell
# address of gateway, maybe different in your environment
export SERVER="https://127.0.0.1:15888"

# POST command use JSON format
export HEADER="Content-Type: application/json"

# public key of test account, replace it with your account
export ETH_ADDRESS="0xD4CE02705041F04135f1949Bc835c1Fe0885513c"
export XDC_ADDRESS="xdcD4CE02705041F04135f1949Bc835c1Fe0885513c"

# path of certs
CERT=$(ls -Frt ${HOME}/.hummingbot-gateway | grep -E '^hummingbot-gateway-[0-9a-z]{8}/$' | tail -n 1)
export CERTS_PATH="${HOME}/.hummingbot-gateway/${CERT:0:-1}/certs"

export GATEWAY_CERT="${CERTS_PATH}/client_cert.pem"
echo "GATEWAY_CERT=${GATEWAY_CERT}"

export GATEWAY_KEY="${CERTS_PATH}/client_key.pem"
echo "GATEWAY_KEY=${GATEWAY_KEY}"
```

![1666763052298](https://user-images.githubusercontent.com/7695325/197944504-c248f81c-61eb-484f-aa69-eb8388b22d16.png)

## 3. Config httpie

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

![1666350758903](https://user-images.githubusercontent.com/7695325/197183081-1466c81c-f74b-4645-8850-07211bf8389e.png)

![1666350676666](https://user-images.githubusercontent.com/7695325/197182863-0786dd56-7097-4b2f-a010-b2228805da64.png)

## 4. create tokens

-   I created some test tokens on apothem blockchain with [factory contract 0xfD9F33ab143b1717D4784A420eE5A93A8CbBcABd](https://explorer.apothem.network/address/xdcfD9F33ab143b1717D4784A420eE5A93A8CbBcABd#readContract). You can mint any quantity to your account.

| Symbol | Decimals | Address                                                                                                                                         |
| -----: | -------: | :---------------------------------------------------------------------------------------------------------------------------------------------- |
|  WBTC2 |        8 | [0x01B0500f82EF188D0410a46f2E8940133E213e83](https://explorer.apothem.network/address/xdc01b0500f82ef188d0410a46f2e8940133e213e83#readContract) |
|   YFI2 |       18 | [0x22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d](https://explorer.apothem.network/address/xdc22e4Eb82FF59c53B275aDEacd4EE4Bc47fc4f16d#readContract) |
|   MKR2 |       18 | [0x258E445fEf3F41429e38ee124DA63aBfb08edc70](https://explorer.apothem.network/address/xdc258E445fEf3F41429e38ee124DA63aBfb08edc70#readContract) |
|  AAVE2 |       18 | [0x3042207876c47D3c206df99b3279d97813B34Ea1](https://explorer.apothem.network/address/xdc3042207876c47D3c206df99b3279d97813B34Ea1#readContract) |
|   UNI2 |       18 | [0xD9e33607d06cBB1Fef59488b9969426b10F310B8](https://explorer.apothem.network/address/xdcD9e33607d06cBB1Fef59488b9969426b10F310B8#readContract) |
|  USDC2 |        6 | [0xF83B9Dc502A3F76c042b4043B6C1B5eBBE574389](https://explorer.apothem.network/address/xdcF83B9Dc502A3F76c042b4043B6C1B5eBBE574389#readContract) |
