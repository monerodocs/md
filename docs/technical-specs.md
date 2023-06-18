---
title: Monero Technical Specification
---
# Monero Technical Specs

## Live

* Monero blockchain is live since 18 April 2014

## No premine, no instamine, no ICO, no token

* Monero had no premine or instamine
* Monero did not sell any token
* Monero had no presale of any kind

## Proof of Work

* CryptoNight
    * v0 since block height 0
    * v1 since block height 1546000 (forked on 2018-04-06)
    * v2 since block height 1685555 (forked on 2018-10-18)
    * v3 since block height 1788000 (forked on 2019-03-09); "CryptonightR"
* RandomX
    * v0 since block height 1978433 (forked on 2019-11-30)

## Difficulty retarget

* every block
* based on the last 720 blocks (24h), excluding 20% of the timestamp outliers

## Block time

* 2 minutes
* may change in the future as long as emission curve is preserved

## Block reward

* smoothly decreasing and subject to penalties for blocks greater than median size of the last 100 blocks (M100)
* 0.6 XMR as of June 2023; for the current reward check the coinbase transaction of the [latest block](https://xmrchain.net/)

## Block size

* dynamic
* maximum of two times the median size of the last 100 blocks (2 * M100)
* ~50KB as of June 2020; check [the latest block size](https://bitinfocharts.com/comparison/monero-size.html#3m)

## Current blockchain size

* Pruned node: ~ 57GB as of Jan 2023
* Complete chain without any pruning: ~138GB as of Jan 2023

## Emission curve

### Main emission

* first, the main emission is about to produce ~18.132 million coins by the end of May 2022
* as of June 2023 the emission is about 3 XMR per 10 minutes
* see [charts and details](https://www.reddit.com/r/Monero/comments/512kwh/useful_for_learning_about_monero_coin_emission/)

### Tail emission

* the tail emission kicked in after main emission is done
* it will produce 0.6 XMR per 2-minute block
* this translates to <1% inflation decreasing over time

## Max supply

* ~18.293 million XMR + 0.6 XMR per 2 minutes
* technically infinite but practically deflationary if accounted for lost coins

## Divisibility

* Monero is divisible up to 12 digits
* The smallest unit is called piconero and equals 1e-12 XMR, or 0.000000000001 XMR

## Sender privacy

* ring signatures
    * the ring size is 16 (15 decoys)
* assurance: probabilistic / plausible deniability

## Recipient privacy

* stealth addresses
* assurance: strong

## Amount privacy

* ring confidential transactions
* assurance: strong

## IP address privacy

For the full node (`monerod`):

* dandelion++
* assurance: won't protect against ISP/VPN provider, won't protect against the very first remote node in Dandellion++ protocol
* for the full protection user must manually wrap `monerod` with Tor

For the wallet (`monero-wallet-gui` or `monero-wallet-cli`):

* typically wallet runs on the same machine as full node so there is no risk
* if wallet connects to remote full node, there is no IP protection by default
    * user must manually wrap wallet with Tor or I2P
