---
title: Multisignature | Monero Documentation
---
# Multisignature

In cryptocurrencies, multisig feature allows to sign transaction with more than one private key. Funds protected with multisig can only be spent by signing with M-of-N keys.

Example use cases:

* shared account (1-of-2; both husband and wife individually have full access to their funds)
* consensus account (2-of-2; both husband and wife must agree to spend their funds)
* threshold account (2-of-3; an escrow service is involved as an independent 3rd party, to co-sign with either the seller, or with the buyer, if seller and buyer do not agree)
* secure account (2-of-3; a single owner controlls all 3 keys but secures them via a different means to diversify risks)
* arbitrary threshold account (M-of-N; some cryptocurrencies provide full flexibility on the number of signers)

## Monero multisignature

Monero doesn't directly implement multisignatures (at least not in a classical sense). Monero emulates the feature by secret splitting.

Transactions are still signed with a single spend key. The spend key is a sum of all N private keys. The rationale for such design is to decouple multisig from ring signatures.

Let's consider the 2-of-3 scheme. We have 3 participants. Each participant is granted exactly 2 private keys in a way that pairs do not repeat between participants. This way any 2 participants together have all 3 private keys required to create the private spend key.

Multi-signing is a wallet-level feature. There is no way to learn from the blockchain which transactions were created using multiple signatures.

It is also worth noting in Monero there is no multisig addresses as such. [Address structure](Public-Address) does not care how the underlying private spend key got created.

After multisig wallet setup every participant ends up knowing the public address and private view key. This is necessary for participants to recognize and decipher transactions they are supposed to co-sign.

## Multisig wallet setup

Let's consider a 2-of-3 scheme as it generalizes well. There will be three CLI wallet commands involved:

### 1. prepare_multisig

Every participant independently generates **initialization data**. This is **not** an address.

Every participant sends his initialization data manually to all other participants over secure channel.

### 2. make_multisig

Every participant applies initialization data from other participants. This results in a **second round of initialization data**. This is still **not** an address.

Every participants sends his second round of init data to all other participants over secure channel.

### 3. finalize_multisig

Every participant finalizes wallet creation by applying the second round of init data from all other participants. This finally results in a wallet **public address** and **private view key** to be known for all participants. 

Please note actions are symmetric for all participants. Even though we considered a 2-of-3 scheme, every participant cooperates with everyone else. The secret splitting is performed internally by the wallet.

Secure sharing of initialization data between participants is manual. The wallet itself does not provide any secure communication channel. This is out of scope.

## Receiving funds

Address built by multisig setup is like any other address.

You can generate integrated addresses and subaddresses based on it.

All participants are able to see incoming funds as they share the private view key.

With a CLI, use the following commands to see incoming payments:

    address
    refresh
    show_transfers

## Spending funds

TODO

## Reference

* [https://monero.stackexchange.com/questions/5646/how-to-use-monero-multisignature-wallets-2-2-2-3](https://monero.stackexchange.com/questions/5646/how-to-use-monero-multisignature-wallets-2-2-2-3)
