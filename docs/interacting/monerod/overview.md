# `monerod`

## Connects you to Monero network

The Monero daemon `monerod` keeps your computer synced up with the Monero network.

It downloads and validates the blockchain from the p2p network.

## Not aware of your private keys

`monerod` is entirely decoupled from your wallet.

`monerod` does not access your private keys - it is not aware of your transactions and balance.

This allows you to run `monerod` on a separate computer or in the cloud.

In fact, you can connect to a remote `monerod` instance provided by a semi-trusted 3rd party. Such 3rd party will not be able to steal your funds. This is very handy for learning and experimentation.

However, there are privacy and reliability implications to using remote, untrusted node. For any real business **you should be running your own full node**.

## Data directory

This is where the blockchain, log files, and p2p network memory are stored.

By default data directory is at:

* `$HOME/.bitmonero/` on Linux
* `$HOME/Library/Application\ Support/` on macOS
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


## Running

First, go to directory where you unpacked Monero.

The **[stagenet](/networks)** is what your should be using for learning and experimentation.

Start: `./monerod --stagenet --detach`

Watch:
`tail -f ~/.bitmonero/stagenet/bitmonero.log`

Stop:
`./monerod --stagenet exit`

The **[mainnnet](/networks)** is when you want to deal with the real XMR.

Start: `./monerod --detach`

Watch:
`tail -f ~/.bitmonero/bitmonero.log`

Stop:
`./monerod exit`
