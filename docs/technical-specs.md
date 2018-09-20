---
title: Monero Technical Specification | Monero Documentation
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
    * (planned) v2 since block height 1685555 (will fork on ~2018-10-18)
* Changes every ~6 months to discourage ASIC-s

## Difficulty retarget

* every block
* based on the last 720 blocks (24h), excluding 20% of the timestamp outliers

## Block time

* 2 minutes
* may change in the future as long as emission curve is preserved

## Block reward

* smoothly decreasing and subject to penalties for blocks greater then median size of the last 100 blocks (M100)
* ~3.8 XMR as of Sep 2018; for the current reward check the coinbase transaction of the [latest block](https://moneroblocks.info/)

## Block size

* dynamic
* maximum of two times the median size of the last 100 blocks (2 * M100)
* ~100KB as of Aug 2018; check [the latest block size](https://bitinfocharts.com/comparison/monero-size.html#3m)

## Emission curve

### Main emission

* first, the main emission is about to produce ~18.132 million coins by the end of May 2022
* as of Aug 2018 the emission is about 20 XMR per 10 minutes
* see [charts and details](https://www.reddit.com/r/Monero/comments/512kwh/useful_for_learning_about_monero_coin_emission/)

### Tail emission

* the tail emission kicks in once main emission is done
* it will produce 0.6 XMR per 2-minute block
* this translates to <1% inflation decreasing over time

## Max supply

* ~18.132 million XMR + 0.6 XMR per 2 minutes
* technically infinite but practicaly deflationary if accounted for lost coins

## Sender privacy

* ring signatures
* assurance: probabilistic / plausible deniability

## Recipient privacy

* stealth addresses
* assurance: strong

## Amount privacy

* ring confidential transactions
* assurance: strong

## IP address privacy

* there is an ongoing effort to integrate I2P into Monero - the [Kovri](https://github.com/monero-project/kovri) project 
* assurance: none at the moment - please use TOR or I2P
