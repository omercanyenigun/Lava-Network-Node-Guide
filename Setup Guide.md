**Lava-Network-Node-Guide**


![alt text](https://i.hizliresim.com/e5qbrvl.png)


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

```
ver="1.19.3"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
```

```
go version
```

#go version go1.19 olmalı.

```
cd $HOME
git clone https://github.com/K433QLtr6RA9ExEq/GHFkqmTzpdNLDd6T.git
wget https://lava-binary-upgrades.s3.amazonaws.com/testnet/v0.4.0/lavad
chmod +x lavad
mv lavad $HOME/go/bin/
```

```
lavad version --long | head
```

#version: 0.4.0-rc2-e2c69db çıktı vermeli


#monikeradınız yerine kendinize göre isim verin

```
lavad init "MonikerAdınız" --chain-id lava-testnet-1
lavad config chain-id lava-testnet-1
```

#cüzdan oluşturun ve ismini belirleyin

```
lavad keys add <Cuzdanismi>
```

#cüzdan bilgilerini kaydetmeyi unutmayın
#Lava Network Discord faucet kanalından token isteyin $request ile

**Genesis**

``` 
cp $HOME/GHFkqmTzpdNLDd6T/testnet-1/genesis_json/genesis.json $HOME/.lava/config
```

**Chain Ayarlama**

```
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
```

**Peer Ekleme**

```
PEERS="4bfb0d4d945985d2cc92ea4ba3578459b80f1dab@190.2.155.67:33656,ac7cefeff026e1c616035a49f3b00c78da63c2e9@18.215.128.248:26656,6c988ad39fef48abd5504fda547d561fb8a60c3a@130.185.119.243:33656,2c2353c872b0c5af562c518b1aa48a2649a4c927@65.108.199.62:11656,4f9120f706512162fbe4f39aac78b9924efbec58@65.109.92.235:11036,f9190a58670c07f8202abfd9b5b14187b18d755b@144.76.97.251:27656,f120685de6785d8ee0eadfca42407c6e10593e74@144.76.90.130:32656,6641a193a7004447c1b49b8ffb37a90682ce0fb9@65.108.78.116:13656,c19965fe8a1ea3391d61d09cf589bca0781d29fd@162.19.217.52:26656,0516c4d11552b334a683bdb4410fa22ef7e3f8ba@65.21.239.60:11656,dabe2e77bd6b9278f484b34956750e9470527ef7@178.18.246.118:26656,24a2bb2d06343b0f74ed0a6dc1d409ce0d996451@188.40.98.169:27656,b7c3cedc778d93296f179373c3bc6a521e4b682e@65.109.69.160:30656,c678ae0fd7b754615e55bba2589a86e60fc8d45c@136.243.88.91:7140,a65de5f01394199366c182a18d718c9e3ef7f981@159.148.146.132:26656,cc2b2250b21cd6d23305143a32181e5f6bfc5956@135.181.50.187:26656,0561fed6e88f2167979e379436529861527d859d@65.109.92.148:61256,2b5d760125c90970ce27f4783a5d70a19534ff61@146.19.24.101:26546,3c47fd1662bcb17a4713c23e41d7b25e34478b8e@103.19.25.157:26672,131227f65bbc8f5b86030124fa1610a3283ebcbd@135.181.176.109:26656,3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@3.252.219.158:26656,5a469a75fb05eddf2d79fb17063cc59e84d0821a@207.180.236.115:34656,0314d53cc790860fb51f36ac656a19789800ce5c@176.103.222.20:26656,14ae45e7f2ff7491cfa686a8fcac7cc095bc38ff@213.239.217.52:39656,28db9a9c200bedbe5d322f7571462f1146ef898e@209.126.2.184:26656,de764d94d3eed3ac15c2151b5576dd24de5bec81@38.242.236.179:26656,57474bd0977b3ed65cf23086b6d1d92bf00d50d0@207.180.236.122:31656,0efa60456219f5b7847ee21439aa8662c0a8e1b6@65.21.195.40:26656,f22ea1e7b6d31966259e99177d714cffde27c4bf@152.32.211.182:26656,024a0b0a6eae16a2e8aaefcf26b12d2a3b393b28@75.119.155.60:26656,2db2e00432fc950fa2afa03a84288a437fc1c305@2.58.82.212:26656,9a8477637f7944f2537234bbfb6e1559b7805157@195.3.221.13:56656,0a528da95ca8025ef4043b6e73f1e789f4102940@176.103.222.22:26656,90451ff8f47b8f4b077e95837f112135fea14531@207.180.231.123:31656,529675163b5d16838928fe10edce5ef827ff591f@46.4.68.113:25556,6625725632a9343c1d412bc2ec5d76b0cf8289ef@65.21.245.235:26656,2031e65ee8a13e57d922a14d28d67be0ada21a95@54.194.240.43:26656,464e98fa27165f3a13f4173c0ecfbe71ce8f1bf2@167.86.94.71:36656,897fbd850aff33b4d5012d30a8b0ce04225f907f@2.58.82.231:26656,e4c705abed2f76830652799d2eb386a9b876daff@168.119.52.60:26656,151cc6fb6d1a4a4c2f76f7eaf43b9ea80d62ec7b@95.214.55.46:22626,6b1d0465b3e2a32b5328e59eb75c38d88233b56f@80.82.215.19:60656,d3001223151430f204917eb87f86d0bd1e795ebf@161.97.162.6:26656,5b25ec3860445e50a41a80850970b3241350df72@194.233.90.134:26656,c210ed8ecd986a7cc19e87a34c5a2a0f87f1a45c@185.48.24.106:29656,3b3a633e4ad83914a64288dca82f7a7b62536820@65.21.193.112:38656,a76af03d79a90992d135750ab945f79f167d6ee4@65.109.139.182:26656,4f97a7b7d386dc6cc4b4a7239cf76be3c507a1c8@173.212.243.149:26656,9cb975a6676f38577f3888cbc3d7306df3e95f1f@176.120.177.123:26656,c80f5f3b6828342ed2c38026eede1f59b466d30f@168.119.124.130:47656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.lava/config/config.toml
```

**Servis Dosyası**

```
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

```
sudo systemctl daemon-reload
sudo systemctl enable lavad
sudo systemctl restart lavad && sudo journalctl -u lavad -f -o cat
```


**Validatör Oluşturma**

```
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
```

**Explorer**

- **https://lava.explorers.guru/validators**







