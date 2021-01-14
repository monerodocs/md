---
title: Verifying Monero Binaries Signature
---
# Verify Monero Binaries

Verification must be carried on **before extracting the archive and before using Monero**.

Instructions were tested on Linux. They should also work on macOS with slight modifications.

## 1. Import lead maintainer PGP key

This is a one time action. Skip this step for subsequent Monero releases.

Monero core developers sign a list of hashes of released binaries.

BinaryFate is Monero core developer who signs the releases.
His public key is available on GitHub in the project source code.
Import binaryFate's public key to your keyring:

`curl https://raw.githubusercontent.com/monero-project/monero/master/utils/gpg_keys/binaryfate.asc | gpg --import`

Trust binaryFate's public key (fingerprint must be exactly this):

    gpg --edit-key '81AC591FE9C4B65C5806AFC3F0AF4D462A0BDF92'
    trust
    4

!!! danger
    If key with this fingerprint was not found then remove imported key immediately (gpg --delete-keys ...).
    That would mean the key changed (likely was compromised).

## 2. Verify signature of hash list (hashes.txt)

The list of binaries and their hashes is published on [getmonero.org](https://www.getmonero.org/downloads/hashes.txt) and a few other places like release notes on [r/monero](https://reddit.com/r/monero).
Please note the publication channel does not matter as long as you properly verify the signature!                                                                        u

To verify these are real hashes (not tampered with) run:

`curl https://www.getmonero.org/downloads/hashes.txt | gpg --verify`

The expected output should contain the line:

`gpg: Good signature from "binaryFate <binaryfate@getmonero.org>"`

## 3. Verify the hash

By this step we checked that published hashes were not tampered with.

The last step is to compare published hash with downloaded archive SHA-256 hash.

[Download Monero](/interacting/download-monero-binaries) if you didn't already (but do not unpack).

Replace the example file name with actual one:

    file_name=monero-gui-linux-x64-v0.17.1.9.tar.bz2

    file_hash=`sha256sum $file_name | cut -c 1-64`

    curl https://www.getmonero.org/downloads/hashes.txt > /tmp/reference-hashes.txt

    # verify the signature (previous step is repeated here for completeness)
    gpg --verify /tmp/reference-hashes.txt

    # grep must print the hash (output cannot be empty)
    grep $file_hash /tmp/reference-hashes.txt

!!! danger
    If the grep output is empty then double check everything because apparently the hashes don't match.

If grep printed filename and hash then everything is alright!
