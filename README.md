</p>
<p style="font-size:14px" align="right">
<a href="https://discord.gg/NtFMGGRn" target="_blank">Join Point Network Discord<img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
</p>

<p style="font-size:14px" align="right">
<a href="https://t.me/bangpateng_group" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
<a href="https://bangpateng.com/" target="_blank">Visit our website <img src="https://user-images.githubusercontent.com/38981255/184068977-2d456b1a-9b50-4b75-a0a7-4909a7c78991.png" width="30"/></a>
<a href="www.indonode.dev/" target="_blank">Visit my website <img src="https://avatars.githubusercontent.com/u/34649601?v=4" width="30"/></a>
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/38981255/185550018-bf5220fa-7858-4353-905c-9bbd5b256c30.jpg">
</p>

# #Point Network Testnet Incentivized

## Automatic Install ##
```
wget -O point.sh https://raw.githubusercontent.com/elangrr/point-auto/main/point.sh && chmod +x point.sh && ./point.sh
```
## Setelah install load variable!!!
```
source $HOME/.bash_profile
```

### Check info Sync
```
evmosd status 2>&1 | jq .SyncInfo
```

### KALAU UDAH SELESAI INSTALL LANGSUNG KE STEP ##BUAT DOMPET(2)


### MANUAL INSTALL
## Setting up vars
```
NODENAME=<MONIKER>
```
Ganti <MONIKER> dengan moniker sesuka kalian

```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export POINT_CHAIN_ID=point_10721-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages

```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies

```
sudo apt install curl build-essential git wget jq make gcc tmux -y
```

## Install Go

```
if ! [ -x "$(command -v go)" ]; then
  ver="1.18.2"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
fi
```

## Download and build binaries

```
cd $HOME
git clone https://github.com/pointnetwork/point-chain && cd point-chain
git checkout xnet-triton
make install
```

## Install Node

```
evmosd config chain-id $POINT_CHAIN_ID
evmosd config keyring-backend file
```

## Init App

```
evmosd init $NODENAME --chain-id $POINT_CHAIN_ID
```

## Download genesis and addrbook

```
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/config.toml
wget https://raw.githubusercontent.com/pointnetwork/point-chain-config/main/testnet-xNet-Triton-1/genesis.json
mv config.toml genesis.json ~/.evmosd/config/
```

Validasi:

```
evmosd validate-genesis
```

## Create Service

```
sudo tee /etc/systemd/system/evmosd.service > /dev/null <<EOF
[Unit]
Description=evmos
After=network-online.target

[Service]
User=$USER
ExecStart=$(which evmosd) start --home $HOME/.evmosd
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service

```
sudo systemctl daemon-reload
sudo systemctl enable evmosd
sudo systemctl restart evmosd && sudo journalctl -u evmosd -f -o cat
```

## Buat dompet(2)

Untuk buat wallet baru kalian bisa menggunakan perintah di bawah dan Jangan lupa simpan mnemonicnya Validator

```
evmosd keys add $WALLET
```

(OPSIONAL) Untuk recover wallet kalian pakek pharse

```
evmosd keys add $WALLET --recover
```

Untuk mendapatkan daftar wallet saat ini

```
evmosd keys list
```

## load variable Wallet

```
POINT_WALLET_ADDRESS=$(evmosd keys show $WALLET -a)
```
Masukan Password wallet
```
POINT_VALOPER_ADDRESS=$(evmosd keys show $WALLET --bech val -a)
```
Masukan password wallet
```
echo 'export POINT_WALLET_ADDRESS='${POINT_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export POINT_VALOPER_ADDRESS='${POINT_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Minta Faucet ke Address Metamask (Kalo Udah Sekip)

- Isi Form : https://pointnetwork.io/testnet-form (Tunggu 24 Jam Akan Dapat Email dan Coin Test Masuk ke Metamask)
- Add RPC di Metamask (Untuk Memastikan Udah Ada Faucet Landing)

```
Network Title: Point XNet Triton
RPC URL: https://xnet-triton-1.point.space/
Chain ID: 10721
SYMBOL: XPOINT
```

### Tambahkan dompet dengan 1024 XPOINT Anda

Ingat wallet yang dimasukkan ke dalam form ? Sekarang harusnya ada 1024 XPOINT.

Impor dompet dengan kunci pribadi ke dompet Anda (misalnya Metamask), dan Anda akan melihat 1024 XPOINT di sana. tapi ini dompet buat taro dana, bukan dompet validator, dompet validator itu beda lagi.

### Cari tahu alamat mana yang menjadi dompet validator Anda

Evmos memiliki dua format dompet: format Cosmos, dan format Ethereum. Format Cosmos dimulai dengan awalan `evmos`, dan format Ethereum dimulai dengan 0x. Kebanyakan orang tidak perlu tahu tentang format Cosmos, tetapi validator harus memiliki cara untuk mengubah dari satu ke yang lain.

Jalankan `evmosd keys list`, dan Anda akan melihat daftar kunci yang dilampirkan ke node Anda. Lihat yang memiliki name `Validator kalian`, dan catat alamatnya (harus dalam format Cosmos dan dimulai dengan evmosawalan).

(Dalam kebanyakan kasus itu tidak diperlukan, tetapi jika terjadi kesalahan dan jika Anda ingin mengimpor dompet validator Anda di Metamask Anda, kalian butuh private key. buat dapetin private keynya jalanin command ini :` evmosd keys unsafe-export-eth-key validatorkey --keyring-backend file`

Gunakan alat ini untuk mengonversinya ke format Ethereum: https://evmos.me/utils/tools

Ini adalah alamat validator Anda dalam format Ethereum.

### Mendanai validator

Terakhir, gunakan wallet dana untuk mengirim sebanyak yang Anda butuhkan dari wallet dana kalian ke wallet validator (kalian bisa kirim semua : 1024, atau memilih strategi yang berbeda).

### Delegate XPOINT dan Bergabunglah sebagai Validator

Sekarang kalian harus nunggu node untuk synced, karena jika tidak, jumlah point nya gk akan ke detect atau " [] ".

Setelah node synced,kalian punya beberapa XPOINT untuk di delegate, periksa saldo Anda di node, Anda akan melihat saldo Anda di Metamask atau Anda dapat memeriksa saldo Anda dengan perintah ini:

`evmosd query bank balances $POINT_WALLET_ADDRESS`

## Buat Validator (Pastikan Status node False Dan Saldo Udah Ada)

### Check Status (Jika Sudah False dan Token Sudah Landing baru Buat Validator)

```
evmosd status 2>&1 | jq .SyncInfo
```

### Check Saldo

```
evmosd query bank balances $POINT_WALLET_ADDRESS
```
Jika Command di atas Error `$POINT_WALLET_ADDRESS` menjadi `Address Kalian`

## Create Validator Nya

```
evmosd tx staking create-validator \
  --amount 1000000000000000000000apoint \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1000000000000000000000" \
  --pubkey  $(evmosd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $POINT_CHAIN_ID
```
note : 1000000000000000000000=100point

## Delete Node (Permanent)

```
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmos* -rf
sudo rm $(which evmosd) -rf
sudo rm $HOME/.evmosd -rf
sudo rm $HOME/point-chain -rf
```
