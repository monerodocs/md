---
title: Stealth Address | Monero Documentation
---
# Stealth Address

## Hides recipient

Stealth address is a privacy technique to hide the recipient.

Even though blockchain is public, observer has no way to link the payment to the recipient.

Payments simply do **not** go to recipient address. Instead payments go to one-time "stealth" addresses.     

## One-time, per payment 

Stealth address is generated for each individual payment and must not be reused.

In Bitcoin, should stealth address be reused, the payments are linked.
Observer would learn these payments were to the same person.
This wouldn't be end of the world though, as most users would link the outputs anyway when spending from the wallet.  

In Monero, stealth address reuse leads to lose of funds.
If sender re-uses stealth address, then recipient will only be able to claim one of the payments.
See [key image](/cryptography/asymmetric/key-image) to learn why this is the case.
Practically, to re-use stealth address, sender would have to manually craft a malicious transaction.
Recipient would simply not acknowledge receiving the payment, as if sender never paid.

## Wallet level feature

Stealth addresses are not part of the consensus layer. For transaction to be valid,
it does not matter how the key pairs were generated.

Stealth address is non-interactive protocol between sender and recipient.

## Elliptic curves magic properties

Before going further understand the following properties of elliptic curves.

Once you internalize these critical properties,
you will be able to easily come up with a stealth address scheme yourself. 

### It is possible to establish a shared secret without sharing a secret

Two parties can come up with the same secret number w/o sending anything except their public keys.

Specifically, having 2 unrelated key pairs, you can exchange public keys, and then each party can independently calculate the same secret number, simply by multiplying own private key with other party's public key:

`s = aB = bA`, where:

* s - the secret (256-bit number)
* a - Alice private key
* A - Alice public key
* b - Bob private key
* B - Bob public key

### A new key pair can be derived by multiplying both keys 

Having a key pair, you can derive a new key pair, simply by multiplying both keys by an integer.

Surprisingly, the new key pair will be valid, i.e. the private key will match the public key.  

## Stealth address protocol

1. Sender Alice generates a new key pair. Note this is entirely local to the sender. 

* a - private key
* A - public key

2. Sender Alice gets receiver's (Bob) public key from his address, `B`.

3. Sender calculates the secret:

`s = rB`

## Reference

http://www.scitepress.org/DigitalLibrary/Link.aspx?doi=10.5220/0006270005590566
