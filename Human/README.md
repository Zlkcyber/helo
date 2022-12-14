# peers/
> ADD PEERS

```

peers="3fc2c2e3a4b11d540c736a4ae4c9c247fb05fbae@168.119.186.161:26656,3d1e89341f64df76599748b634cbabbb8ba3d1b2@65.21.170.3:43656,295be5393e99c60763c85987fa3f8045af20d828@95.214.53.178:36656,69822c67487d4907f162fdd6d42549e1df60c82d@65.21.224.248:26656,c2cf4e1d0da9ac1e8be5d5288d0bf8e8052b2d86@65.109.92.148:60856,fa57a5bd809eb234f0135e2e62039b5ea09d3992@65.108.250.241:36656,5c27e54b2b8a597cbbd1c43905d2c18a67637644@142.132.231.118:36656,c7181941789884d6c468bfca31778b10f83a388e@95.217.12.217:26656,c40acba57194521c2d16d59e9dcb2250bb8f2db2@162.55.245.219:36656,981e9829afd1679cd9fafc90edc4ff918057e6fe@217.13.223.167:60556,5e41a64298ca653af5297833c6a47eb1ad1bf367@154.38.161.212:36656,3f13ad6e8795479b051d147a5049bf4bd0a63817@65.108.142.47:22656,aac683209559ca9ea48de4c47f3806483a5ec13f@185.244.180.97:26656,d55876bc04e363bbe68a7fb344dd65632e310f45@138.201.121.185:26668"

sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.humans/config/config.toml

```

> ADD RPC FOR STATESYNC

```

SNAP_RPC=https://rpc-testnet.humans.zone:443

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \

BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \

TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

```

> CHECK LATEST HEIGHT

```

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

```

> TURN ON STATESYNC

```

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \

s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \

s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \

s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \

s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.humans/config/config.toml

```

> RESTART NODE

```

systemctl restart humansd && journalctl -u humansd -f -o cat

```
