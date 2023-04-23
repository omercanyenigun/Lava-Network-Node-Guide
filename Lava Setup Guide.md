**Lava-Network-Node-Guide (Cosmovisor)**


![alt text](https://i.hizliresim.com/2gb6pg0.png)


**Minimum Sistem Gereksinimleri**

- **4 CPU**
- **8GB RAM**
- **160GB DISK**
- **Ubuntu 20.04+**


- **Gerekli Güncellemeler**

```
sudo apt update
```

```
sudo apt install -y unzip logrotate git jq sed wget curl coreutils systemd
```

- **Go Kurulum**

```
wget https://golang.org/dl/go1.19.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.19.3.linux-amd64.tar.gz
rm go1.19.3.linux-amd64.tar.gz
```

```
echo "export PATH=\$PATH:/usr/local/go/bin" >>~/.profile
echo "export PATH=\$PATH:\$(go env GOPATH)/bin" >>~/.profile
source ~/.profile
```
- **Go Versiyon Kontrol**

```
go version
```

![alt text](https://i.hizliresim.com/hs3sbdm.png)

```
git clone https://github.com/K433QLtr6RA9ExEq/GHFkqmTzpdNLDd6T.git
```
- **testnet-1 klasörüne gir**

```
cd GHFkqmTzpdNLDd6T/testnet-1
```

```
source setup_config/setup_config.sh
```

```
echo "Lava config file path: $lava_config_folder"
mkdir -p $lavad_home_folder
mkdir -p $lava_config_folder
cp default_lavad_config_files/* $lava_config_folder
```

```
cp genesis_json/genesis.json $lava_config_folder/genesis.json
```

- **moniker-isminiz ve cüzdan-isminizi belirlerin**

```
LAVA_CHAIN="lava-testnet-1"
LAVA_MONIKER="moniker-isminiz"
LAVA_WALLET="cüzdan-isminiz"
```

- **Cosmovisor Kurulum**

```
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0
```

```
mkdir -p $lavad_home_folder/cosmovisor
```

```
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/cosmovisor-upgrades/cosmovisor-upgrades.zip
```

```
unzip cosmovisor-upgrades.zip
```

```
cp -r cosmovisor-upgrades/* $lavad_home_folder/cosmovisor
```

```
echo "# Setup Cosmovisor" >> ~/.profile
echo "export DAEMON_NAME=lavad" >> ~/.profile
echo "export CHAIN_ID=$LAVA_CHAIN" >> ~/.profile
echo "export DAEMON_HOME=$HOME/.lava" >> ~/.profile
echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.profile
echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
echo "export UNSAFE_SKIP_BACKUP=true" >> ~/.profile
source ~/.profile
```

- **Genesis Dosyası**

```
$lavad_home_folder/cosmovisor/genesis/bin/lavad init \
$LAVA_MONIKER \
--chain-id $LAVA_CHAIN \
--home $lavad_home_folder \
--overwrite
cp genesis_json/genesis.json $lava_config_folder/genesis.json
```

![alt text](https://i.hizliresim.com/tgczsug.png)

- **Symlinks Oluşturma**
```
ln -s $HOME/.lava/cosmovisor/genesis $HOME/.lava/cosmovisor/current
sudo ln -s $HOME/.lava/cosmovisor/current/bin/lavad /usr/local/bin/lavad
```
- **Servis Dosyası**

```
echo "[Unit]
Description=Cosmovisor daemon
After=network-online.target
[Service]
Environment="DAEMON_NAME=lavad"
Environment="DAEMON_HOME=${HOME}/.lava"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="UNSAFE_SKIP_BACKUP=true"
User=$USER
ExecStart=${HOME}/go/bin/cosmovisor start --home=$lavad_home_folder --p2p.seeds $seed_node
Restart=always
RestartSec=3
LimitNOFILE=infinity
LimitNPROC=infinity
[Install]
WantedBy=multi-user.target
" >cosmovisor.service
sudo mv cosmovisor.service /lib/systemd/system/cosmovisor.service
```

```
sudo systemctl daemon-reload
sudo systemctl enable cosmovisor.service
sudo systemctl restart systemd-journald
sudo systemctl start cosmovisor
```

- **Cosmovisor Kontrol**

```
sudo systemctl status cosmovisor
```
![alt text](https://i.hizliresim.com/550uoy3.png)

#CTRL-C ile çıkış yapabilirsiniz.

- **Cüzdan Oluşturma**

```
lavad keys add $LAVA_WALLET
```
![alt text](https://i.hizliresim.com/fj1spis.png)

#Mnemoniclerinizi kaydetmeyi unutmayın!

- **Faucetten Token İsteme**

#Lava Discord Faucet kanalından $request <cüzdan-adresiniz> ile token isteyin

**Lava Discord**  **https://discord.gg/jxjeamNe**

- **Logları Görüntüleme**

```
sudo journalctl -u cosmovisor -f
```

- **Senkronizasyon Kontrolü**

```
lavad status | jq
```
![alt text](https://i.hizliresim.com/aflioiq.png)

#Senkronizasyon için latest_block_height değeri son blockta ve catching_up false olmalı

- **Validatör Oluşturma**

```
lavad tx staking create-validator --yes \
 --amount 50000ulava \
 --moniker $LAVA_MONIKER \
 --commission-rate "0.10" \
 --commission-max-rate "0.20" \
 --commission-max-change-rate "0.01" \
 --min-self-delegation "1" \
 --pubkey "$(lavad tendermint show-validator)" \
 --from $LAVA_WALLET \
 --chain-id $LAVA_CHAIN
```

#50000ulava olan token adedini cüzdanınızdaki token adedine göre ayarlayın.


![alt text](https://i.hizliresim.com/d9w2quz.png)


**Diğer Gerekli Kodlar**

- **Cüzdan Bakiye Kontrol**

```
lavad query bank balances <cüzdan-adresiniz>
```

![alt text](https://i.hizliresim.com/k70j58a.png)

- **Cüzdan Listesi**

```
lavad  keys list
```

![alt text](https://i.hizliresim.com/e93p95w.png)


- **Validatör Ödüllerini Çekme**

```
lavad tx distribution withdraw-rewards <valoper-adresiniz> --from $LAVA_WALLET --commission --chain-id $LAVA_CHAIN --gas auto --fees 4000ulava
```

- **Başka Cüzdana Token Gönderme**

```
lavad tx bank send <cüzdan-adresiniz> <gönderilecek-cüzdan-adresi> 500000000ulava --chain-id $LAVA_CHAIN --gas auto --fees 4000ulava
```

- **Keplr Wallet'a Lava Testnet Ağı Ekleme**

- **https://docs.lavanet.xyz/wallet/** sitesinde ADD LAVA TO KEPLR butonuna basıp çıkan Pop-up'a Approve Diyerek Ekleyebilirsiniz

![alt text](https://i.hizliresim.com/dbe5a32.png)

- **Lava Cüzdan Mnemoniclerinizi Keplr Wallet'a İmport Ederek Cüzdanınızı Keplr Wallet'ta Görebilirsiniz**

![alt text](https://i.hizliresim.com/rxrgzjs.png)

- **Explorer** - **https://lava.explorers.guru/validators**

- **https://t.me/testnetrun**

- **https://www.youtube.com/@TestNetRun**

- **https://testnet.run/**

- **https://stake.testnet.run/**

- **https://twitter.com/testnetrun**








