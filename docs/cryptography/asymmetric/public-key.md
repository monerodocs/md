---
title: Public Keys in Monero
---
# Public Keys in Monero

!!! note
    Author is nowhere close to being a cryptographer. Be sceptical on accuracy.

Public key is deterministically derived from private key based on [edwards25519 curve](/cryptography/asymmetric/edwards25519) with a little Monero-specific twist.

Public key is meant to be shared. Assuming correct implementation, it is not practically possible to recover private key from public key. 

Public key is a **point (x,y)** on the elliptic curve.

In equations points are represented by **uppercase letters**.

In user-facing contexts, public key is encoded in a [little-endian](https://en.wikipedia.org/wiki/Endianness#Little) hexadecimal form, like:
`016a941812293cf9a86071060fb090ab38d67945e659968cb8cf30e1bc725683` 

## Deriving public key

Say:

* P is a public key
* x is a private key
* G is a "base point"; this is simply a constant specific to [edwards25519](/cryptography/asymmetric/edwards25519); this point lies on the elliptic curve
 
Then:

    P = xG

The public key is simply the base point (G) multiplied by the private key (x).
Multiplying the point is adding the point to itself a number of times.

However, the addition is **not** a simple vector addition. It has a very specific
definition nicely described in [this article](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/).
What is important is that result of addition is always a point on the curve.
For example, G + G is another point on the curve.

## Use cases

[Monero address](/public-address/standard-address) is composed of public spend key and public view key.
These keys are used to build stealth addresses to receive payments.  
