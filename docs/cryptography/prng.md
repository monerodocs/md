---
title: Monero Pseudorandom Number Generator
---
# Monero Pseudorandom Number Generator

Monero uses PRNG based on the Keccak hashing function.
Basically, output of the previous hashing round is input for the next one.

The initial seed comes from entropy sources provided by operating system.
On Linux and MacOS the seed comes from `/dev/urandom`.
On Windows the WinAPI `CryptGenRandom` call is used for seeding.

There is no reseeding.

## Caveats

* This concerns the reference C++ implementation of Monero.
Please note there are many alternative implementations of private key generation,
including JavaScript, Python, Android/Java. These should be researched case by case for correctness.    
* In Monero source code you can also find libsodium based random bytes generator. It is part of the embedded library and apparently is not used in actual Monero code.  

## Reference

* [Source code](https://github.com/monero-project/monero/blob/1a4298685aa9e694bc555ae69be59d14d3790465/src/crypto/random.c)
* [StackExchange answer](https://monero.stackexchange.com/a/2076/3218)
