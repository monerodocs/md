---
title: monero-wallet-gui - Reference
---
# `monero-wallet-gui` - Reference

## Overview

### Desktop GUI wallet

The "official" desktop wallet for Monero. Available for Linux, macOS and Windows.

Wallet uses your private keys to understand your total balance,
transactions history, and to facilitate creating transactions.

However, wallet does not store the blockchain and does not directly participate in the p2p network.

### Depends on the full node 

Wallet connects to a [full node](/interacting/monerod-reference) to scan the blockchain for your transaction outputs and to send your transactions out to the network.   

The full node can be either local (same computer) or remote.

Normally, you run the full node on the same computer as wallet (or within your home network).

Connection happens over HTTP and uses [this API](https://www.getmonero.org/resources/developer-guides/wallet-rpc.html).

Any transaction leaving the wallet is already blinded by all Monero privacy features.
This means plain text HTTP communication isn't an issue on its own even if you connect to a remote node.

However, connecting to a remote node has other nuanced trade-offs, which is a topic for a separate article. 

### User guide PDF

A nice PDF guide is available in the catalog you unpacked Monero. Make sure to check it out!

The online living version is also available:<br />
[https://github.com/monero-ecosystem/monero-GUI-guide/blob/master/monero-GUI-guide.md](https://github.com/monero-ecosystem/monero-GUI-guide/blob/master/monero-GUI-guide.md)

## Syntax

`./monero-wallet-gui [options]`

Example:

`./monero-wallet-gui --log-file=/dev/null`

## Running

Go to directory where you unpacked Monero.

Run the full node and wait until it syncs up with the network (may take up to a few days):

`./monerod`

In a separate terminal window, run the wallet:

`./monero-wallet-gui`

## Options

There are very few options because everything is set up via a GUI.

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--help`            | Enlists available options.
| `--log-file`        | Full path to the log file. Example (mind file permissions): <br/>`./monerod --log-file=/var/log/monero/mainnet/monerod.log`

## Defaults

The wallet is created in `$HOME/Monero/wallets/`.
You may want to change it to `$HOME/.bitmonero/wallets/` to have all Monero related files in one place.
This is possible on wallet creation wizard in the GUI. 

The log file is created directly in the home directory `$HOME/monero-wallet-gui.log`.
You may want to change with `--log-file=$HOME/.bitmonero/monero-wallet-gui.log` option. 
