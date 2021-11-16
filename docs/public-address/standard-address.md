---
title: Standard Address
---
# Address

Monero public address is what you publish to get paid.

An address can be generated offline and for free. It boils down to generating a large random number representing your private spending key.

Publishing your Monero address does __not__ endanger your privacy. That's because in Monero transactions go to stealth addresses which are decoupled from your public address.

There are a few **types of public addresses** in Monero:

* Standard address - basic type of an address, also referred to as raw address
* Subaddress - what you should be using by default
* Integrated address - relevant for exchanges, merchants, and other businesses accepting Monero in a fully automated way

## Standard address

Historically, raw address was the only available option. For that reason it is the most widely adopted and supported address type.

Its strength is simplicity. However, these days users should prefer receiving to subaddresses instead.

Technically, raw address is also a basis for creating subaddresses and integrated addresses.

Raw address is **still useful for**:

* accepting block reward in a solo-mining scenario as other addresses are not supported
* accepting from senders who batch payouts (like mining pools); in this scenario the sender is paying multiple parties using a single transaction; such transaction has multiple outputs; subaddresses do not work in this scenario
* accepting from senders who use legacy wallets (can't send to subaddress)

Monero raw address is composed of two public keys:

* public spend key
* public view key

It also contains a checksum and a "network byte" which actually identifies both the network and the address type.

## Data structure

Index       | Size in bytes    | Description
------------|------------------|-------------------------------------------------------------
0           | 1                | identifies the network and address type; [18](https://github.com/monero-project/monero/blob/793bc973746a10883adb2f89827e223f562b9651/src/cryptonote_config.h#L149) - main chain; [53](https://github.com/monero-project/monero/blob/793bc973746a10883adb2f89827e223f562b9651/src/cryptonote_config.h#L161) - test chain
1           | 32               | public spend key
33          | 32               | public view key
65          | 4                | checksum ([Keccak-f[1600] hash](https://github.com/monero-project/monero/blob/8f1f43163a221153403a46902d026e3b72f1b3e3/src/common/base58.cpp#L261) of the previous 65 bytes, trimmed to first [4](https://github.com/monero-project/monero/blob/8f1f43163a221153403a46902d026e3b72f1b3e3/src/common/base58.cpp#L53) bytes)

It totals to 69 bytes. The bytes are then encoded ([src](https://github.com/monero-project/monero/blob/8f1f43163a221153403a46902d026e3b72f1b3e3/src/common/base58.cpp#L240)) in [Monero specific Base58](/cryptography/base58) format, resulting in a 95 chars long string. Example standard address:

`4AdUndXHHZ6cfufTMvppY6JwXNouMBzSkbLYfpAV5Usx3skxNgYeYTRj5UzqtReoS44qo9mtmXCqY45DJ852K5Jv2684Rge`

See the [source code](https://github.com/monero-project/monero/blob/f7b9f44c1b0d53170fd7f53d37fc67648f3247a2/src/cryptonote_basic/cryptonote_basic_impl.cpp#L159).

## Generating

### Generating the keys

As mentioned above, a Monero address encodes two elliptic curve public keys so the first step is to generate two elliptic curve private keys from which the public keys may be derived.

Each private key is represented by a scalar between 1 and 2^252 + 27742317777372353535851937790883648492 inclusive. (Note: 2^252 + 27742317777372353535851937790883648492 + 1 is a prime order of the elliptic curve basepoint, `l`).

To generate a new private key, the convention is to generate a random 256 bit numbers and reduce it modulo 2^252 + 27742317777372353535851937790883648493.

To get the public keys to be encoded in the address, the ed25519 basepoint `G` is multiplied by each key scalar.

### Checksum

A checksum of the network byte and public keys is included in the address to help wallet software verify that a typo hasn't been made when users enter the address.

To get this checksum, the network byte as well as the public spend and view keys are concatenated together and Keccak-256 hashed. The first four bytes of this hash are the checksum.

When wallet software parses a Monero address it should decode the address and recompute the checksum and verify that it matches the checksum included in the address.

### Base58 Encoding

In an effort to further prevent errors from typos Monero addresses are encoded using a special character dictionary.

Characters used in Monero base58: `123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz`. Notice how characters that look similar such as `O` and `0` are missing.

[More about Monero base58](https://monerodocs.org/cryptography/base58/).

### Deteministic Wallets

There is no way to regulate how people choose their address keys, it is simply highly suggested that they are chosen at random. The original CryptoNote implementation generated each keypair randomly, independently of each other. Addresses generated this way are called non-deterministic, they have fallen out of popularity.

As of present, the convention is to derive an address's view key deterministically from the spend key which allows for human language encoded seed mnemonics.

Deterministic addresses derive the private view key from the private spend key by hashing it (conventionally Keccak-256) and reducing it modulo `l`.

## Reference

* [StackExchenge answer](https://monero.stackexchange.com/questions/980/what-are-the-public-viewkeys-and-spendkeys)
* [https://xmr.llcoins.net/addresstests.html](https://xmr.llcoins.net/addresstests.html)
* [src/cryptonote_basic/account.cpp account_base::generate](https://github.com/monero-project/monero/blob/dcba757dd283a3396120f0df90fe746e3ec02292/src/cryptonote_basic/account.cpp#L155)
