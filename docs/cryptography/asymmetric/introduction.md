---
title: Asymmetric Cryptography in Moner
---
# Asymmetric Cryptography in Monero

!!! note
    Author is nowhere close to being a cryptographer. Be sceptical on accuracy.

Before we get to Monero specific stuff, a little bit of context. We are talking asymmetric cryptography here.
The "asymmetric" simply means the are two keys:

* the private key (used primarily for signing data and for decrypting data)
* the public key (used primarily for signature verification and encrypting data)

This is in contrast to symmetric cryptography which uses a single key. This key is a secret shared among the parties.

Historically, asymmetric cryptography was based on the problem of factorization of a very large integers
back into prime numbers (which is practically impossible for large enough integers).

Recently, asymmetric cryptography is based on a mathematical notion of elliptic curves.
Edwards25519 is a specific, well researched and standardized elliptic curve used in Monero.
