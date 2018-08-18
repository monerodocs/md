# Interacting with Monero

You can interact with Monero via desktop GUI, commandline interface, and programming API.

On top of that, Monero nodes interact with each other in a peer-to-peer network. 

## Installation directory overview

[Download Monero](https://getmonero.org/downloads). Once unpacked you will see several executable files.

Monero project nicely decouples node logic `monerod` from wallet logic `monero-wallet-*`.
Wallet logic is offered through three independent user interfaces - the GUI, the CLI, and the HTTP API.

![Monero Directory](/images/monero-dir.png "Monero Unpacked")

| Executable                 | Description  
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monerod`                  | The full node daemon. Does not require a wallet. 
| `monero-wallet-gui`        | Wallet logic and __graphical__ user interface. <br />Requires `monerod` running.
| `monero-wallet-cli`        | Wallet logic and __commandline__ user interface. <br />Requires `monerod` running.
| `monero-wallet-rpc`        | Wallet logic and __HTTP API__ (JSON-RPC protocol). <br />Requires `monerod` running.
| `monero-blockchain-export` | Tool to export blockchain to `blockchain.raw` file.
| `monero-blockchain-import` | Tool to import [blockchain.raw](https://downloads.getmonero.org/blockchain.raw) - ideally your own trusted copy.
| `monero-gen-trusted-multisig` | Tool to generate a set of multisig wallets. <br />See chapter on [multisignatures](/multisignature).
| `monero-blockchain-blackball` | Advanced tool to mitigate privacy issues related to Monero forks. <br />See the [commit](https://github.com/monero-project/monero/commit/df6fad4c627b99a5c3e2b91b69a0a1cc77c4be14#diff-0410fba131d9a7024ed4dcf9fb4a4e07) and [pull request](https://github.com/monero-project/monero/pull/3322).
| `monero-blockchain-usage` | Advanced tool to mitigate privacy issues related to Monero forks. <br />See the [commit](https://github.com/monero-project/monero/commit/0590f62ab64cf023d397b995072035986931a6b4) and the [pull request](https://github.com/monero-project/monero/pull/3322).

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
