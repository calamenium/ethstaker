This guide is a work in progress and is provided as is with no warranty. Use at your own risk.

It will cover the setup process to be ready for the Ropsten Ethereum testnet merge on June 8th, 2022.

# Prerequisites

- The Ubuntu 20.04 LTS operating system is used.

## Ubuntu and Security Preparations

### Ubuntu Updates

```
sudo apt update && sudo apt upgrade
sudo apt dist-upgrade && sudo apt autoremove
sudo reboot
```

### Firewall Settings

```
sudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 30303
sudo ufw allow 9000
sudo ufw enable
sudo ufw status numbered
```

### Install Java

```
sudo apt install default-jre default-jdk
```
### Install Git

```
sudo apt install git-all
```

### Create User

```
sudo useradd --no-create-home --shell /bin/false besu
mkdir /var/lib/besu/
sudo chown -R besu:besu /var/lib/besu/
```

### Download and Build Besu

```
git clone -b main https://github.com/hyperledger/besu --recursive
cd besu
./gradlew installDist distTar
sudo cp -a build/install/besu/. /usr/local/bin/besu
cd ~
```

### Create Systemd File

```
sudo nano /etc/systemd/system/besu.service
```

Copy and paste the following into editor, save:

```
[Unit]
Description=Ethereum Besu execution client
After=network.target
Before=network.target

[Service]
User=besu
Group=besu
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx4g"
ExecStart=/usr/local/bin/besu/bin/besu \
    --data-path=/var/lib/besu/ \
    --rpc-http-enabled \
    --rpc-http-api=ADMIN,MINER,ETH,NET,DEBUG,TXPOOL \
    --rpc-http-cors-origins="*" \
    --p2p-enabled=true \
    --network=ROPSTEN \
    --data-storage-format=BONSAI \
    --miner-enabled=true \
    --miner-stratum-enabled \
    --miner-coinbase="0x37efA76D983911e833d1BA9044d682A96A6c0EEE" \
    --bootnodes=enode://6332792c4a00e3e4ee0926ed89e0d27ef985424d97b6a45bf0f23e51f0dcb5e66b875777506458aea7af6f9e4ffb69f43f3778ee73c81ed9d34c51c4b16b0b0f@52.232.243.152:30303,enode://94c15d1b9e2fe7ce56e458b9a3b672ef11894ddedd0c6f247e0f1d3487f52b66208fb4aeb8179fce6e3a749ea93ed147c37976d67af557508d199d9594c35f09@192.81.208.223:30303

[Install]
WantedBy=multi-user.target
```
- `rpc-http-cors-origins="*"` allows using metamask localhost network option.

### Enable Systemctl

```
sudo systemctl daemon-reload
sudo systemctl start besu
sudo systemctl status besu
sudo systemctl enable besu
sudo journalctl -fu besu.service -o cat | ccze -A
```

### Connecting to Peers, Syncing Blockchain

- The Ropsten network will begin to sync once enough peers are found.

## References used
- Somer Esat's Guides - https://github.com/SomerEsat/ethereum-staking-guide
- Luis Naranjo's Guides - https://luisnaranjo733.hashnode.dev/want-to-help-test-the-merge-run-a-node-on-kintsugi
