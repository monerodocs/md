---
title: Verifying Monero Binaries Signature | Monero Documentation
---
# Verify Monero Binaries

Verification must be carried on **before extracting the archive and before using Monero**.

Instructions were tested on Linux. They should also work on macOS with slight modifications. 

## 1. Import core dev PGP key

This is a one time action. Skip this step for subsequent Monero releases.

Monero core developers sign a list of hashes of released binaries.

Riccardo "fluffypony" Spagni is Monero core developer who signs the releases.
Riccardo's public key is available on GitHub in the project source code.
Import Riccardo's public key to your keyring:

`curl https://raw.githubusercontent.com/monero-project/monero/master/utils/gpg_keys/fluffypony.asc | gpg --import`

Trust Riccardo's public key (fingerprint must be exactly this):

    gpg --edit-key 'BDA6BD7042B721C467A9759D7455C5E3C0CDCEB9'
    trust
    4 

!!! danger
    If key with this fingerprint was not found then remove imported key immediately (gpg --delete-keys ...).
    That would mean the key changed (likely was compromised).

## 2. Verify signature of hash list  

The list of binaries and their hashes is published on [getmonero.org](https://www.getmonero.org/downloads/hashes.txt) and a few other places like release notes on [r/monero](https://reddit.com/r/monero).
Please note the publication channel does not matter as long as you properly verify the signature!                                                                        u 

To verify these are real hashes (not tampered with) run: 

`curl https://www.getmonero.org/downloads/hashes.txt | gpg --verify` 

The expected output should contain the line:

`gpg: Good signature from "Riccardo Spagni <ric@spagni.net>" [full]`

## 3. Verify the hash

By this step we checked that published hashes were not tampered with.

The last step is to compare published hash with downloaded archive SHA-256 hash.

[Download Monero](/interacting/download-monero-binaries) if you didn't already (but do not unpack).

Replace the example file name with actual one:

    file_name=monero-linux-x64-v0.14.0.0.tar.bz2

    file_hash=`sha256sum $file_name | cut -c 1-64`

    curl https://www.getmonero.org/downloads/hashes.txt > /tmp/reference-hashes.txt

    # verify the signature (previous step is repeated here for completeness)
    gpg --verify /tmp/reference-hashes.txt

    # grep must print the hash (output cannot be empty)
    grep $file_hash /tmp/reference-hashes.txt 

!!! danger
    If the grep output is empty then double check everything because apparently the hashes don't match.

If grep printed filename and a hash then everything is alright!
