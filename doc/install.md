# How to install hummingbot from XDC

## 1) Environment

-   OS: Ubuntu 22.04
-   hummingbot:
    -   version: v1.8.0-xdc
    -   path: `${HOME}/${HUMMINGBOT_PATH}`

## 2) Install dependencies

Run the following commands in the first terminal:

```shell
sudo apt update -y
sudo apt install -y build-essential curl git httpie jq wget
```

![1665307628160](https://user-images.githubusercontent.com/7695325/194749045-a898869c-e6a3-425b-91f7-608d4a18f200.png)

## 3) Install miniconda3

Run the following commands in the first terminal:

```shell
cd ${HOME}
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh -b -u
./miniconda3/bin/conda init
# Reload .bashrc to register "conda" command
exec bash
conda install -y conda-build
```

![1665308065619](https://user-images.githubusercontent.com/7695325/194749317-91b1e182-4f92-452b-b4a6-f8de1a0afba0.png)
![1665308711511](https://user-images.githubusercontent.com/7695325/194749800-23c0f94c-1ab4-491e-bcb4-7b6f393391e1.png)

## 4) Install hummingbot

Run the following commands in the first terminal:

```shell
cd ${HOME}
export HUMMINGBOT_PATH=${HUMMINGBOT_PATH:-"hummingbot.xdc"}
git clone https://github.com/Carry-So/hummingbot.git ${HUMMINGBOT_PATH}
cd ${HUMMINGBOT_PATH}
git checkout v1.8.0-xdc
./clean
./install
conda activate hummingbot
./compile
```

![1665309337380](https://user-images.githubusercontent.com/7695325/194750289-4ff5c951-440a-4883-989d-a08e3e479c11.png)
![1665309494510](https://user-images.githubusercontent.com/7695325/194750376-120b51c6-b99b-4062-a874-db30e1ec3c0b.png)

## 5) Setup hummingbot

### 5.1 Start hummingbot client

Run the command `bin/hummingbot.py` in the first terminal to start hummingbot client. Then set your password according to prompts. Please refer to https://docs.hummingbot.org/operation/password/ if has problem.

![1665310045517](https://user-images.githubusercontent.com/7695325/194750757-cc1f6d9e-e2ce-46e8-9e7e-db09a613e97b.png)
![1665310115784](https://user-images.githubusercontent.com/7695325/194750805-b9085783-4e7d-437e-b7c1-49f7b40d10bd.png)
![1665310150313](https://user-images.githubusercontent.com/7695325/194750835-dd2c9bc9-1bc3-44dd-a4aa-fb9c0b343a25.png)
![1665310230670](https://user-images.githubusercontent.com/7695325/194750908-c55d1317-a7b2-4dd7-96e5-baafa05e2bf4.png)
![1665310316621](https://user-images.githubusercontent.com/7695325/194750953-baa2ac96-7c27-4306-9152-74b53575e68a.png)
![1665310349389](https://user-images.githubusercontent.com/7695325/194750973-30031034-91ec-4041-9559-e300220ed366.png)
![1665310389794](https://user-images.githubusercontent.com/7695325/194750995-f6dd4e78-2e7e-45ce-8aef-4d6be6bf180e.png)

### 5.2 Generate certs

Run the command `gateway generate-certs` in the input pane(lower left) of hummingbot client. You will be prompted to enter the passphrase used to encrypt these certs. We recommend using the same password in last step. **Do not quit hummingbot client now!.**

```text
>>> gateway generate-certs
Enter pass phase to generate Gateway SSL certifications >>> *****
Gateway SSL certification files are created in /home/<YOUR NAME>/.hummingbot-gateway/hummingbot-gateway-********/certs
```

Take note of this folder path. Please refer to: https://docs.hummingbot.org/developers/gateway/setup/#1-generate-certs if has problem.

![1665310606540](https://user-images.githubusercontent.com/7695325/194751175-f8c00800-32b9-46ce-a7fb-09abf2e61f62.png)
![1665310874784](https://user-images.githubusercontent.com/7695325/194751373-4e6f09df-34ab-44c8-980b-82c2cfc36ee6.png)
![1665310918625](https://user-images.githubusercontent.com/7695325/194751406-89827215-7630-439c-9232-32d9bd151def.png)

### 5.3 Setup gateway ssl

Run the following commands to setup gateway ssl in the second terminal:

```shell
export HUMMINGBOT_PATH=${HUMMINGBOT_PATH:-"hummingbot.xdc"}
cd ${HOME}/${HUMMINGBOT_PATH}/gateway
setup/generate_conf.sh conf

CERT=$(ls -Frt ${HOME}/.hummingbot-gateway | grep -E '^hummingbot-gateway-[0-9a-z]{8}/$' | tail -n 1)
export CERTS_PATH="${HOME}/.hummingbot-gateway/${CERT:0:-1}/certs"
echo "CERTS_PATH=${CERTS_PATH}"

cat > conf/ssl.yml <<EOF
caCertificatePath: ${CERTS_PATH}/ca_cert.pem
certificatePath: ${CERTS_PATH}/server_cert.pem
keyPath: ${CERTS_PATH}/server_key.pem
EOF

cat conf/ssl.yml
```

Please refer to: https://docs.hummingbot.org/developers/gateway/setup/#2-set-up-gateway-ssl if has problem.

![1665374305016](https://user-images.githubusercontent.com/7695325/194797500-15dcb4b1-6ba2-4f01-917d-1626bd3558d4.png)

### 5.4 Start hummingbot gateway

Run the following commands to start hummingbot gateway in second terminal:

```shell
# Install dependencies
yarn

# Compile code
yarn build

# Start server using certs passphrase, such as: `yarn start --passphrase=daniel`
yarn start --passphrase=<PASSWORD>
```

Please refer to: https://docs.hummingbot.org/developers/gateway/setup/#3-run-gateway-server if has problem.

![1665318968820](https://user-images.githubusercontent.com/7695325/194757180-372b50e5-e134-484d-bc87-e94b353ea0d5.png)

### 5.5 Connect wallet

The hummingbot client should show: `Gateway: ONLINE` on the top of log pane(right) now. Run the following commands in Hummingbot to test the connection and connect to a DEX like Uniswap. Notice: the private key has no `0x` prefix(length is 64 characters) in this step!

```text
>>> gateway test-connection

Successfully pinged Gateway.

>>> gateway connect uniswap

What chain do you want uniswap to connect to? (ethereum, polygon) >>> ethereum

Which network do you want uniswap to connect to? (mainnet, kovan, ropsten, arbitrum_one, optimism) >>> mainnet

Do you want to continue to use `https://rpc.ankr.com/eth` for ethereum-mainnet? (Yes/No) >>> Yes

Enter your ethereum-mainnet wallet private key >>> *******************************
The uniswap connector now uses wallet [public address] on ethereum-mainnet.
```

![1665312003280](https://user-images.githubusercontent.com/7695325/194752175-86ad2ab0-1584-4c89-938d-ddb6219e878e.png)

Now, you can type command `exit` in hummingbot client to quit now. When you exit and restart Hummingbot, it should automatically detect whether Gateway is running and connect to it. Please refer to: https://docs.hummingbot.org/developers/gateway/setup/#4-connect-wallet if has problem.
