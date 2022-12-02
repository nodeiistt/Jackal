#!/bin/bash
echo "=================================================="
echo "welcome to the skynodes auto script"
echo "www.skynodes.org"
echo "www.githup.com/0xMrmoon"
echo "Good Luck"
echo "=================================================="
sleep 5

# Variables by skynodes
JKL_WALLET=wallet
JKL=canined
JKL_ID=lupulella-2
JKL_PORT=50
JKL_FOLDER=.canine
JKL_FOLDER2=canine-chain
JKL_VER=v1.2.0-alpha.4
JKL_REPO=https://github.com/JackalLabs/canine-chain
JKL_GENESIS=https://raw.githubusercontent.com/JackalLabs/jackal-chain-assets/main/testnet/genesis.json
JKL_ADDRBOOK=curl -Ls https://snapshots.kjnodes.com/jackal-testnet/addrbook.json > $HOME/.canine/config/addrbook.json
JKL_MIN_GAS=0
JKL_DENOM=ujkl
JKL_SEEDS=
JKL_PEERS=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:37656,9b2bbd5121265ebbf9003341e8a2e0abdbc24b67@46.228.199.8:26656,5eedbfbe64b942f4ab54db3842acf3bfab034c24@161.97.74.88:46656,09d9127972ded9e22f9f11833ed7fcfa149cf1fa@65.109.92.240:19126,0394449cab5a29f24dd4f37683d3b7622f27c0fc@65.108.206.118:61156,94b63fddfc78230f51aeb7ac34b9fb86bd042a77@95.217.89.23:30567


sleep 1

echo "export JKL_WALLET=${JKL_WALLET}" >> $HOME/.bash_profile
echo "export JKL=${JKL}" >> $HOME/.bash_profile
echo "export JKL_ID=${JKL_ID}" >> $HOME/.bash_profile
echo "export JKL_PORT=${JKL_PORT}" >> $HOME/.bash_profile
echo "export JKL_FOLDER=${JKL_FOLDER}" >> $HOME/.bash_profile
echo "export JKL_FOLDER2=${JKL_FOLDER2}" >> $HOME/.bash_profile
echo "export JKL_VER=${JKL_VER}" >> $HOME/.bash_profile
echo "export JKL_REPO=${JKL_REPO}" >> $HOME/.bash_profile
echo "export JKL_GENESIS=${JKL_GENESIS}" >> $HOME/.bash_profile
echo "export JKL_PEERS=${JKL_PEERS}" >> $HOME/.bash_profile
echo "export JKL_SEED=${JKL_SEED}" >> $HOME/.bash_profile
echo "export JKL_MIN_GAS=${JKL_MIN_GAS}" >> $HOME/.bash_profile
echo "export JKL_DENOM=${JKL_DENOM}" >> $HOME/.bash_profile
source $HOME/.bash_profile

sleep 1

if [ ! $JKL_NODENAME ]; then
	read -p "YOUR_MONIKER_GOES_HERE: " JKL_NODENAME
	echo 'export JKL_NODENAME='$JKL_NODENAME >> $HOME/.bash_profile
fi

echo -e "Your Node Name: \e[1m\e[32m$JKL_NODENAME\e[0m"
echo -e "Wallet: \e[1m\e[32m$JKL_WALLET\e[0m"
echo -e "Chain: \e[1m\e[32m$JKL_ID\e[0m"
echo -e "Port: \e[1m\e[32m$JKL_PORT\e[0m"

sleep 2


#Updates
echo -e "\e[1m\e[32m1. ... \e[0m" && sleep 1
sudo apt update && sudo apt upgrade -y


#Install Packages skynodes
echo -e "\e[1m\e[32m2. Packages... \e[0m" && sleep 1
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y

# GO 
echo -e "\e[1m\e[32m1. GO. \e[0m" && sleep 1
ver="1.18.2"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version


sleep 1

# Library Skynodes
echo -e "\e[1m\e[32m1. REPO ... \e[0m" && sleep 1
cd $HOME
git clone $JKL_REPO
cd $JKL_FOLDER2
git checkout $JKL_VER
make install

sleep 1

# Conf skynodes
echo -e "\e[1m\e[32m1. conf ... \e[0m" && sleep 1
$JKL config chain-id $JKL_ID
$JKL config keyring-backend file
$JKL init $JKL_NODENAME --chain-id $JKL_ID

# ADDRBOOK ve GENESIS by Nodeist
wget $JKL_GENESIS -O $HOME/$JKL_FOLDER/config/genesis.json
wget $JKL_ADDRBOOK -O $HOME/$JKL_FOLDER/config/addrbook.json

# EŞLER VE TOHUMLAR by Nodeist
SEEDS="$JKL_SEEDS"
PEERS="$JKL_PEERS"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/$JKL_FOLDER/config/config.toml

sleep 1


# config pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/$JKL_FOLDER/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/$JKL_FOLDER/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/$JKL_FOLDER/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/$JKL_FOLDER/config/app.toml


# PORT by skynodes
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:2${JKL_PORT}8\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:2${JKL_PORT}7\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${JKL_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:2${JKL_PORT}6\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":2${JKL_PORT}0\"%" $HOME/$JKL_FOLDER/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${JKL_PORT}7\"%; s%^address = \":8080\"%address = \":${JKL_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${JKL_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${JKL_PORT}91\"%" $HOME/$JKL_FOLDER/config/app.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:2${JKL_PORT}7\"%" $HOME/$JKL_FOLDER/config/client.toml

#
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/$JKL_FOLDER/config/config.toml

#min gas
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00125$JKL_DENOM\"/" $HOME/$JKL_FOLDER/config/app.toml

#indexer 
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/$JKL_FOLDER/config/config.toml

# res
$JKL tendermint unsafe-reset-all --home $HOME/$JKL_FOLDER

echo -e "\e[1m\e[32m4. SERVIS BASLATILIYOR... \e[0m" && sleep 1
# create service
sudo tee /etc/systemd/system/$JKL.service > /dev/null <<EOF
[Unit]
Description=$JKL
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which $JKL) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF


# start service  skynodes
sudo systemctl daemon-reload
sudo systemctl enable $JKL
sudo systemctl restart $JKL

echo ' setup is okey with skynodes  www.skynodes.org'
echo -e 'check your logs: \e[1m\e[32mjournalctl -fu canined -o cat\e[0m'
echo -e "chech your sync: \e[1m\e[32mcurl -s localhost:${JKL_PORT}657/status | jq .result.sync_info\e[0m"

source $HOME/.bash_profile
