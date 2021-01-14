---
title: monero-blockchain-import - Reference
---
# `monero-blockchain-import` - Reference

!!! note
    Nowadays, there is little usage for raw blockchain export / import. In the past the p2p blockchain download was much slower. Back than blockchain.raw file was used to speed up download process of bootstrapping a node. 

## Overview

The tool imports bootstrap file `blockchain.raw` to your full node.

This could be useful if you want to decouple download from verification for any reason (like testing performance in isolation).
In this case, you may download the `blockchain.raw` file from [https://downloads.getmonero.org/blockchain.raw](https://downloads.getmonero.org/blockchain.raw).

Ideally though, you should use your own trusted `blockchain.raw` file that you [exported](/interacting/monero-blockchain-export-reference) earlier.

Please note that importing `blockchain.raw` will **not** speed up the process over syncing up from p2p network.
This is because usual bottlenecks are disk IO and verification, not the download.  

The tool works on your local files. It does not require `monerod` running.

## Syntax

`./monero-blockchain-import [options]`

Example:

`./monero-blockchain-import --help`

## Running

Go to directory where you unpacked Monero.

`./monero-blockchain-import --stagenet --output-file=/tmp/blockchain.raw`


## Options

### Help

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--help`            | Enlist available options.

### Pick network

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| (missing)           | By default `monero-blockchain-import` assumes the [mainnet](/infrastructure/networks#mainnet) blockchain.
| `--stagenet`        | Import [stagenet](/infrastructure/networks#stagenet) blockchain.
| `--testnet`         | Import [testnet](/infrastructure/networks#testnet) blockchain.

### Logging

Specifying the log file path is not supported.

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--log-level`       | `0-4` with `0` being minimal logging and `4` being full tracing. Defaults to `0`. These are general presets and do not directly map to severity levels. For example, even with minimal `0`, you may see some most important `INFO` entries. Example: <br />`./monero-blockchain-import --log-level=1`

### Input

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--input-file`      | Full path to raw blockchain file. The default is `$DATA_DIR/export/blockchain.raw`.
| `--block-stop`      | Only import up to this block height. By default import all blocks (value `0`).
| `--pop-blocks`      | Discard specified number of blocks from the tip of the bootstrapped blockchain. By default don't discard anything (value `0`).

### Output

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--data-dir`        | Full path to data directory. This is where the blockchain, log files, and p2p network memory are stored. For defaults and details see [data directory](/interacting/overview/#data-directory).
| `--count-blocks`    | Count blocks in the bootstrap file and exit.
| `--drop-hard-fork`  | Whether to drop hard fork data. Off by default (`0`). 
| `--database`        | The only valid value seems to be `lmdb` (the default).

### Performance

| Option                            | Description
|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--dangerous-unverified-import`   | The safe default is to run verification (value `0`). You can enable `--dangerous-unverified-import` if you are importing from your own and trusted blockchain.raw (which we assume was already verified). The "dangerous" mode will greatly speed up the process.   
| `--batch`                         | Whether to save to disk on an ongoing basis (the default, value `1`) or maybe do everything in RAM and save everything in the end (value `0`). No batching is only effective in combination with no verification (`--dangerous-unverified-import`). See also `--batch-size`.   
| `--batch-size`                    | How often to save to disk expressed in number of blocks. By default save every `5000` blocks (when verifying) or every `20000` blocks (when not verifying). Big batches are faster but require more RAM.
| `--resume`                        | Resume from current height if output database already exists (the default, value `1`). Changing to `--resume=0` doesn't change much &mdash; existing blocks are skipped pretty quickly and the process is resumed anyway. 

## Reference

* [https://github.com/monero-project/monero/tree/master/src/blockchain_utilities](https://github.com/monero-project/monero/tree/master/src/blockchain_utilities)
