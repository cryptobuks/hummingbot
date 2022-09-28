# Run hummingbot after setup

Pelase read https://docs.hummingbot.org/ for other operations.

## 1) Environment

-   OS: Ubuntu 22.04
-   hummingbot:
    -   version: v1.8.0-xdc
    -   path: `${HOME}/${HUMMINGBOT_PATH}`

## 2) Start hummingbot gateway

Run the following commands to start hummingbot gateway in the first terminal:

```shell
export HUMMINGBOT_PATH=${HUMMINGBOT_PATH:-"hummingbot.xdc"}
cd ${HOME}/${HUMMINGBOT_PATH}/gateway

# Start server using certs passphrase, such as: `yarn start --passphrase=daniel`
yarn start --passphrase=<PASSWORD>
```

![1665319200317](https://user-images.githubusercontent.com/7695325/194757336-6c7e4674-dc3b-4715-90f2-8c2294e63e2c.png)

## 3) Start hummingbot client

Run the following commands in the second terminal:

```shell
export HUMMINGBOT_PATH=${HUMMINGBOT_PATH:-"hummingbot.xdc"}
cd ${HOME}/${HUMMINGBOT_PATH}
conda activate hummingbot
bin/hummingbot.py
```

![1665319335702](https://user-images.githubusercontent.com/7695325/194757438-e5fdced4-e74a-433a-9e8a-46a42a4b8f59.png)

## 4) connect xsswap

Run the command `gateway connect xsswap` hummingbot client.

![1666349852294](https://user-images.githubusercontent.com/7695325/197180516-8a2c2973-89e9-41d8-bd6e-c40b0ff8c72b.png)
