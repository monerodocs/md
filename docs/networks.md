---
title: Mainnet, Stagenet, Testnet | Monero Documentation
---
# Networks

Monero offers three separate networks and blockchains: **mainnet**, **stagenet** and **testnet**. Every blockchain has its own genesis block and is entirely seperate from others. Also, corresponding p2p networks are separate.

## Mainnet

Mainnet is the "production" network and blockchain.

Mainnet is the only blockchain where XMR units have value.

Mainnet is what people mean by default.

## Stagenet

Stagenet is what you need to learn Monero safely.

Stagenet is technically equivalent to mainnet, both in terms of features and consensus rules. Similar to mainnet, use the [latest official Monero release](https://getmonero.org/downloads/) to be compatible with stagenet.

To get started:

* Connect your GUI or CLI to stagenet open node **monero-stagenet.exan.tech:38081**
* Get free XMR from [stagenet faucet](http://stagenet.xmr-tw.org:38085/)
* Check the [stagenet block explorer](http://162.210.173.150:8083/)

Stagenet was introduced in March 2018 as part of Monero 0.12.0.0.

## Testnet

As a normal user, use [stagenet](/networks#stagenet) instead. Testnet is for developers.

Testnet is the "experimental" network and blockchain where things get released long before mainnet.

Testnet forks early and often. Most of the time you need to compile Monero from source to be compatible with testnet. Otherwise you will get stuck on the old not-forked version.

To get started:

* Build Monero from [source](https://github.com/monero-project/monero)
* Connect your GUI or CLI to testnet open node **monero-testnet.exan.tech:28081**
* Get free XMR from [testnet faucet](https://dis.gratis/)
* Check the [testnet block explorer](https://testnet.xmrchain.net)

## Private Testnet

You can also create your private version of the testnet.

Private testnet gives more flexibility to arange for specific situations for developers.

Check the [private testnet guide](https://github.com/moneroexamples/private-testnet).

## Why stagenet and testnet coins have no value?

If mainnet and stagenet are technically equivalent then why only mainnet coins have value?

This is simply the convention community embraced. Value only comes from a shared believe that mainnet coins will be accepted by other people in the future.
