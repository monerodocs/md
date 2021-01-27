---
title: Subaddress
---
# Subaddress

Subaddress is what you should be using by default to receive Monero.

## Learn for what you are being paid

By providing a unique subaddress for each anticipated payment you will know for what you are being paid.

This use case overlaps with integrated addresses. Subaddresses are generally prefered for reasons outlined below.

## Prevent payer from linking your payouts together

To prevent the payer from linking your payouts together simply generate a new subaddress for each payout.
This way specific service (like anonymous exchange) that sends you Monero won't (easilly) know it is you again receving Monero.

The exception to this is when a service (or group of colluding services) decides to actively attack you, one address at the time, with the so-called [Janus attack](https://web.getmonero.org/2019/10/18/subaddress-janus.html), which risks them losing funds. If you need perfect unlinkability of your receivables, the only solution remains to use a separate seed (separate Monero wallet).

Also, note it won't help if you have an account with the service. Then your payouts are already linked in the service database, regardless of Monero.

## Group funds into accounts

**Accounts** are a convenience wallet-level feature to group subaddresses under one label and balance.

You may want to organize your funds into accounts like "cash", "work", "trading", "mining", "donations", etc.

As accounts are only groupings of subaddresses, they themselves do not have an address.

Accounts are deterministically derived from the root private key along with subaddresses.

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
0           | 1                | identifies the network and address type; [42](https://github.com/monero-project/monero/blob/31bdf7bd113c2576fe579ef3a25a2d8fef419ffc/src/cryptonote_config.h#L171) - mainnet; [36](https://github.com/monero-project/monero/blob/31bdf7bd113c2576fe579ef3a25a2d8fef419ffc/src/cryptonote_config.h#L200) - stagenet; [63](https://github.com/monero-project/monero/blob/31bdf7bd113c2576fe579ef3a25a2d8fef419ffc/src/cryptonote_config.h#L185) - testnet

Otherwise the data structure is the same as for the [standard address](/public-address/standard-address/#data-structure).

## Generating

Each subaddress conceptually has:
 
* account index (also known as "major" index)
* subaddress index within the account (also known as "minor" index)

The indexes are 0-based. By default wallets use account index 0.

The indexes are not directly included in the subaddress data structure.
Instead, they are used as input to generating subaddress keys.

### Private view key

A per-subaddress scalar `m` is derived as follows:

    m = Hs("SubAddr" || a || account_index || subaddress_index_within_account)
    
Where:

* `Hs` is a Keccak-256 hash function interpreted as integer and modulo `l` (maximum edwards25519 scalar)
* `||` is a byte array concatenation operator
* `SubAddr` is a 0-terminated fixed string (8 bytes total)
* `a` is a private view key of the standard address (a 32 byte little endian unsigned integer)
* `account_index` is index of an account (a 32 bit little endian unsigned integer)
* `subaddress_index_within_account` is index of the subaddress within the account (a 32 bit little endian unsigned integer)

Deriving "sub view keys" from the main view key allows for creating a view only wallet that monitors the entire wallet including subaddresses.

### Public spend key

The subaddress public spend key `D` is derived as follows:

    D = B + m*G

Where:

* `B` is standard address public spend key
* `m` is a per-subaddress scalar that is derived from the private spend key
* `G` is the "base point"; this is simply a constant specific to [edwards25519](/cryptography/asymmetric/edwards25519)

### Public view key

The subaddress public view key `C` is derived as follows:

    C = a*D

Where:

* `a` is a private view key of the standard address
* `D` is a public spend key of the subaddress

### Special case for (0, 0)

The subaddress #0 on the account #0 is the [standard address](/public-address/standard-address).
As standard address has different generation rules, this is simply implemented via an `if` statement.

### Building the address string

The procedure is the same as for the [standard address](/public-address/standard-address).

## Caveats

* It is not recommended to sweep all the balances of subaddress to standard address in a single transaction. That links the subaddresses together on the blockchain. However, this only concerns privacy against specific sender and the situation will never get worse than not using subaddresses in the first place. If you need to join funds while preserving maximum privacy do it with individual transactions (one per subaddress).
* Convenience labels are not preserved when recreating from seed.

## Reference

* [monero-python](https://github.com/emesik/monero-python/blob/125d5eac0d4583b586b98e21b28fb9a291db26e5/monero/wallet.py#L195) - the easiest to follow implementation by Michał Sałaban
* [get_subaddress_spend_public_key()](https://github.com/monero-project/monero/blob/16dc6900fb556b61edaba5e323497e9b8c677ae2/src/device/device_default.cpp#L143) - Monero reference implementation
* [historical discussion on Github](https://github.com/monero-project/monero/pull/2056) - gives context but is not up to date with all details
* [StackExchange answer](https://monero.stackexchange.com/questions/10674/how-are-subaddresses-and-account-addresses-generated-from-master-wallet-keys/10676#10676) - excellent summary by knaccc
