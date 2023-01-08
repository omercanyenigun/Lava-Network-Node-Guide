**Lava-Network-Node-Guide**


**Minimum Sistem Gereksinimleri**

- **4 CPU**
- **8GB RAM**
- **160GB DISK**
- **Ubuntu 20.04+**


**Gerekli Güncellemeler**

```
sudo apt update && sudo apt upgrade -y
```

```
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

&& sudo tar -xvf go1.19.linux-amd64.tar.gz && sudo mv go /usr/local \
&& echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile \
&& source ~/.bash_profile


go version

# go version go1.19 olmalı.


cd $HOME
git clone https://github.com/K433QLtr6RA9ExEq/GHFkqmTzpdNLDd6T.git
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.3.0/lavad
chmod +x lavad
mv lavad $HOME/go/bin/


**Güncelleme**


cd $HOME
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.4.0/lavad
chmod +x lavad
mv lavad $HOME/go/bin/
sudo systemctl restart lavad && sudo journalctl -u lavad -f -o cat


lavad version --long | head

#version: 0.4.0-rc2-e2c69db çıktı vermeli


#monikeradınız yerini kendinize göre isim verin

lavad init "MonikerAdınız" --chain-id lava-testnet-1
lavad config chain-id lava-testnet-1

#cüdan oluşturun ve ismini belirleyin

lavad keys add <Cuzdanismi>

#cüzdan bilgilerini kaydetmeyi unutmayın
# Lava Network Discord faucet kanalından token isteyin örnek: $request <cuzdanadresi>


cp $HOME/GHFkqmTzpdNLDd6T/testnet-1/genesis_json/genesis.json $HOME/.lava/config


**Chain Ayarlama**


sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ulava\"/" $HOME/.lava/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.lava/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.lava/config/config.toml
peers="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.lava/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.lava/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.lava/config/config.toml


**Servis Dosyası**

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


sudo systemctl daemon-reload
sudo systemctl enable lavad
sudo systemctl restart lavad && sudo journalctl -u lavad -f -o cat


**Validatör Oluşturma**


lavad tx staking create-validator \
  --amount 1000000ulava \
  --from <cuzdanismi> \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(lavad tendermint show-validator) \
  --moniker <monikerismi> \
  --chain-id lava-testnet-1 \
  --identity="" \
  --details="" \
  --website="" -y







