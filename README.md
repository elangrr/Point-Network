</p>
<p style="font-size:14px" align="right">
<a href="https://discord.gg/NtFMGGRn" target="_blank">Join Point Network Discord<img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
</p>

<p style="font-size:14px" align="right">
<a href="www.indonode.dev/" target="_blank">Visit my website <img src="https://avatars.githubusercontent.com/u/34649601?v=4" width="30"/></a>
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/38981255/185550018-bf5220fa-7858-4353-905c-9bbd5b256c30.jpg">
</p>

# Point Network Testnet Incentivized

## [Official Guide](https://github.com/pointnetwork/point-chain/blob/xnet-triton/VALIDATORS.md)
## [Try Point Network](https://pointnetwork.io/?i=366120433928)
## [Manual Install](https://github.com/elangrr/Point-Network/blob/main/Manual_install.md) , WARNING! The variable are different from official guide and this guide

## Automatic Install ##
```
wget -O point.sh https://raw.githubusercontent.com/elangrr/point-official/main/point.sh && chmod +x point.sh && ./point.sh
```
## After install please Load Variable! (Post Installation)
```
source $HOME/.bash_profile
```

### Check info Sync
Note : You have to synced to the lastest block , check the sync status with this command
```
evmosd status 2>&1 | jq .SyncInfo
```

## Create Wallet
Create validator wallet using this command, Dont forget to save the Mnemonic!
```
evmosd keys add $validatorkey --keyring-backend file
```
(OPTIONAL) To recover using your previous saved wallet
```
evmosd keys add $validatorkey --recover
```
To get current list of wallet
```
evmosd keys list --keyring-backend file
```
To get private key of validator wallet (SAVE IT SOMEWHERE SAFE!)
```
evmosd keys unsafe-export-eth-key validatorkey --keyring-backend file
```
## Safe wallet Info
```
EVMOSD_WALLET_ADDRESS=$(EVMOSD keys show $validatorkey -a)
EVMOSD_VALOPER_ADDRESS=$(EVMOSD keys show $validatorkey --bech val -a)
echo 'export EVMOSD_WALLET_ADDRESS='${EVMOSD_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export EVMOSD_VALOPER_ADDRESS='${EVMOSD_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
## Fund your wallet
Fill the form with your funds wallet info , NOT VALIDATOR WALLET!!!
[FAUCET FORM](https://pointnetwork.io/testnet-form) , Usually the funds will be sent 24 Hour

## Create Validator
Before creating validator please make sure you have the funds already in your wallet minimum 100 point
To check wallet balance :
```
evmosd query bank balances $EVMOSD_WALLET_ADDRESS
```
To create a validator with 1000point delegation use this command below :

```
evmosd tx staking create-validator \
--amount=1000000000000000000000apoint \
--pubkey=$(evmosd tendermint show-validator) \
--moniker=$NODENAME \
--chain-id=point_10721-1 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.01" \
--min-self-delegation="1000000000000000000000" \
--gas="400000" \
--gas-prices="0.025apoint" \
--from=validatorkey \
--keyring-backend file
```

## Monitoring your validator

Check TX HASH ( Which <txhash> is your txhash from the transaction
```
evmosd query tx <txhash>
```
If the transaction was correct you should instantly become part of the validators set. Check your pubkey first:
```
evmosd tendermint show-validator
```
You will see a key there, you can identify your node among other validators using that key:
```
evmosd query tendermint-validator-set
```
There you will find more info like your VotingPower that should be bigger than 0. Also you can check your VotingPower by running:
```
evmosd status
```

## Useful Commands
Check Logs
```
journalctl -fu evmosd -o cat
```
Start Service
```
sudo systemctl start evmosd
```
Stop Service
```
sudo systemctl stop evmosd
```
Restart Service
```
sudo systemctl restart evmosd
```
## Node Info
Synchronization info
```
evmosd status 2>&1 | jq .SyncInfo
```
Validator Info
```
evmosd status 2>&1 | jq .ValidatorInfo
```
Node Info
```
evmosd status 2>&1 | jq .NodeInfo
```

## Delegation, Etc
To delegate to your validator run this command :
 Note : Change <ammount> to your like , for example : 100000000000000000000apoint is 100point
```
evmosd tx staking delegate $(evmosd tendermint show-address) <ammount>apoint --chain-id=point_10721-1 --from=$validatorkey --gas=400000 --gas-prices=0.025apoint 
```

## Validator Management
Unjail Validator (MAKE SURE YOU ARE SYNCED WITH THE LASTEST NODE!!)
```
evmosd tx slashing unjail --from=validatorkey --chain-id=point_10721-1 --gas-prices=0.025apoint
```
Check if your validator is active: (if the output is non-empty, you are a validator)
```
evmosd query tendermint-validator-set | grep "$(evmosd tendermint show-address)"
```
See the slashing status: (if Jailed until year 1970 means you are not jailed!)
``` 
evmosd query slashing signing-info $(evmosd tendermint show-validator) 
```

## Delete Node Permanently (Backup your Private key first if you wanna migrate !!)
```
sudo systemctl stop evmosd
sudo systemctl disable evmosd
sudo rm /etc/systemd/system/evmos* -rf
sudo rm $(which evmosd) -rf
sudo rm $HOME/.evmosd -rf
sudo rm $HOME/point-chain -rf
```

  
