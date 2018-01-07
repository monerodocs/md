# Private keys in Monero

!!! danger
    Article author is nowhere close to being a cryptographer. Be sceptical on accuracy.

!!! warning
    Article is a work in progress.

Private key is generated [randomly](/cryptography/prng).

Private key must be kept secret.

Private key is a **large integer** impossible to guess, like:
`108555083659983933209597798445644913612440610624038028786991485007418559037440`

Private key is 256 bits long. 

Private key is a **scalar**, meaning it is a single value.

In equations scalars are represented by **lowercase letters**. 

In user-facing contexts, private key is encoded in a [little-endian](https://en.wikipedia.org/wiki/Endianness#Little) hexadecimal form, like:
`b3588a87056fb21dc4d052d59e83b54293882e646b543c29478e4cf45c28a402`

## Relation to Ed25519

Being a simple random integer, private key is not specific to any particular asymmetric cryptography scheme.

However, before deriving Ed25519 public key, the private key is subject to modulo `l`,
where `l` is the maximum scalar allowed by [Ed25519 scheme](/cryptography/asymmetric/ed25519).

The `l` is on the order of 2^252, so the effective key strength is technically 252 bits, not 256 bits.
This is standard for EC cryptography and is more of a cosmetic nuance than any real concern.
