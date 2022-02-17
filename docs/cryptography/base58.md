---
title: Base58
---
# Base58

Base58 is a binary-to-text encoding scheme. It is similar to Base64 but has been modified to avoid both non-alphanumeric characters and letters which might look ambiguous when printed. The characters excluded in relation to Base64 are: `IOl0+/`

Base58 does not strictly specify the format. This results in some implementations being incompatible with others, for example with regard to alphabet order.

For details, see [Wikipedia](https://en.wikipedia.org/wiki/Base58).

## Base58 in Monero

Monero has its own variant of Base58.

In Monero the Base58 encoding is performed in 8-byte blocks, except the last block which is the remaining (8 or less) bytes .

The 8-byte block converts to 11 or less Base58 characters. If the block converted to less than 11 characters, the output is padded with "1"s (0 in Base58). The final block is padded as well to whatever would be the maximum size of this number of bytes encoded in Base58.

The advantage of Monero implementation is that output is of a fixed size which is not the case with plain Base58. The disadvantage is that default libraries won't work.

For details, see [reference C++ Base58](https://github.com/monero-project/monero/blob/master/src/common/base58.cpp) implementation and [unofficial Python Base58](https://github.com/bigreddmachine/MoneroPy/blob/master/moneropy/base58.py) implementation.
