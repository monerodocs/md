# Monero Technical Specs

## No premine, no instamine, no token

* Monero had no premine or instamine
* Monero did not sell any token
* Monero had no presale of any kind

## Proof of Work

* CryptoNight
* may change in the future

## Difficulty retarget

* every block
* based on the last 720 blocks, excluding 20% of the timestamp outliers

## Block time

* 2 minutes
* may change in the future as long as emission curve is preserved

## Block reward

* ~6 XMR as of Dec 2017, see the [latest block](https://moneroblocks.info/) coinbase transaction amount for current reward
* smoothly decreasing and subject to penalties for blocks greater then median size of the last 100 blocks (M100)

## Block size

* dynamic, maximum of two times median size of the last 100 blocks (2 * M100)

## Emission curve

**Main curve**

First, the main emission is about to produce ~18.132 million coins by the end of May 2022.

As of Dec 2017 the emission is about 30 XMR per 10 minutes.

See [charts and details](https://www.reddit.com/r/Monero/comments/512kwh/useful_for_learning_about_monero_coin_emission/).

**Tail curve**

The tail emission kicks in once main emission is done.

It will produce 0.6 XMR per 2-minute block.

This translates to <1% inflation decreasing over time.

## Max supply

* infinite

## Sender privacy

* Ring signatures

## Recipient privacy

* Stealth addresses

## Amount obfuscation

* Ring confidential transactions
