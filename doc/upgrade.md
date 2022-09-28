# How to upgrade hummingbot from old version

## Get source codes from xdc

```shell
export HUMMINGBOT_PATH=${HUMMINGBOT_PATH:-"hummingbot.xdc"}
cd ${HOME}/${HUMMINGBOT_PATH}
git remote add xdc https://github.com/Carry-So/hummingbot.git
git fetch xdc
git checkout xdc-release-1.8.0
```

## Upgrade hummingbot client

```shell
./clean
./install
conda activate hummingbot
./compile
```

## Change gateway port in hummingbot client

Then change gateway port in file `conf/conf_client.yml` to 15888, the default value is 5000 in old releases.

## Upgrade hummingbot gateway

```shell
cd gateway
yarn
yarn build
```

## Clean old conf in gateway

Then remove all configuration files(\*.yml) in directory `gateway/conf` except file `gateway/conf/ssl.yml`. Gateway will recreate these files if not exist when start. Remember backup all files in case of downgrade.

```shell
cd gateway
mkdir conf.bak
mv conf/*.yml conf.bak/
cp conf.bak/ssl.yml conf
```
