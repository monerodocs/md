# Asymmetric cryptography used in Monero

!!! danger
    Article author is nowhere close to being a cryptographer. Be sceptical on accuracy.

Before we get to Monero, a little bit of context. We are talking asymmetric cryptography here.
The "asymmetric" simply means the are two keys:

* the private key (used primarily for signing data and for decrypting data)
* the public key (used primarily for signature verification and encrypting data)

This is in contrast to symmetric cryptography which uses a single (secret) key.

Historically, asymmetric cryptography was based on the problem of factorization of a very large integers
back into prime numbers (which is practically impossible for large enough integers).

Recently, asymmetric cryptography is based on a mathematical notion of elliptic curves.
Ed25519 is a specific, well researched and standardized elliptic curve used in Monero.      

## Private key

Private key is a **large integer**, like:
`115792089237316195423570985008687907853269984665640564039457584007913129639930`

Private key is a **scalar**, meaning it is a single value.

In equations scalars are represented by **lowercase letters**. 

In user-facing contexts, private keys are encoded in little-endian hexadecimal form, like:
`35187c5096d10db8a57be93885f28694ac9dcaa09d6b1fb1903aec07e168430a`
