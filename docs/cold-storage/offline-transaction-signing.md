# Offline Transaction Signing

!!! warning
    This is **NOT** necessarily the recommended cold storage setup,
    due to high complexity, large room for errors and employing
    a general purpose computer for transaction signing (even if offline).

    Published for educational purposes only to understand "what would it take to sign offline".

    It is generally better to use a hardware wallet like Trezor or Ledger.

    Opinions may vary.

!!! note
    This is a guest tutorial contributed by [crocket](https://github.com/crocket).

Offline transaction signing involves:

* Creating an unsigned transaction on an online, view-only wallet
* Moving the unsigned transaction to an offline machine
* Signing the unsigned transaction on an offline machine
* Moving the signed transaction back to online, view-only wallet
* Broadcasting the transaction

## Creating a new offline wallet

Constructing a new offline wallet is done by executing:

```
monero-wallet-cli --generate-new-wallet /path/to/wallet-file
```

on an offline machine. Record the seed on paper by executing `seed` on the offline wallet.

## Creating a new offline wallet with seed offset passphrase

Seed and seed offset passphrase combine to create a new seed. You can store seed
and seed offset passphrase in separate places so that a thief can't steal your
fund without stealing seed and seed offset passphrase. I recommend 6 to 8
(english) words as a seed offset passphrase as one english word has 11 bits of
entropy on average and 8 words have 88 bits of entropy. With seed passphrase,
you can also create decoy wallets that contain a little bit of money and can
protect you from torturers or blackmailers who demand money from you.

If you want to create an offline wallet with seed and seed passphrase,
create an offline wallet, record the seed on paper, delete the wallet file,
generate seed offset passphrase, record seed offset passphrase on paper, and
execute

```
monero-wallet-cli --generate-new-wallet /path/to/wallet-file \
---restore-deterministic-wallet
```

to restore from seed and seed offset passphrase. When you restore from seed, you
can enter seed offset passphrase.

Generate seed offset passphrase on an offline machine or with diceware because
humans are bad at creating random passphrases.

If you want to reconstruct an existing offline wallet that received or sent
transactions, you need extra steps. Refer to `Restoring offline wallet`.

## Creating a new view-only wallet

To create a view-only wallet, copy primary address and secret view key from an
offline wallet to an online machine where a view-only wallet is going to be
created. You can get primary address by executing `address` on an offline wallet
and secret view key by executing `viewkey` on the offline wallet.

You can use a microSD card and two USB microSD card readers to exchange data
between an offline wallet and a view-only wallet. You can also use a USB flash
drive.

To create a view-only wallet on an online machine, execute

```
monero-wallet-cli --generate-from-view-key /path/to/wallet-file \
--daemon-address remote-node-address:port
```

If you want to reconstruct an existing view-only wallet that received or sent
transactions, refer to `Restoring view-only wallet`.

## Launching offline wallet

Execute

```
monero-wallet-cli --wallet-file /path/to/wallet-file
```

## Launching a view-only wallet

Execute

```
monero-wallet-cli --wallet-file /path/to/wallet-file \
--daemon-address remote-node-address:port
```

It's safe to sync your wallet over clearnet. If you want to broadcast a
transaction without revealing your IP address, execute

```
monero-wallet-cli --wallet-file /path/to/wallet-file \
--daemon-address tor-or-i2p-remote-node-address:port \
--proxy 127.0.0.1:tor-or-i2p-port
```

Synchronizing wallet over clearnet is a lot faster than doing it on tor or i2p.
Thus, consider synchronizing over clearnet even if you broadcast transactions
over tor or i2p.

## Offline transaction signing

Execute any wallet command that transfers monero to any address. For example,

```
transfer xmr-address amount-of-xmr-to-send
```

Any transfer command on a view-only wallet creates `unsigned_monero_tx` in the
current working directory.

Move `unsigned_monero_tx` to an offline machine that has an offline wallet.
Execute

```
sign_transfer
```

on the offline wallet in the directory with `unsigned_monero_tx`.
`signed_monero_tx` file is created in the current working directory. Move
`signed_monero_tx` to the online machine with a view-only wallet. In the
directory with `signed_monero_tx`, launch the view-only wallet, and execute

```
submit_transfer
```

Because a view-only wallet doesn't have key images, it can't see outgoing
transactions. To make a view-only wallet see outgoing transactions, it has
to export new outputs created by `submit_transfer` to an offline wallet which
creates key images out of new outputs.

Execute

```
export_outputs outputs
```

on a view-only wallet. Move `outputs` file to the offline machine with an offline
wallet. Launch the offline wallet, and execute

```
import_outputs /path/to/outputs
```

Export key images derived from new outputs by executing

```
export_key_images key_images
```

on an offline wallet. Move `key_images` file to the machine with a view-only
wallet. Launch the view-only wallet, and execute

```
import_key_images /path/to/key_images
```

## Updating wallet software on an offline signing machine

When you update an offline machine with offline wallets, you can't just
connect the machine to the internet and update wallet software because
doing so exposes offline wallets to the internet.

Instead, boot OS installation media, wipe filesystems, and then connect
to the internet, and install everything from scratch again.

If your root filesystem is encrypted, OS installation media can connect
to the internet from the beginning because encrypted data are safe until
they are decrypted.

## Restoring offline wallet

After updating wallet software on an offline signing machine by wiping it out
and reinstalling everything, you have to restore offline wallet.

Restore an offline wallet from seed (and seed offset passphrase) by executing

```
monero-wallet-cli --generate-new-wallet wallet-file --restore-deterministic-wallet
```

The new offline wallet can't sign new transactions because it doesn't have
all trasaction outputs that precede a new unsigned transaction. Thus, it first
has to import all outputs from a view-only wallet.

On a view-only wallet that was derived from the offline wallet, execute

```
export_outputs all all_outputs
```

`all` is important because `export_outputs` exports only new outputs that weren't
exported before, but

```
export_outputs all
```

exports all outputs. Move `all_outputs` file to the offline machine with the
offline wallet. Execute

```
import_outputs /path/to/all_outputs
```

on the new offline wallet.

## Restoring view-only wallet

If you reconstruct view-only wallet, because it doesn't have key images,
it can't see outgoing transactions. If it can't see outgoing transactions,
it reports wrong account balances. Thus, it has to import all key images
from its corresponding offline wallet.

On the offline wallet, execute

```
export_key_images all all_key_images
```

`export_key_images` doesn't work because it exports only new key images that
weren't exported before.

```
export_key_images all
```

exports all key images. Move `all_key_images` file to the machine with the
view-only wallet. Execute

```
import_key_images /path/to/all_key_images
```
