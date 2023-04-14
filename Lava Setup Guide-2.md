![alt text](https://i.hizliresim.com/e5qbrvl.png)


**Minimum Sistem Gereksinimleri**

- **4 CPU**
- **8GB RAM**
- **160GB DISK**
- **Ubuntu 20.04+**

**Gerekli Güncellemeler**

```python
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

- **Go Kurulum ve Versiyon Kontrolü**

```python
ver="1.19" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
Version: 1.19 çıkmalı


- **Yapılandırma**

```python
cd $HOME
git clone https://github.com/lavanet/lava
cd lava
git fetch --all
git checkout v0.8.1
make install
```

- **Güncelleme**

```python
cd $HOME/lava
git fetch --all
git checkout v0.8.1
make install
lavad version --long | head
sudo systemctl restart lavad
```
version: 0.8.1 çıkmalı


- **Moniker ve Chain Belirleme**

```python
lavad init Testnet --chain-id lava-testnet-1
lavad config chain-id lava-testnet-1
```


- **Cüzdan Oluşturma veya İmport Etme**

```python
lavad keys add wallet
```

- **Eğer kendi cüzdanınızı import etmek isterseniz**

```python
lavad keys add wallet --recover
```


- **Cüzdan Listesi**

```python
lavad  keys list
```


- **Faucetten Token İsteme**

#Lava Discord Faucet kanalından $request <cüzdan-adresiniz> ile token isteyin

**Lava Discord**  **https://discord.gg/fmAtVh5MJG**

- **Bakiye Kontrolü**

```python
lavad query bank balances cüzdanadresi
```

- **Genesis Dosyası** 

```python
wget -O ~/.lava/config/genesis.json https://raw.githubusercontent.com/omercanyenigun/Lava-Network-Node-Guide/main/genesis.json
```

- **Minimum Gas Fiyatı, Peers, Seeds Ayarlama**

```python
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ulava\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.lava/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.lava/config/config.toml
peers="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.lava/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.lava/config/config.toml
sed -i 's/create_empty_blocks_interval = ".*s"/create_empty_blocks_interval = "60s"/g' ~/.lava/config/config.toml
sed -i 's/timeout_propose = ".*s"/timeout_propose = "60s"/g' ~/.lava/config/config.toml
sed -i 's/timeout_commit = ".*s"/timeout_commit = "60s"/g' ~/.lava/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "601s"/g' ~/.lava/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.lava/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.lava/config/config.toml
```

- **Pruning (budama) isteğe bağlı**

```python
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="10" && \
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.lava/config/app.toml && \
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.lava/config/app.toml && \
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.lava/config/app.toml && \
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.lava/config/app.toml
```

- **Indexer düzenleme (isteğe bağlı)**

```python
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.lava/config/config.toml
```

- **Addrbook Dosyası**

```python
wget -O $HOME/.lava/config/addrbook.json https://raw.githubusercontent.com/omercanyenigun/Lava-Network-Node-Guide/main/addrbook.json
```

- **Servis Dosyası Düzenleme**

```python
sudo tee /etc/systemd/system/lavad.service > /dev/null <<EOF
[Unit]
Description=lava
After=network-online.target

[Service]
User=$USER
ExecStart=$(which lavad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```


- **Node Başlatma**

```python
sudo systemctl daemon-reload
sudo systemctl enable lavad
```

- **Log Kontrolü**

```python
sudo systemctl restart lavad && sudo journalctl -u lavad -f -o cat
```

- **Senkronizasyon Kontrolü (çıktı false ise senkronizasyon olmuştur)**

```python
lavad status 2>&1 | jq .SyncInfo
```

- **Validatör Oluşturma**

```python
lavad tx staking create-validator \
  --amount 1000000ulava \
  --from wallet \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(lavad tendermint show-validator) \
  --moniker Testnet \
  --chain-id lava-testnet-1 \
  --identity="" \
  --details="" \
  --website="" -y
```

- **Validatör Ödüllerini Çekme**

```python
lavad tx distribution withdraw-rewards <valoper-adresiniz> --from $LAVA_WALLET --commission --chain-id $LAVA_CHAIN --gas auto --fees 4000ulava
```

- **Başka Cüzdana Token Gönderme**

```python
lavad tx bank send <cüzdan-adresiniz> <gönderilecek-cüzdan-adresi> 500000000ulava --chain-id $LAVA_CHAIN --gas auto --fees 4000ulava
```



- **Keplr Wallet'a Lava Testnet Ağı Ekleme**

- **https://docs.lavanet.xyz/wallet/** sitesinde ADD LAVA TO KEPLR butonuna basıp çıkan Pop-up'a Approve Diyerek Ekleyebilirsiniz

![alt text](https://i.hizliresim.com/dbe5a32.png)

- **Lava Cüzdan Mnemoniclerinizi Keplr Wallet'a İmport Ederek Cüzdanınızı Keplr Wallet'ta Görebilirsiniz**

![alt text](https://i.hizliresim.com/rxrgzjs.png)

- **Explorer** - **https://lava.explorers.guru/validators**
