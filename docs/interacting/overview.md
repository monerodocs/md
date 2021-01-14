---
title: Interacting with Monero
---
# Interacting with Monero

You can interact with Monero via desktop GUI, commandline interface, and programming API.

On top of that, Monero nodes interact with each other in a peer-to-peer network.

## Installation directory overview

Once unpacked you will see several executable files. You will also find a nice PDF guide for the GUI wallet.

Monero project nicely decouples network node logic from wallet logic.
Wallet logic is offered through three independent user interfaces - the GUI, the CLI, and the HTTP API.

```
# cd monero-gui-v0.17.1.9

# ---- guide to Monero GUI ----

monero-gui-wallet-guide.pdf

# ---- main executable files -----------

monerod
monero-wallet-gui

# ---- extra executable files -----------

extras/monero-wallet-cli
extras/monero-wallet-rpc

extras/monero-blockchain-prune
extras/monero-gen-trusted-multisig
extras/monero-gen-ssl-cert

extras/monero-blockchain-export
extras/monero-blockchain-import

# ---- don't bother with these ----------

extras/monero-blockchain-stats
extras/monero-blockchain-mark-spent-outputs
extras/monero-blockchain-prune-known-spent-data
extras/monero-blockchain-usage
extras/monero-blockchain-ancestry
extras/monero-blockchain-depth
```

## Executables

| Executable                 | Description
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monerod`                  | The full node daemon. Does not require a wallet. <br />[Documentation](/interacting/monerod-reference/).
| `monero-wallet-gui`        | Wallet logic and __graphical__ user interface. <br />Requires `monerod` running.
| `monero-wallet-cli`        | Wallet logic and __commandline__ user interface. <br />Requires `monerod` running.
| `monero-wallet-rpc`        | Wallet logic and __HTTP API__ (JSON-RPC protocol). <br />Requires `monerod` running.
| `monero-blockchain-prune`  | Prune existing local blockchain. This saves 2/3 of disk space (down to 31GB as of Jan 2021). This is preferable over `monerod --prune-blockchain` which only logically releases space inside the file while the file remains large. The `monero-blockchain-prune` creates a shrinked copy of the blockchain file. See [tutorial1](https://monero.stackexchange.com/questions/11454/how-do-i-utilize-blockchain-pruning-in-the-gui-monero-wallet-gui), [tutorial2](https://www.publish0x.com/solareclipse/howto-prune-shrink-the-database-of-the-monero-blockchain-on-xpgwjx).
| `monero-gen-ssl-cert`      | Generate 4096 bit RSA private key and self signed TLS certificate for use with `monerod` RPC interface. Note, Monero daemon automatically generates TLS certificate on each restart. Manual generation with this tool is only useful if you want to pin TLS certificate fingerprint in your monero wallet. See the [pull request](https://github.com/monero-project/monero/pull/5495).
| `monero-gen-trusted-multisig`          | Tool to generate a set of multisig wallets. <br />See chapter on [multisignatures](/multisignature).
| `monero-blockchain-export` | Tool to export blockchain to `blockchain.raw` file.
| `monero-blockchain-import` | Tool to import [blockchain.raw](https://downloads.getmonero.org/blockchain.raw) - ideally your own trusted copy.

## Executables - legacy

You most likely should not bother with these legacy or very specialized tools.

| Executable                 | Description
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monero-blockchain-stats`              | Generate stats like tx/day, blocks/day, bytes/day based on your local blockchain.
| `monero-blockchain-mark-spent-outputs` | Advanced tool to mitigate potential privacy issues related to Monero forks. You normally shouldn't be concerned with that.<br />See the [commit](https://github.com/monero-project/monero/commit/df6fad4c627b99a5c3e2b91b69a0a1cc77c4be14#diff-0410fba131d9a7024ed4dcf9fb4a4e07) and [pull request](https://github.com/monero-project/monero/pull/3322).
| `monero-blockchain-prune-known-spent-data`  | Previous limited pruning tool to prune select "known spent" transaction outputs (from the before RCT era). Nowadays prefer `monero-blockchain-prune`. This only saves ~200 MB. See the [commit](https://github.com/monero-project/monero/commit/d855f9bb92dbfab707a0e37505906366de818a14).
| `monero-blockchain-usage`              | Advanced tool to mitigate potential privacy issues related to Monero forks. You normally shouldn't be concerned with that.<br />See the [commit](https://github.com/monero-project/monero/commit/0590f62ab64cf023d397b995072035986931a6b4) and the [pull request](https://github.com/monero-project/monero/pull/3322).
| `monero-blockchain-ancestry`           | Advanced research tool to learn ancestors of a transaction, block or chain. Irrelevant for normal users. See this [pull request](https://github.com/monero-project/monero/pull/4147/files).
| `monero-blockchain-depth`              | Advanced research tool to learn depth of a transaction, block or chain. Irrelevant for normal users. See this [commit](https://github.com/monero-project/monero/commit/289880d82d3cb206a2cf4ae67d2deacdab43d4f4#diff-34abcc1a0c100efb273bf36fb95ebfa0).


## Interacting

There are quite a few ways you can interact with Monero software.
Perhaps the most surprising for newcomers is that `monerod` daemon accepts interactive keyboard commands while it is running.

Also, please note that HTTP API is split across `monerod` and `monero-wallet-rpc`. You need to run and call both daemons to explore the full API.
This follows the node-logic vs wallet-logic split mentioned earlier.

All wallet implementations depend on the `monerod` running.

| Executable                 | p2p network         | node commands via keyboard | node HTTP API | wallet commands via keyboard | wallet HTTP API | wallet via GUI
| -------------------------- | ------------------- | -------------------------- | ------------- | ---------------------------- | --------------- | --------------
| `monerod`                  | ✔                   | ✔                          | ✔             |                              |                 |
| `monero-wallet-cli`        |                     |                            |               | ✔                            |                 |
| `monero-wallet-rpc`        |                     |                            |               |                              | ✔               |
| `monero-wallet-gui`        |                     |                            |               |                              |                 | ✔

## Data directory

This is where the blockchain, log files, and p2p network memory are stored.

By default data directory is at:

* `$HOME/.bitmonero/` on Linux and macOS
* `C:\ProgramData\bitmonero\` on Windows

Please mind:

* data directory is hidden as per OS convention
* the `bitmonero` directory name is historical artefact from before Monero forked away from Bitmonero, about 2000 years Before Christ

Data directory contains:

* `lmdb/` - the blockchain database directory
* `p2pstate.bin` - saved memory of discovered and rated peers
* `bitmonero.log` - log file

It can also contain subdirectories for stagenet and testnet, mirroring the same structure:

* `stagenet/` - data directory for Stagenet
* `testnet/` - data directory for Testnet
