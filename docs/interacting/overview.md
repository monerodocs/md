# Interacting with Monero

You can interact with Monero via desktop GUI, commandline interface, and programming API.

On top of that, Monero nodes interact with each other in a peer-to-peer network. 

## Installation directory overview

This considers the released version of Monero software. Once unpacked, you will notice six executable files:

![Monero Directory](/images/monero-dir.png "Logo Title Text 1")

## Separation of node and wallet

Monero project nicely decouples node logic `monerod` from wallet logic `monero-wallet-*`.
Wallet logic is offered through three independent user interfaces - the GUI, the CLI, and the HTTP API.

| Executable                 | Description  
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monerod`                  | The full node daemon. Does not require a wallet. 
| `monero-wallet-gui`        | Wallet logic and __graphical__ user interface. Requires `monerod` running.
| `monero-wallet-cli`        | Wallet logic and __commandline__ user interface. Requires `monerod` running.
| `monero-wallet-rpc`        | Wallet logic and __HTTP API__ (JSON-RPC protocol). Requires `monerod` running.
| `monero-blockchain-export` | Tool to export blockchain to `blockchain.raw` file.
| `monero-blockchain-import` | Tool to import [blockchain.raw](https://downloads.getmonero.org/blockchain.raw) - ideally your own trusted copy.

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
