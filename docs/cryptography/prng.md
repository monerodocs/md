# Monero Pseudo Random Number Generator

Monero uses PRNG based on Keccak hashing function.

The seed comes from entropy sources provided by operating system.
On Linux and MacOS this translates to `/dev/urandom`.
On Windows the WinAPI `CryptGenRandom` call is used.

There is no reseeding.

## Caveats

* In Monero source code you can also find libsodium based random bytes generator. It is part of the embedded library and apparently is not used in actual Monero code.  

## Reference

* [Source code](https://github.com/monero-project/monero/blob/1a4298685aa9e694bc555ae69be59d14d3790465/src/crypto/random.c)
* [StackExchange answer](https://monero.stackexchange.com/a/2076/3218)
