---
title: CryptoNight
---
# CryptoNight

> CryptoNight is a memory hard hash function

## Background

CryptoNight was originally designed around 2013 as part of the CryptoNote suite.

One design goal was to make it very friendly for the off-the-shelf CPU-s, by employing:

* native AES encryption 
* fast 64 bit multipliers
* scratchpad fitting exactly the size of the per-core L3 cache on Intel CPUs (about 2MB)

More ambitious design goal was to make it inefficiently computable on ASIC-s.
This goal has since failed, as it inevitably happens with "ASIC hard" algorithms.
Efficient CryptoNight ASIC was developed in 2017 by Bitmain. 

Monero inherited CryptoNight as its proof of work in 2014.
Since then Monero slightly evolved the algorithm to intentionally break compatibility with released ASIC-s. Three used variants existed: Cryptonightv1, Cryptonightv2 and [Cryptonight-R](https://github.com/SChernykh/CryptonightR).
**Monero no longer uses CryptoNight or any variant. Monero changed its mining algorithm to [RandomX](/proof-of-work/random-x) in November 2019.**

## The goal is to find small-enough hash

In hashing based PoW algorithms the goal is to find small-enough hash.

Hash is simply an integer (normally, a very large integer).
Most hashing functions result in 256-bit hashes (integers between 0 and 2^256).
This includes Bitcoin's double-SHA-256 and Monero's CryptoNight.

Miner randomly tweaks input data until the hash fits under specified threshold.
The threshold (also a large integer) is established collectively by the network as part of the consensus mechanism.
The PoW is only considered valid (solved) if hash fits under the threshold.  

Because hash functions are one-way, it is not possible to analytically calculate input data that would result in a small-enough hash.
The solution must be brute-forced by tweaking the input data and recalculating the hash over and over again.

Miners have a few areas of flexibility regarding input data - most importantly they can iterate with the nonce value.
They also have a power over which transactions are included in the block and how they are put together in a merkle tree. 

## Cryptographic primitives

CryptoNight is based on:

* AES encryption
* 5 hashing functions, all of which were finalists in NIST SHA-3 competition:
    * Keccak (the primary one)
    * BLAKE
    * Groestl
    * JH
    * Skein

## Input data

In Monero the input to hashing function is concatenation of:

* serialized block header (around 46 bytes; subject to varint representation)
* merkle tree root (32 bytes)
* number of transactions included in the block (around 1-2 bytes; subject to varint representation)

See [get_block_hashing_blob()](https://github.com/monero-project/monero/blob/master/src/cryptonote_basic/cryptonote_format_utils.cpp#L1078) function to dig further.

## Algorithm

!!! warning
    The article attempts to give reader a high-level understanding of the CryptoNight algorithm.
    For implementation details refer to CryptoNote Standard and Monero source code.
    See references at the bottom. 

### Overview

CryptoNight attempts to make memory access a bottleneck for performance ("memory hardness"). It has three steps:

1. Initialize large area of memory with pseudo-random data. This memory is known as the scratchpad.
2. Perform numerous read/write operations at pseudo-random (but deterministic) addresses on the scratchpad.
3. Hash the entire scratchpad to produce the resulting value.

### Step 1: scratchpad initialization

Firstly, the input data is hashed with Keccak-1600. This results in 200 bytes of pseudorandom data (1600 bits == 200 bytes).

These 200 bytes become a seed to generate a larger, 2MB-wide buffer of pseudorandom data,
by applying AES-256 encryption.

The first 0..31 bytes of Keccak-1600 hash are used as AES key.

The encryption is performed on 128 bytes-long payloads until 2MB is ready.
The first payload are Keccak-1600 bytes 66..191.
The next payload is encryption result of the previous payload.

Each 128-byte payload is actually encrypted 10 times.

The details are a bit more nuanced, see "Scratchpad Initialization" in [CryptoNote Standard](https://cryptonote.org/cns/cns008.txt).  

### Step 2: memory-hard loop

The second step is basically 524288 iterations of a simple stateful algorithm.

Each algorithm iteration reads from and writes back to the scratchpad,
at pseudorandom-but-deterministic locations.

Critically, next iteration depends on the state prepared by previous iterations.
It is not possible to directly calculate state of future iterations.

The specific operations include AES, XOR, 8byte_mul, 8byte_add - operations that are CPU-friendly (highly optimized on modern CPU-s).

The goal here is to make memory latency the bottleneck in attempt to close the gap between potential ASIC-s and general purpose CPU-s.

### Step 3: hashing

The final step (simplifying) is to:
 
* combine original Keccak-1600 output with the whole scratchpad
* pick the hashing algorithm based on 2 low-order bits of the result
    * 0=BLAKE-256
    * 1=Groestl-256
    * 2=JH-256 
    * 3=Skein-256
* hash the result with selected function

The resulting 256-bit hash is the final output of CryptoNight algorithm.

## Monero specific modifications

### CryptoNight v0

This is how Monero community refers to original implementation of CryptoNight.

### CryptoNight v1

See the [source code diff](https://github.com/monero-project/monero/pull/3253/files). 

### CryptoNight v2

See the [rationale](https://github.com/SChernykh/xmr-stak-cpu/blob/master/README.md) and the [source code diff](https://github.com/monero-project/monero/commit/5fd83c13fbf8dc304909345e60a853c15b0de1e5#diff-7000dc02c792439471da62856f839d62).

### CryptoNight v3 aka CryptoNightR

See the [rationale](https://github.com/monero-project/monero/pull/5126) and the [source code diff](https://github.com/monero-project/monero/pull/5126/files).

## Critique

* CryptoNight hash is relatively expensive to verify. This poses a risk of DoS-ing nodes with incorrect proofs to process. See [strong asymmetry](/proof-of-work/what-is-pow/#strong-asymmetry) requirement. 
* The hash function was designed from scratch with limited peer review. While CryptoNight is composed of proven and peer-reviewed primitives, combining secure primitives doesn't necessarily result in a secure cryptosystem.
* CryptoNight ultimately failed to prevent ASIC-s.
* Complexity of CryptoNight kills competition in ASIC manufacturing.

CryptoNight proof of work remains one of the most controversial aspect of Monero.

## Reference

* [CryptoNight hash function](https://cryptonote.org/cns/cns008.txt) description in the CryptoNote Standard
* [CryptoNight v2 source code](https://github.com/monero-project/monero/blob/master/src/crypto/slow-hash.c)
    * The entry point is `cn_slow_hash()` function. Manually removing support and optimizations for multiple architectures should help you understand the actual code. 
* "Egalitarian Proof of Work" chapter in [CryptoNote whitepaper](https://downloads.getmonero.org/whitepaper_annotated.pdf) 
* [First days of Monero mining](https://da-data.blogspot.com/2014/08/minting-money-with-monero-and-cpu.html) by dr David Andersen
* Some [test vectors](https://github.com/monero-project/monero/tree/master/tests/hash) in Monero source code 
