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

