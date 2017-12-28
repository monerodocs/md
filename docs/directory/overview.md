# Monero directory overview

This considers the released version of Monero software. Once unpacked, directory structure looks like:

![Monero Directory](/images/monero-dir.png "Logo Title Text 1")

## Separation of node and wallet

Monero project nicely decouples node logic `monerod` from wallet logic `monero-wallet-*`.
Wallet logic is offered through three independent user interfaces - the GUI, the CLI, and the HTTP API.

| Executable                 | Description  
| -------------------------- |:-----------------------------------------------------------------------------------------------------------------------------------
| `monerod`                  | The full node daemon. Does not require a wallet. 
| `monero-wallet-gui`        | Wallet logic and graphical user interface. Requires `monerod` running.
| `monero-wallet-cli`        | Wallet logic and commandline user interface. Requires `monerod` running.
| `monero-wallet-rpc`        | Wallet logic and HTTP API (JSON-RPC protocol). Requires `monerod` running.
| `monero-blockchain-export` | Tool to export synchronized blockchain to `blockchain.raw` file.
| `monero-blockchain-import` | Tool to import [blockchain.raw](https://downloads.getmonero.org/blockchain.raw) - ideally your own trusted copy.
