---
title: Private Keys in Monero
---
# Private Keys in Monero

!!! note
    Author is nowhere close to being a cryptographer. Be sceptical on accuracy.

In Monero, the root private key is generated [randomly](/cryptography/prng). Other private keys are derived deterministically from the root private key.

Private key must be kept secret.

Private key is a **large integer** impossible to guess, like:
`108555083659983933209597798445644913612440610624038028786991485007418559037440`

Private key is 256 bits long. 

Private key is a **scalar**, meaning it is a single value.

In equations scalars are represented by **lowercase letters**. 

## Relation to Ed25519

Being simply a random integer, private key is not specific to any particular asymmetric cryptography scheme.

In context of Monero EC cryptography the private key is a number the base point `G` is multiplied by.
The result of the multiplication is the public key `P` (another point on the curve).
Multiplication of a point by a number has a very special definition in EC cryptography.
See this [this guide](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/) for details.

### Key strength

Before deriving the public key, private key is subject to modulo `l`,
where `l` is the maximum scalar allowed by the [edwards25519 curve](/cryptography/asymmetric/edwards25519).

The `l` is on the order of 2^252, so the effective key strength is technically 252 bits, not 256 bits.
This is standard for EC cryptography and is more of a cosmetic nuance than any concern.

## Encoding

In user-facing contexts, the private key integer is:
 
1. Taken modulo `l` to avoid malleability
2. Put as array of 32 bytes in a [little-endian](https://en.wikipedia.org/wiki/Endianness#Little) direction (the first byte is the least significant)
3. Converted to hexadecimal form, like: `b3588a87056fb21dc4d052d59e83b54293882e646b543c29478e4cf45c28a402`

## Private spend key

Private spend key is used to spend moneros.
 
More specifically, it is used to build one-time private keys which allow to spend related outputs.

## Private view key

Private view key is used to recognize your incoming transactions on the otherwise opaque blockchain.

## One-time private keys

One-time private key like construct is used in [stealth addresses](https://monero.stackexchange.com/questions/1409/constructing-a-stealth-monero-address).
