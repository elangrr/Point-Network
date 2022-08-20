</p>
<p style="font-size:14px" align="right">
<a href="https://discord.gg/NtFMGGRn" target="_blank">Join Point Network Discord<img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
</p>


<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/38981255/185550018-bf5220fa-7858-4353-905c-9bbd5b256c30.jpg">
</p>

# Migrate your validator to another machine

### 1. Run a new full node on a new machine
To setup node you can follow guide [point node setup for testnet](https://github.com/elangrr/Point-Network/blob/main/README.md))

### 2. Confirm that you have the recovery seed phrase information for the active key running on the old machine

#### To backup your key
```
evmosd keys unsafe-export-eth-key validatorkey --keyring-backend file
```
> _This prints the private key

#### To get list of keys
```
evmosd keys list
```

### 3. Recover the active key of the old machine on the new machine

#### This can be done with the mnemonics
```
evmosd keys add $WALLET --recover
```

### 4. Wait for the new node on the new machine to finish catching-up

#### To check synchronization status
```
evmosd status 2>&1 | jq .SyncInfo
```
> _`catching_up` should be equal to `false`_

### 5. After the new node has caught-up, stop the validator node

> _To prevent double signing, you should stop the validator node before stopping the new full node to ensure the new node is at a greater block height than the validator node_
> _If the new node is behind the old validator node, then you may double-sign blocks_

#### Stop and disable service on OLD MACHINE
```
sudo systemctl stop evmosd
sudo systemctl disable evmosd
```
> _The validator should start missing blocks at this point_

### 6. Stop service on NEW MACHINE
```
sudo systemctl stop evmosd
```

### 7. Move the validator's private key from the old machine to the new machine
#### Private key is located in: `~/.evmosd/config/priv_validator_key.json`

> _After being copied, the key `priv_validator_key.json` should then be removed from the old node's config directory to prevent double-signing if the node were to start back up_
```
sudo mv ~/.evmosd/config/priv_validator_key.json ~/.evmosd/bak_priv_validator_key.json
```

### 8. Start service on a new validator node
```
sudo systemctl start evmosd
```
> _The new node should start signing blocks once caught-up_

### 9. Make sure your validator is not jailed
#### To unjail your validator
```
evmosd tx slashing unjail --chain-id $POINT_CHAIN_ID --from $WALLET --gas=auto -y
```

or use this command 
```
evmosd tx slashing unjail --from=wallet --chain-id=point_10721-1
```

### 10. After you ensure your validator is producing blocks and is healthy you can shut down old validator server


Reference : [KJNodes](https://github.com/kj89/testnet_manuals/)
