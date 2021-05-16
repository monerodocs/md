---
title: Keccak-256 Hash Function
---
# Keccak-256 Hash Function

Monero employs Keccak as a hashing function. In most context specifically Keccak-256 is used,
providing 32-byte hashes.

Keccak is the leading hashing function, designed by non-NSA designers.
Keccak won [NIST competition](https://en.wikipedia.org/wiki/NIST_hash_function_competition) to become the official SHA3.

## Use Cases

Monero does **not** employ Keccak for Proof-of-Work. Instead, Keccak is used for:
   
* random number generator
* block hashing
* transaction hashing
* stealth address private key image (for double spend protection)
* public address checksum
* RingCT
* multisig
* bulletproofs

...and likely a few other things.

## Keccak-256 vs SHA3-256

SHA3-256 is Keccak-256, except that NIST changed the padding.
For this reason, the original Keccak-256 gives a different hash value than NIST SHA3-256.  

Monero uses original Keccak-256.
The NIST standard was only published on August 2015, while Monero went live on 18 April 2014.  

## Reference

* [Keccak source code used in Monero](https://github.com/monero-project/monero/blob/5c2dfe157b48a486eb2b92dcf8789b3b1eb20f60/src/crypto/keccak.c)
* [SHA3 on Wikipedia](https://en.wikipedia.org/wiki/SHA-3)
* [Keccak-256 vs SHA3-256](https://ethereum.stackexchange.com/questions/550/which-cryptographic-hash-function-does-ethereum-use) explained on Ethereum stackexchange
* [Online tool to calculate Keccak-256 and SHA3-256](https://emn178.github.io/online-tools/keccak_256.html)
