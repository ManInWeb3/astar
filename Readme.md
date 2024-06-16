#

sudo -s
apt update;apt dist-upgrade -y; apt install tmux mc nano jq wireguard python3-pip

pip install --user -U wg-meshconf


wg-meshconf addpeer relay --address 10.1.0.1/16 --endpoint 152.42.168.231
wg-meshconf addpeer para --address 10.2.0.1/16 --endpoint 152.42.224.160
wg-meshconf addpeer rpc1 --address 10.3.0.1/16 --endpoint 167.172.67.163
wg-meshconf addpeer rpc2 --address 10.4.0.1/16 --endpoint 188.166.216.212
wg-meshconf addpeer lb --address 10.5.0.1/16 --endpoint 188.166.219.135

sudo apt update
sudo apt dist-upgrade -y
sudo apt install -y tmux mc nano jq wireguard
sudo mv /home/astar-admin/*.conf /etc/wireguard/wg0.conf
sudo systemctl enable --now wg-quick@wg0
systemctl status wg-quick@wg0.service

## Download bin

curl -Lo /usr/local/bin/polkadot https://github.com/paritytech/polkadot-sdk/releases/download/polkadot-v1.12.0/polkadot
curl -Lo /usr/local/bin/polkadot-execute-worker https://github.com/paritytech/polkadot-sdk/releases/download/polkadot-v1.12.0/polkadot-execute-worker
curl -Lo /usr/local/bin/polkadot-prepare-worker https://github.com/paritytech/polkadot-sdk/releases/download/polkadot-v1.12.0/polkadot-prepare-worker
https://github.com/AstarNetwork/Astar/releases/download/v5.39.1/astar-collator-v5.39.1-ubuntu-x86_64.tar.gz

useradd --no-create-home --shell /usr/sbin/nologin astar

# Run relay chain
## 1. Build chain specs

`polkadot build-spec --chain rococo-local> rococo-local.json`

## 2. Generate p2p key for the validators
polkadot key generate-node-key --base-path /data/alice
polkadot key generate-node-key --base-path /data/bob

## 3. Set alice and bob p2p identity as boot node
Set `bootNodes` in the chain spec file to the correct alice multiaddress

## Generate raw chain-spec
`polkadot build-spec --chain rococo-local.json --raw > rococo-local-raw.json`

## 4. Run alice and bob validators
[Service files](validators/etc_systemd_system)


## Port Forwarding for relay chain
ssh -L 9944:127.0.0.1:9946 -i ~/.ssh/id_ed25519 astar-admin@152.42.168.231 -p17117

Can see the chain in explorer
`https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/explorer`

# Run parachain
## Reserve a unique parachain id (2000)
https://docs.substrate.io/tutorials/build-a-parachain/connect-a-local-parachain/
follow "Reserve a unique identifier"

## Build parachain spec
`astar-collator build-spec --chain astar-dev --disable-default-bootnode > astar-dev.json`

## Correct relaychain id in the spec json
Correct
```
  "para_id": 2000,
...
  "parachainInfo": {
    "parachainId": 2000
  },
```

## Build raw parachain spec
`astar-collator build-spec --chain ./astar-dev.json --disable-default-bootnode --raw > ./astar-dev-raw.json`

## Export the WebAssembly runtime for the parachain.
`astar-collator export-genesis-wasm --chain astar-dev-raw.json astar-dev-genesis-wasm`

## Generate a parachain genesis state.
`astar-collator export-genesis-state --chain astar-dev-raw.json astar-dev-genesis-state`

## Run collators
[see service files](collators/etc_systemd_system)

## Register with the local relay chain

### paraSudoWrapper.sudoScheduleParaInitialize(id, genesis)
#### Check sudo.Sudid and paras.PvfCheckStarted

### slots.forceLease
#### Check sudo.Sudid and slots.Leased


## Set session keys
https://docs.astar.network/docs/build/nodes/collator/spinup_collator/#session-keys


# Questions
1. Firewal: should I setup a firewall with ufw, due to dont have access to the infra level firewall (cloud firewall/security groups/etc.)

2. Do you have any smoke/validation test, to be sure that it meets requirements???

