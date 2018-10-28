---
title: Verify Monero Binaries Signature | Monero Documentation
---

# Verify Monero Binaries

Verification must be carried on **before extracting the archive and before using Monero**.

Instructions are for Linux but should also work on macOS with cosmetic modifications. 

## 0. Import core dev PGP key

This is a one time action. Skip this step for subsequent Monero releases.

Monero core developers sign a list of hashes of released binaries.

Riccardo "fluffypony" Spagni is Monero core developer who signs the releases.
Riccardo's public key is available on GitHub in the project source code.
Import Riccardo's public key to your keyring:

`curl https://raw.githubusercontent.com/monero-project/monero/master/utils/gpg_keys/fluffypony.asc | gpg --import`

Trust Riccardo's public key:

    gpg --edit-key '7455C5E3C0CDCEB9'
    trust
    4 

## 1. Verify signature of hash list  

The list of binaries and their hashes is published on [getmonero.org](https://www.getmonero.org/downloads/hashes.txt) and a few other places like release notes on [r/monero](https://reddit.com/r/monero).
Please note the publication channel does not matter as long as you properly verify the signature!  

To verify these are real hashes (not tampered with) run: 

`curl https://www.getmonero.org/downloads/hashes.txt | gpg --verify` 

The expected output is:

    ...
    gpg: Good signature from "Riccardo Spagni <ric@spagni.net>" [full]

## 2. Verify the hash

By this step we checked that published hashes were not tampered with.

The last step is to compare published hash with hash of downloaded archive.

Replace file name with yours:

    file_name=monero-linux-x64-v0.13.0.4.tar.bz2

    file_hash=`sha256sum $filename | cut -c 1-64`

    curl https://www.getmonero.org/downloads/hashes.txt > /tmp/reference-hashes.txt

    # verify the signature (previous step repeated here)
    gpg --verify /tmp/reference-hashes.txt

    grep $file_hash /tmp/reference-hashes.txt 

If grep displayed a line containing your binary name and a hash then all is fine!

If the output is empty then double check everything because apparently the hashes don't match.
