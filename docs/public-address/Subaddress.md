# Subaddress

**!! This is unreleased feature !!**

Subaddresses serve two purposes described below.

## Prevent payer from linking your payouts together

To prevent the payer from linking your payouts together simply generate a new subaddress for each payout.
This way services like [Shapeshift](https://shapeshift.io) wouldn't know it is you again receving Monero.

Note it won't help if you have an account with the service. Then your payouts are already linked in the service database, regardless of Monero.

## Group incoming payments

Think income streams.

Subaddresses allow to group incoming transactions within a single wallet.

User interface allows to assign convenience labels to subaddresses.

You may want to organize your incoming funds into a streams like "donations", "work", etc.

This is similar to subaccounts in your bank account. There is a very important difference though.

In Monero funds don't really sit on public addresses. Public addresses are conceptually a gateway or a routing mechanism. Funds sit on the unspent outputs. Thus, a single transaction can aggregate and spent outputs from multiple addresses.

## Why not multiple wallets?

The advantage over creating multiple wallets is that you only have a **single seed** to manage.
All subaddresses can be derived from the wallet seed.

Additionally, you conveniently manage your subaddresses within a single user interface.

## Data structure

Subaddress has a dedicated "network byte":

Index       | Size in bytes    | Description
------------|------------------|-------------------------------------------------------------
0           | 1                | identifies the network and address type; [42](https://github.com/monero-project/monero/blob/784f7b07f05a645d43f62ed3a9cefda4b0c57825/src/cryptonote_config.h#L153) - main chain; [63](https://github.com/monero-project/monero/blob/784f7b07f05a645d43f62ed3a9cefda4b0c57825/src/cryptonote_config.h#L167) - test chain

Otherwise the data structure is the same as for [standard address](public-address/standard-address).

Each subaddress conceptually has an index (with 0 being the base standard address).
The index is not directly included in subaddress structure but is used as input to create the private spend key.

## Caveates

* Subaddress **cannot** be used to receive transactions having multiple destinations (e.g. pool payouts). Only the standard address (the one with index == 0) can receive such transactions.
* It is not recommended to sweep all the balances of subaddress to main address in a single transaction. That links the subaddresses together on the blockchain. However, this only concerns privacy against specific sender and the situation will never get worse than not using subaddresses in the first place. If you need to join funds while preserving maximum privacy do it with individual transactions (one per subaddress).
* Convenience labels are not preserved when recreating from seed.

## Reference

* [https://github.com/monero-project/monero/pull/2056](https://github.com/monero-project/monero/pull/2056)
* [https://www.reddit.com/r/Monero/comments/5vgjs2/subaddresses_and_disposable_addresses/](https://www.reddit.com/r/Monero/comments/5vgjs2/subaddresses_and_disposable_addresses/)
