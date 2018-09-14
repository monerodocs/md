---
title: Subaddress | Monero Documentation
---
# Subaddress

Subaddress is what you should be using by default to receive Monero.

## Learn for what you are being paid

By providing a unique subaddress for each anticipated payment you will know for what you are being paid.

This use case overlaps with integrated addresses. Subaddresses are generally prefered for reasons outlined below.

## Prevent payer from linking your payouts together

To prevent the payer from linking your payouts together simply generate a new subaddress for each payout.
This way services like [Shapeshift](https://shapeshift.io) wouldn't know it is you again receving Monero.

Note it won't help if you have an account with the service. Then your payouts are already linked in the service database, regardless of Monero.

## Group funds into accounts

!!! Note
    Feel free to skip this if your are new to Monero. Accounts are not essential and currently not supported by the GUI.

**Accounts** are a convenience wallet-level feature to group subaddresses under one label and balance.

You may want to organize your funds into accounts like "cash", "work", "trading", "mining", "donations", etc.

As accounts are only groupings of subaddresses, they themselves do not have an address.

Accounts are deterministically derived from the root private key along with subaddresses.

As of September 2018 accounts are only supported by the CLI wallet and missing from GUI wallet.

Accounts are similar to subaccounts in your classic bank account. There is a very important difference though. In Monero funds don't really sit on accounts or public addresses. Public addresses are conceptually a gateway or a routing mechanism. Funds sit on transactions' unspent outputs. Thus, a single transaction can - in principle - aggregate and spend outputs from multiple addresses (and by extension from multiple accounts). The CLI or GUI wallet may not directly support creating such transactions for simplicity.

In short, think of accounts as a **soft** grouping of your funds.

## Why not multiple wallets?

The advantage over creating multiple wallets is that you only have a **single seed** to manage.
All subaddresses can be derived from the wallet seed.

Additionally, you conveniently manage your subaddresses within a single user interface.

## Wallet level feature

Subaddresses and accounts are a wallet-level feature to construct and interpret transactions. They do not affect the consensus. 

## Data structure

Subaddress has a dedicated "network byte":

Index       | Size in bytes    | Description
------------|------------------|-------------------------------------------------------------
0           | 1                | identifies the network and address type; [42](https://github.com/monero-project/monero/blob/784f7b07f05a645d43f62ed3a9cefda4b0c57825/src/cryptonote_config.h#L153) - main chain; [63](https://github.com/monero-project/monero/blob/784f7b07f05a645d43f62ed3a9cefda4b0c57825/src/cryptonote_config.h#L167) - test chain

Otherwise the data structure is the same as for [main address](/public-address/main-address/).

Each subaddress conceptually has an index (with 0 being the main address).
The index is not directly included in subaddress structure but is used as input to create the private view key.

## Generating

The private view key `m` for a subaddress is derived as follows:
    
    m = Hs(a || i)
    
Where:

* `Hs` is a Keccak-256 hash function interpreted as integer and modulo `l` (maximum edwards25519 scalar)
* `a` is a private view key of the base address
* `i` is a subaddress index

Deriving "sub view keys" from the "base view key" allows for creating a view only wallet that monitors entire wallet including subaddresses.

TODO: describe rest of the procedure.

## Caveates

* Subaddress **cannot** be used to receive transactions having multiple destinations (e.g. pool payouts). Only the main address (the one with index == 0) can receive such transactions.
* It is not recommended to sweep all the balances of subaddress to main address in a single transaction. That links the subaddresses together on the blockchain. However, this only concerns privacy against specific sender and the situation will never get worse than not using subaddresses in the first place. If you need to join funds while preserving maximum privacy do it with individual transactions (one per subaddress).
* Convenience labels are not preserved when recreating from seed.

## Reference

* [https://github.com/monero-project/monero/pull/2056](https://github.com/monero-project/monero/pull/2056)
* [https://www.reddit.com/r/Monero/comments/5vgjs2/subaddresses_and_disposable_addresses/](https://www.reddit.com/r/Monero/comments/5vgjs2/subaddresses_and_disposable_addresses/)
