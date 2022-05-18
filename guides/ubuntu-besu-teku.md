This guide is a work in progress and is provided as is with no warranty. Use at your own risk.

It will cover the setup process to be ready for the Ropsten Ethereum testnet merge on June 8th, 2022.

# Prerequisites

- The Ubuntu 20.04 LTS operating system is used.

## Ubuntu and Security Preparations

### Ubuntu Updates

`sudo apt update && sudo apt upgrade
sudo apt dist-upgrade && sudo apt autoremove
sudo reboot`

### Firewall Settings

`sudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 30303
sudo ufw allow 9000
sudo ufw enable
sudo ufw status numbered`

### Install Java

`sudo apt install default-jre default-jdk`

### Create User

`sudo useradd --no-create-home --shell /bin/false besu
mkdir /var/lib/besu/
sudo chown -R besu:besu /var/lib/besu/``

### Download and Build Besu

`git clone -b main https://github.com/hyperledger/besu --recursive
cd besu
./gradlew installDist distTar
sudo cp -a build/install/besu/. /usr/local/bin/besu
cd ~`

### Create Systemd File

`sudo nano /etc/systemd/system/besu.service`

Copy and paste the following into editor, save:

`[Unit]
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
    --p2p-enabled=true \
    --network=ROPSTEN \
    --data-storage-format=BONSAI \
    --bootnodes=enode://6f377dd1ef5a3272d7e02fac9064c4f95d74f7edfd866e59ded774ee5b4649ff61c3f24c95f5c3d07d692b447f0569716b8921b6861810b96a705c92e1d27ff9@161.35.67.219:30303,enode://08e5308bca664503e023e10ff39795041a5d49d0384a071554b155af1e82e99afc487c3ec25e41b8910f633039b193902d41ecd7ae42ca031929558eb8f94222@161.35.79.127:30303,enode://b7abf51b9496011789a59cf3ea6df653a4a2aadec9e152b81fbfbf704245ad624e99960c11ad38a6c5b30bb5791d7431477b375be806c24bcee4886124b54c05@64.225.4.226:30303,enode://371ef6897c38b451afd1864f1c3e6697d39ec9d509803a540195e873816c441cc82415a1f01c2c98441d0cbad06b2958e2aaa3c23b27304841f1492a04a6d47b@165.232.177.121:30303,enode://c20047e975f562131d0211b1a36f955b821663bd83a69edd725b221b70db0d01320716dd6a45d87e4e8afc1bc53439ff001212a70be0f1064db65c82d7ebbc9d@161.35.67.221:30303,enode://11b10968e46d30e3013b376e941eed90a3472ddebef0df004fa9dc20644e6b9fddb7ec1fe5aa06c57650e2752115e9f93c0049e4618b9f811acffb1ca7e402ec@161.35.75.78:30303,enode://57745805245c441b71a9f3b3e7d78f75dd576d36b236b9f64cf9a9cccdcb574ec1f64d69c05add598ef26e3d7f646534b4c9976ca53551f71ad579a472635086@165.232.185.207:30303

[Install]
WantedBy=multi-user.target`

### Enable Systemctl

`sudo systemctl daemon-reload
sudo systemctl start besu
sudo systemctl status besu
sudo systemctl enable besu
sudo journalctl -fu besu.service -o cat | ccze -A`

### Connecting to Peers, Syncing Blockchain

- The Ropsten network will begin to sync once enough peers are found.

## References used
- Somer Esat's Guides - https://github.com/SomerEsat/ethereum-staking-guide
- Luis Naranjo's Guides - https://luisnaranjo733.hashnode.dev/want-to-help-test-the-merge-run-a-node-on-kintsugi
