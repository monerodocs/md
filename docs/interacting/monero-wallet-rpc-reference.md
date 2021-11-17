---
title: monero-wallet-rpc - Reference
---

# `monero-wallet-rpc` - Reference

!!! note
    This is only relevant for programmers. Everyday users won't need `monero-wallet-rpc`.

!!! note
    Use [stagenet](/infrastructure/networks) for learning and development on top of `monero-wallet-rpc`.

## Overview

### Provides wallet API over HTTP

Programmers can build thin user interfaces (UIs) on top of `monero-wallet-rpc`.

Programmers can also use `monero-wallet-rpc` to build arbitrary wallet automation driven by HTTP calls.

Think of `monero-wallet-rpc` as complete wallet logic exposed via JSON-RPC over HTTP.

Wallet uses your private keys to understand your total balance,
transactions history, and to facilitate creating transactions.

However, wallet does not store the blockchain and does not directly participate in the p2p network.

### Depends on the full node

Wallet connects to a [full node](/interacting/monerod-reference) to scan the blockchain for your transaction outputs and to send your transactions out to the network.

The full node can be either local (same computer) or remote.

You can play with [CLI wallet](/interacting/monero-wallet-cli-reference) and [GUI wallet](/interacting/monero-wallet-gui-reference) first to understand the relationship between the full node, the wallet and the user.

## Syntax

`./monero-wallet-rpc --rpc-bind-port <port> (--wallet-file <file>|--generate-from-json <file>|--wallet-dir <directory>) [options]`

Or with a config file:

`./monero-wallet-rpc --config-file <arg>`

## Running

Make sure you are running a locally synced `monerod` or point to a remote daemon with `--daemon-address` option.

#### Linux (Production Example)

`./monero-wallet-rpc --rpc-bind-port 28088 --wallet-file wallets/main/main --password walletPassword --rpc-login monero:rpcPassword --log-file logs/monero-wallet-rpc.log --max-log-files 2 --trusted-daemon --non-interactive`

- If the RPC is used to retrieve information not dependent on any spending, consider using a view-only to prevent abuse.
- `--rpc-login` should be used in production to protect against any network attacks. An uncommon password protects against the case where the RPC port is open to the public

#### Windows (Development Example)

`monero-wallet-rpc --rpc-bind-port 28088 --wallet-file wallets\main\main --password walletPassword --daemon-address http://node.supportxmr.com:18081 --untrusted-daemon --disable-rpc-login`

- Specifying `--untrusted-daemon` is not necessary but tells yourself that the daemon is untrusted and that commands requiring a trusted daemon will be disabled
- Default installation on Windows is `"C:\Program Files\Monero GUI Wallet"`

### Trouble Shooting

If the expected RPC URL, say [http://127.0.0.1:28088/json_rpc](http://127.0.0.1:28088/json_rpc), is unavailabe, or there is no terminal output saying that the server has been started, `monero-wallet-rpc` might be trying to synchronize the wallet. In that case, you should use the GUI or CLI to sync that wallet file because using the GUI/CLI results in faster and measurable syncing.

The suggested way is to have two wallet files for the same keys. One that is used manually (synced often), and one that is used by `monero-wallet-rpc`. Whenever you decide to use `monero-wallet-rpc` and encounter the unresponsive issue, simply copy the files of the GUI/CLI wallet and replace the ones that were being used by `monero-wallet-rpc`. This problem should only occur on the development system where `monerod` or `monero-wallet-rpc` might not have been running for weeks. In production, `monerod` and `monero-wallet-rpc` should have minimal downtimes, ensuring that the wallet is always synchronized.

## API conventions

The API is based on [JSON-RPC standard](https://en.wikipedia.org/wiki/JSON-RPC) version 2.0.

All `monero-wallet-rpc` method calls use the same JSON-RPC interface.

Assuming your `monero-wallet-rpc` is running on 127.0.0.1:18082, you would call it like this:

``` Bash
IP=127.0.0.1
PORT=18082
METHOD="make_integrated_address"
PARAMS="{\"payment_id\":\"1234567890123456789012345678900012345678901234567890123456789000\"}"
curl \
    -u username:password --digest \
    -X POST http://$IP:$PORT/json_rpc \
    -d '{"jsonrpc":"2.0","id":"0","method":"'$METHOD'","params":'"$PARAMS"'}' \
    -H 'Content-Type: application/json'
```

The **atomic unit** used by the API is the smallest fraction of 1 XMR according to the monerod implementation.
1 XMR = 1e12 atomic units.

## Options

### Help and Version

| Option       | Description
|--------------|------------
|  `--help`    | Produce help message
|  `--version` | Output version information

### Pick Network

| Option        | Description
|---------------|------------
|  `--testnet`  | For testnet. Daemon must also be launched with --testnet flag
|  `--stagenet` | For stagenet. Daemon must also be launched with --stagenet flag

### Logging

| Option                                 | Description
|----------------------------------------|------------
|  `--log-file <arg>`                    | Specify log file
|  `--log-level <arg>`                   | 0-4 or categories
|  `--max-log-file-size <arg=104850000>` | Specify maximum log file size in bytes
|  `--max-log-files <arg=50>`            | Specify maximum number of rotated log files to be saved (no limit by setting to 0)

### Daemon (Node)

| Option                                     | Description
|--------------------------------------------|-----
|  `--daemon-address <arg>`                  | Use daemon instance at \<host>:\<port>
|  `--daemon-host <arg>`                     | Use daemon instance at host \<arg> instead of localhost
|  `--proxy <arg>`                           | \[\<ip>:]\<port> socks proxy to use for daemon connections
|  `--trusted-daemon`                        | Enable commands which rely on a trusted daemon
|  `--untrusted-daemon`                      | Disable commands which rely on a trusted daemon
|  `--password <arg>`                        | Wallet password (escape/quote as \| needed)
|  `--password-file <arg>`                   | Wallet password file
|  `--daemon-port <arg=0>`                   | Use daemon instance at port \<arg> instead of 18081
|  `--daemon-login <arg>`                    | Specify username\[:password] for daemon RPC client
|  `--daemon-ssl <arg=autodetect)`           | Enable SSL on daemon RPC connections: enabled\|disabled\|autodetect
|  `--daemon-ssl-private-key <arg>`          | Path to a PEM format private key
|  `--daemon-ssl-certificate <arg>`          | Path to a PEM format certificate
|  `--daemon-ssl-ca-certificates <arg>`      | Path to file containing concatenated PEM format certificate(s) to replace system CA(s).
|  `--daemon-ssl-allowed-fingerprints <arg>` | List of valid fingerprints of allowed RPC servers
|  `--daemon-ssl-allow-any-cert`             | Allow any SSL certificate from the daemon
|  `--daemon-ssl-allow-chained`              | Allow user (via --daemon-ssl-ca-certificates) chain certificates

### Other Useful

| Option                | Description
|-----------------------|-----
| `--tx-notify <arg>`   | Run a program for each new incoming transaction, '%s' will be replaced by the transaction hash
| `--non-interactive`   | Run non-interactive (useful when input is DEVNULL)
| `--config-file <arg>` | Config file

### RPC

| Option                                          | Description
|-------------------------------------------------|-----
|  `--rpc-bind-port <arg>`                        | Sets bind port for server
|  `--disable-rpc-login`                          | Disable HTTP authentication for RPC connections served by this process
|  `--restricted-rpc`                             | Restricts to view-only commands
|  `--rpc-bind-ip <arg=127.0.0.1>`                | Specify IP to bind RPC server
|  `--rpc-bind-ipv6-address <arg=::1>`            | Specify IPv6 address to bind RPC server
|  `--rpc-restricted-bind-ip <arg=127.0.0.1>`     | Specify IP to bind restricted RPC server
|  `--rpc-restricted-bind-ipv6-address <arg=::1>` | Specify IPv6 address to bind restricted RPC server
|  `--rpc-use-ipv6`                               | Allow IPv6 for RPC
|  `--rpc-ignore-ipv4`                            | Ignore unsuccessful IPv4 bind for RPC
|  `--rpc-login <arg>`                            | Specify username\[:password] required for RPC server
|  `--confirm-external-bind`                      | Confirm rpc-bind-ip value is NOT a loopback (local) IP
|  `--rpc-access-control-origins <arg>`           | Specify a comma separated list of origins to allow cross origin resource sharing
|  `--rpc-ssl <arg=autodetect>`                   | Enable SSL on RPC connections: enabled\|disabled\|autodetect
|  `--rpc-ssl-private-key <arg>`                  | Path to a PEM format private key
|  `--rpc-ssl-certificate <arg>`                  | Path to a PEM format certificate
|  `--rpc-ssl-ca-certificates <arg>`              | Path to file containing concatenated PEM format certificate(s) to replace system CA(s).
|  `--rpc-ssl-allowed-fingerprints <arg>`         | List of certificate fingerprints to allow
|  `--rpc-ssl-allow-chained`                      | Allow user (via --rpc-ssl-certificates) chain certificates
|  `--disable-rpc-ban`                            | Do not ban hosts on RPC errors
|  `--rpc-client-secret-key <arg>`                | Set RPC client secret key for RPC payments

### Open Existing Wallet

| Option                      | Description
|-----------------------------|-----
| `--wallet-file <arg>`       | Use wallet \<arg>
| `--wallet-dir <arg>`        | Directory for newly created wallets
| `--prompt-for-password`     | Prompts for password when not provided
| `--max-concurrency <arg=0>` | Max number of threads to use for a parallel job

### Create new Wallet

| Option                         | Description
|--------------------------------|-----
| `--kdf-rounds <arg=1>`         | Number of rounds for the key derivation function
| `--hw-device <arg>`            | HW device to use
| `--hw-device-deriv-path <arg>` | HW device wallet derivation path (e.g., SLIP-10)
| `--extra-entropy <arg>`        | File containing extra entropy to initialize the PRNG (any data, aim for 256 bits of entropy to be useful, which typically means more than 256 its of data)
| `--generate-from-json <arg>`   | Generate wallet from JSON format file

### Windows Service

| Option                 | Description
|------------------------|-----
|  `--run-as-service`    | true if running as windows service
|  `--install-service`   | Install Windows service
|  `--uninstall-service` | Uninstall Windows service
|  `--start-service`     | Start Windows service
|  `--stop-service`      | Stop Windows service

### Legacy and Rare Uses

| Option                                              | Description
|-----------------------------------------------------|-----
| `--shared-ringdb-dir <arg=C:\ProgramData\.shared-ringdb, C:\ProgramData\.shared-ringdb\testnet if 'testnet', C:\ProgramData\.shared-ringdb\stagenet if 'stagenet'>`                | Set shared ring database path
| `--no-dns`                                          | Do not use DNS
| `--offline`                                         | Do not connect to a daemon, nor use DNS
| `--bitmessage-address <arg=http://localhost:8442/>` | Use PyBitmessage instance at URL \<arg>
| `--bitmessage-login <arg=username:password>`        | Specify \<arg> as username:password for PyBitmessage API

## Index of JSON-RPC Methods

- [**add_address_book**](#add_address_book)
- [**change_wallet_password**](#change_wallet_password)
- [**check_reserve_proof**](#check_reserve_proof)
- [**check_spend_proof**](#check_spend_proof)
- [**check_tx_key**](#check_tx_key)
- [**check_tx_proof**](#check_tx_proof)
- [**close_wallet**](#close_wallet)
- [**create_account**](#create_account)
- [**create_address**](#create_address)
- [**create_wallet**](#create_wallet)
- [**delete_address_book**](#delete_address_book)
- [**export_key_images**](#export_key_images)
- [**export_multisig_info**](#export_multisig_info)
- [**export_outputs**](#export_outputs)
- [**finalize_multisig**](#finalize_multisig)
- [**get_account_tags**](#get_account_tags)
- [**get_accounts**](#get_accounts)
- [**get_address**](#get_address)
- [**get_address_book**](#get_address_book)
- [**get_address_index**](#get_address_index)
- [**get_attribute**](#get_attribute)
- [**get_bulk_payments**](#get_bulk_payments)
- [**get_height**](#get_height)
- [**get_languages**](#get_languages)
- [**get_payments**](#get_payments)
- [**get_reserve_proof**](#get_reserve_proof)
- [**get_spend_proof**](#get_spend_proof)
- [**get_transfer_by_txid**](#get_transfer_by_txid)
- [**get_transfers**](#get_transfers)
- [**get_tx_key**](#get_tx_key)
- [**get_tx_notes**](#get_tx_notes)
- [**get_tx_proof**](#get_tx_proof)
- [**get_version**](#get_version)
- [**import_key_images**](#import_key_images)
- [**import_multisig_info**](#import_multisig_info)
- [**import_outputs**](#import_outputs)
- [**incoming_transfers**](#incoming_transfers)
- [**is_multisig**](#is_multisig)
- [**label_account**](#label_account)
- [**label_address**](#label_address)
- [**make_integrated_address**](#make_integrated_address)
- [**make_multisig**](#make_multisig)
- [**make_uri**](#make_uri)
- [**open_wallet**](#open_wallet)
- [**parse_uri**](#parse_uri)
- [**prepare_multisig**](#prepare_multisig)
- [**query_key**](#query_key)
- [**refresh**](#refresh)
- [**relay_tx**](#relay_tx)
- [**rescan_blockchain**](#rescan_blockchain)
- [**rescan_spent**](#rescan_spent)
- [**set_account_tag_description**](#set_account_tag_description)
- [**set_attribute**](#set_attribute)
- [**set_tx_notes**](#set_tx_notes)
- [**sign**](#sign)
- [**sign_multisig**](#sign_multisig)
- [**sign_transfer**](#sign_transfer)
- [**split_integrated_address**](#split_integrated_address)
- [**start_mining**](#start_mining)
- [**stop_mining**](#stop_mining)
- [**stop_wallet**](#stop_wallet)
- [**store**](#store)
- [**submit_multisig**](#submit_multisig)
- [**submit_transfer**](#submit_transfer)
- [**sweep_all**](#sweep_all)
- [**sweep_dust**](#sweep_dust)
- [**sweep_single**](#sweep_single)
- [**tag_accounts**](#tag_accounts)
- [**transfer**](#transfer)
- [**transfer_split**](#transfer_split)
- [**untag_accounts**](#untag_accounts)
- [**verify**](#verify)

## JSON-RPC Methods

### **add_address_book**

Add an entry to the address book.

Alias:  _None_.

Inputs:

-   _address_  - string;
-   _payment_id_  - (optional) string, defaults to "0000000000000000000000000000000000000000000000000000000000000000";
-   _description_  - (optional) string, defaults to "";

Outputs:

-   _index_  - unsigned int; The index of the address book entry.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"add_address_book","params":{"address":"78P16M3XmFRGcWFCcsgt1WcTntA1jzcq31seQX1Eg92j8VQ99NPivmdKam4J5CKNAD7KuNWcq5xUPgoWczChzdba5WLwQ4j","description":"Third account"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "index": 1
  }
}
```


### **change_wallet_password**

Change a wallet password.

Alias:  _None_.

Inputs:

-   _old_password_  - string; (Optional) Current wallet password, if defined.
-   _new_password_  - string; (Optional) New wallet password, if not blank.

Outputs:  _None_.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"change_wallet_password","params":{"old_password":"theCurrentSecretPassPhrase","new_password":"theNewSecretPassPhrase"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **check_reserve_proof**

Proves a wallet has a disposable reserve using a signature.

Alias:  _None_.

Inputs:

-   _address_  - string; Public address of the wallet.
-   _message_  - string; (Optional) Should be the same message used in  `get_reserve_proof`.
-   _signature_  - string; reserve signature to confirm.

Outputs:

-   _good_  - boolean; States if the inputs proves the reserve.

In the example below, the reserve has been proven:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_reserve_proof","params":{"address":"55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt","signature":"ReserveProofV11BZ23sBt9sZJeGccf84mzyAmNCP3KzYbE1111112VKmH111118NfCYJQjZ6c46gT2kXgcHCaSSZeL8sRdzqjqx7i1e7FQfQGu2o113UYFVdwzHQi3iENDPa76Kn1BvywbKz3bMkXdZkBEEhBSF4kjjGaiMJ1ucKb6wvMVC4A8sA4nZEdL2Mk3wBucJCYTZwKqA8i1M113kqakDkG25FrjiDqdQTCYz2wDBmfKxF3eQiV5FWzZ6HmAyxnqTWUiMWukP9A3Edy3ZXqjP1b23dhz7Mbj39bBxe3ZeDNu9HnTSqYvHNRyqCkeUMJpHyQweqjGUJ1DSfFYr33J1E7MkhMnEi1o7trqWjVix32XLetYfePG73yvHbS24837L7Q64i5n1LSpd9yMiQZ3Dyaysi5y6jPx7TpAvnSqBFtuCciKoNzaXoA3dqt9cuVFZTXzdXKqdt3cXcVJMNxY8RvKPVQHhUur94Lpo1nSpxf7BN5a5rHrbZFqoZszsZmiWikYPkLX72XUdw6NWjLrTBxSy7KuPYH86c6udPEXLo2xgN6XHMBMBJzt8FqqK7EcpNUBkuHm2AtpGkf9CABY3oSjDQoRF5n4vNLd3qUaxNsG4XJ12L9gJ7GrK273BxkfEA8fDdxPrb1gpespbgEnCTuZHqj1A"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "good": true,
    "spent": 0,
    "total": 100000000000
  }
}

```

In the example below, all wallet reserve has been proven:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_reserve_proof","params":{"address":"55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt","message":"I have 10 at least","signature":"...signature..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "good": true,
    "spent": 0,
    "total": 164113855714662789
  }
}

```

In the example below, the wrong message is used, avoiding the reserve to be proved:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_spend_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","message":"wrong message","signature":"SpendProofV1aSh8Todhk54736iXgV6vJAFP7egxByuMWZeyNDaN2JY737S95X5zz5mNMQSuCNSLjjhi5HJCsndpNWSNVsuThxwv285qy1KkUrLFRkxMSCjfL6bbycYN33ScZ5UB4Fzseceo1ndpL393T1q638VmcU3a56dhNHF1RPZFiGPS61FA78nXFSqE9uoKCCoHkEz83M1dQVhxZV5CEPF2P6VioGTKgprLCH9vvj9k1ivd4SX19L2VSMc3zD1u3mkR24ioETvxBoLeBSpxMoikyZ6inhuPm8yYo9YWyFtQK4XYfAV9mJ9knz5fUPXR8vvh7KJCAg4dqeJXTVb4mbMzYtsSZXHd6ouWoyCd6qMALdW8pKhgMCHcVYMWp9X9WHZuCo9rsRjRpg15sJUw7oJg1JoGiVgj8P4JeGDjnZHnmLVa5bpJhVCbMhyM7JLXNQJzFWTGC27TQBbthxCfQaKdusYnvZnKPDJWSeceYEFzepUnsWhQtyhbb73FzqgWC4eKEFKAZJqT2LuuSoxmihJ9acnFK7Ze23KTVYgDyMKY61VXADxmSrBvwUtxCaW4nQtnbMxiPMNnDMzeixqsFMBtN72j5UqhiLRY99k6SE7Qf5f29haNSBNSXCFFHChPKNTwJrehkofBdKUhh2VGPqZDNoefWUwfudeu83t85bmjv8Q3LrQSkFgFjRT5tLo8TMawNXoZCrQpyZrEvnodMDDUUNf3NL7rxyv3gM1KrTWjYaWXFU2RAsFee2Q2MTwUW7hR25cJvSFuB1BX2bfkoCbiMk923tHZGU2g7rSKF1GDDkXAc1EvFFD4iGbh1Q5t6hPRhBV8PEncdcCWGq5uAL5D4Bjr6VXG8uNeCy5oYWNgbZ5JRSfm7QEhPv8Fy9AKMgmCxDGMF9dVEaU6tw2BAnJavQdfrxChbDBeQXzCbCfep6oei6n2LZdE5Q84wp7eoQFE5Cwuo23tHkbJCaw2njFi3WGBbA7uGZaGHJPyB2rofTWBiSUXZnP2hiE9bjJghAcDm1M4LVLfWvhZmFEnyeru3VWMETnetz1BYLUC5MJGFXuhnHwWh7F6r74FDyhdswYop4eWPbyrXMXmUQEccTGd2NaT8g2VHADZ76gMC6BjWESvcnz2D4n8XwdmM7ZQ1jFwhuXrBfrb1dwRasyXxxHMGAC2onatNiExyeQ9G1W5LwqNLAh9hvcaNTGaYKYXoceVzLkgm6e5WMkLsCwuZXvB"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "good": false
  }
}

```


### **check_spend_proof**

Prove a spend using a signature. Unlike proving a transaction, it does not requires the destination public address.

Alias:  _None_.

Inputs:

-   _txid_  - string; transaction id.
-   _message_  - string; (Optional) Should be the same message used in  `get_spend_proof`.
-   _signature_  - string; spend signature to confirm.

Outputs:

-   _good_  - boolean; States if the inputs proves the spend.

In the example below, the spend has been proven:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_spend_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","message":"this is my transaction","signature":"SpendProofV1aSh8Todhk54736iXgV6vJAFP7egxByuMWZeyNDaN2JY737S95X5zz5mNMQSuCNSLjjhi5HJCsndpNWSNVsuThxwv285qy1KkUrLFRkxMSCjfL6bbycYN33ScZ5UB4Fzseceo1ndpL393T1q638VmcU3a56dhNHF1RPZFiGPS61FA78nXFSqE9uoKCCoHkEz83M1dQVhxZV5CEPF2P6VioGTKgprLCH9vvj9k1ivd4SX19L2VSMc3zD1u3mkR24ioETvxBoLeBSpxMoikyZ6inhuPm8yYo9YWyFtQK4XYfAV9mJ9knz5fUPXR8vvh7KJCAg4dqeJXTVb4mbMzYtsSZXHd6ouWoyCd6qMALdW8pKhgMCHcVYMWp9X9WHZuCo9rsRjRpg15sJUw7oJg1JoGiVgj8P4JeGDjnZHnmLVa5bpJhVCbMhyM7JLXNQJzFWTGC27TQBbthxCfQaKdusYnvZnKPDJWSeceYEFzepUnsWhQtyhbb73FzqgWC4eKEFKAZJqT2LuuSoxmihJ9acnFK7Ze23KTVYgDyMKY61VXADxmSrBvwUtxCaW4nQtnbMxiPMNnDMzeixqsFMBtN72j5UqhiLRY99k6SE7Qf5f29haNSBNSXCFFHChPKNTwJrehkofBdKUhh2VGPqZDNoefWUwfudeu83t85bmjv8Q3LrQSkFgFjRT5tLo8TMawNXoZCrQpyZrEvnodMDDUUNf3NL7rxyv3gM1KrTWjYaWXFU2RAsFee2Q2MTwUW7hR25cJvSFuB1BX2bfkoCbiMk923tHZGU2g7rSKF1GDDkXAc1EvFFD4iGbh1Q5t6hPRhBV8PEncdcCWGq5uAL5D4Bjr6VXG8uNeCy5oYWNgbZ5JRSfm7QEhPv8Fy9AKMgmCxDGMF9dVEaU6tw2BAnJavQdfrxChbDBeQXzCbCfep6oei6n2LZdE5Q84wp7eoQFE5Cwuo23tHkbJCaw2njFi3WGBbA7uGZaGHJPyB2rofTWBiSUXZnP2hiE9bjJghAcDm1M4LVLfWvhZmFEnyeru3VWMETnetz1BYLUC5MJGFXuhnHwWh7F6r74FDyhdswYop4eWPbyrXMXmUQEccTGd2NaT8g2VHADZ76gMC6BjWESvcnz2D4n8XwdmM7ZQ1jFwhuXrBfrb1dwRasyXxxHMGAC2onatNiExyeQ9G1W5LwqNLAh9hvcaNTGaYKYXoceVzLkgm6e5WMkLsCwuZXvB"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "good": true
  }
}

```

In the example below, the wrong message is used, avoiding the spend to be proved:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_spend_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","message":"wrong message","signature":"SpendProofV1aSh8Todhk54736iXgV6vJAFP7egxByuMWZeyNDaN2JY737S95X5zz5mNMQSuCNSLjjhi5HJCsndpNWSNVsuThxwv285qy1KkUrLFRkxMSCjfL6bbycYN33ScZ5UB4Fzseceo1ndpL393T1q638VmcU3a56dhNHF1RPZFiGPS61FA78nXFSqE9uoKCCoHkEz83M1dQVhxZV5CEPF2P6VioGTKgprLCH9vvj9k1ivd4SX19L2VSMc3zD1u3mkR24ioETvxBoLeBSpxMoikyZ6inhuPm8yYo9YWyFtQK4XYfAV9mJ9knz5fUPXR8vvh7KJCAg4dqeJXTVb4mbMzYtsSZXHd6ouWoyCd6qMALdW8pKhgMCHcVYMWp9X9WHZuCo9rsRjRpg15sJUw7oJg1JoGiVgj8P4JeGDjnZHnmLVa5bpJhVCbMhyM7JLXNQJzFWTGC27TQBbthxCfQaKdusYnvZnKPDJWSeceYEFzepUnsWhQtyhbb73FzqgWC4eKEFKAZJqT2LuuSoxmihJ9acnFK7Ze23KTVYgDyMKY61VXADxmSrBvwUtxCaW4nQtnbMxiPMNnDMzeixqsFMBtN72j5UqhiLRY99k6SE7Qf5f29haNSBNSXCFFHChPKNTwJrehkofBdKUhh2VGPqZDNoefWUwfudeu83t85bmjv8Q3LrQSkFgFjRT5tLo8TMawNXoZCrQpyZrEvnodMDDUUNf3NL7rxyv3gM1KrTWjYaWXFU2RAsFee2Q2MTwUW7hR25cJvSFuB1BX2bfkoCbiMk923tHZGU2g7rSKF1GDDkXAc1EvFFD4iGbh1Q5t6hPRhBV8PEncdcCWGq5uAL5D4Bjr6VXG8uNeCy5oYWNgbZ5JRSfm7QEhPv8Fy9AKMgmCxDGMF9dVEaU6tw2BAnJavQdfrxChbDBeQXzCbCfep6oei6n2LZdE5Q84wp7eoQFE5Cwuo23tHkbJCaw2njFi3WGBbA7uGZaGHJPyB2rofTWBiSUXZnP2hiE9bjJghAcDm1M4LVLfWvhZmFEnyeru3VWMETnetz1BYLUC5MJGFXuhnHwWh7F6r74FDyhdswYop4eWPbyrXMXmUQEccTGd2NaT8g2VHADZ76gMC6BjWESvcnz2D4n8XwdmM7ZQ1jFwhuXrBfrb1dwRasyXxxHMGAC2onatNiExyeQ9G1W5LwqNLAh9hvcaNTGaYKYXoceVzLkgm6e5WMkLsCwuZXvB"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "good": false
  }
}

```


### **check_tx_key**

Check a transaction in the blockchain with its secret key.

Alias:  _None_.

Inputs:

-   _txid_  - string; transaction id.
-   _tx_key_  - string; transaction secret key.
-   _address_  - string; destination public address of the transaction.

Outputs:

-   _confirmations_  - unsigned int; Number of block mined after the one with the transaction.
-   _in_pool_  - boolean; States if the transaction is still in pool or has been added to a block.
-   _received_  - unsigned int; Amount of the transaction.

Example:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_tx_key","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","tx_key":"feba662cf8fb6d0d0da18fc9b70ab28e01cc76311278fdd7fe7ab16360762b06","address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "confirmations": 0,
    "in_pool": false,
    "received": 1000000000000
  }
}

```


### **check_tx_proof**

Prove a transaction by checking its signature.

Alias:  _None_.

Inputs:

-   _txid_  - string; transaction id.
-   _address_  - string; destination public address of the transaction.
-   _message_  - string; (Optional) Should be the same message used in  `get_tx_proof`.
-   _signature_  - string; transaction signature to confirm.

Outputs:

-   _confirmations_  - unsigned int; Number of block mined after the one with the transaction.
-   _good_  - boolean; States if the inputs proves the transaction.
-   _in_pool_  - boolean; States if the transaction is still in pool or has been added to a block.
-   _received_  - unsigned int; Amount of the transaction.

In the example below, the transaction has been proven:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_tx_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o","message":"this is my transaction","signature":"InProofV13vqBCT6dpSAXkypZmSEMPGVnNRFDX2vscUYeVS4WnSVnV5BwLs31T9q6Etfj9Wts6tAxSAS4gkMeSYzzLS7Gt4vvCSQRh9niGJMUDJsB5hTzb2XJiCkUzWkkcjLFBBRVD5QZ"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "confirmations": 482,
    "good": true,
    "in_pool": false,
    "received": 1000000000000
  }
}

```

In the example below, the wrong message is used, avoiding the transaction to be proved:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_tx_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o","message":"wrong message","signature":"InProofV13vqBCT6dpSAXkypZmSEMPGVnNRFDX2vscUYeVS4WnSVnV5BwLs31T9q6Etfj9Wts6tAxSAS4gkMeSYzzLS7Gt4vvCSQRh9niGJMUDJsB5hTzb2XJiCkUzWkkcjLFBBRVD5QZ"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "confirmations": 0,
    "good": false,
    "in_pool": false,
    "received": 0
  }
}

```


### **close_wallet**

Close the currently opened wallet, after trying to save it.

Alias:  _None_.

Inputs:  _None_.

Outputs:  _None_.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"close_wallet"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **create_account**

Create a new account with an optional label.

Alias:  _None_.

Inputs:

-   _label_  - string; (Optional) Label for the account.

Outputs:

-   _account_index_  - unsigned int; Index of the new account.
-   _address_  - string; Address for this account. Base58 representation of the public keys.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"create_account","params":{"label":"Secondary account"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "account_index": 1,
    "address": "77Vx9cs1VPicFndSVgYUvTdLCJEZw9h81hXLMYsjBCXSJfUehLa9TDW3Ffh45SQa7xb6dUs18mpNxfUhQGqfwXPSMrvKhVp"
  }
}

```


### **create_address**

Create a new address for an account. Optionally, label the new address.

Alias:  _None_.

Inputs:

-   _account_index_  - unsigned int; Create a new address for this account.
-   _label_  - string; (Optional) Label for the new address.

Outputs:

-   _address_  - string; Newly created address. Base58 representation of the public keys.
-   _address_index_  - unsigned int; Index of the new address under the input account.

Example:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"create_address","params":{"account_index":0,"label":"new-sub"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "address": "7BG5jr9QS5sGMdpbBrZEwVLZjSKJGJBsXdZLt8wiXyhhLjy7x2LZxsrAnHTgD8oG46ZtLjUGic2pWc96GFkGNPQQDA3Dt7Q",
    "address_index": 5
  }
}

```


### **create_wallet**

Create a new wallet. You need to have set the argument "–wallet-dir" when launching monero-wallet-rpc to make this work.

Alias:  _None_.

Inputs:

-   _filename_  - string; Wallet file name.
-   _password_  - string; (Optional) password to protect the wallet.
-   _language_  - string; Language for your wallets' seed.

Outputs:  _None_.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"create_wallet","params":{"filename":"mytestwallet","password":"mytestpassword","language":"English"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **delete_address_book**

Delete an entry from the address book.

Alias:  _None_.

Inputs:

-   _index_  - unsigned int; The index of the address book entry.

Outputs:  _None_.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"delete_address_book","params":{"index":1}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **export_key_images**

Export a signed set of key images.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _signed_key_images_  - array of signed key images:
    -   _key_image_  - string;
    -   _signature_  - string;

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"export_key_images"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "signed_key_images": [{
      "key_image": "cd35239b72a35e26a57ed17400c0b66944a55de9d5bda0f21190fed17f8ea876",
      "signature": "c9d736869355da2538ab4af188279f84138c958edbae3c5caf388a63cd8e780b8c5a1aed850bd79657df659422c463608ea4e0c730ba9b662c906ae933816d00"
    },{
      "key_image": "65158a8ee5a3b32009b85a307d85b375175870e560e08de313531c7dbbe6fc19",
      "signature": "c96e40d09dfc45cfc5ed0b76bfd7ca793469588bb0cf2b4d7b45ef23d40fd4036057b397828062e31700dc0c2da364f50cd142295a8405b9fe97418b4b745d0c"
    },...]
  }
}

```


### **export_multisig_info**

Export multisig info for other participants.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _info_  - string; Multisig info in hex format for other participants.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"export_multisig_info"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "info": "4d6f6e65726f206d756c7469736967206578706f72740105cf6442b09b75f5eca9d846771fe1a879c9a97ab0553ffbcec64b1148eb7832b51e7898d7944c41cee000415c5a98f4f80dc0efdae379a98805bb6eacae743446f6f421cd03e129eb5b27d6e3b73eb6929201507c1ae706c1a9ecd26ac8601932415b0b6f49cbbfd712e47d01262c59980a8f9a8be776f2bf585f1477a6df63d6364614d941ecfdcb6e958a390eb9aa7c87f056673d73bc7c5f0ab1f74a682e902e48a3322c0413bb7f6fd67404f13fb8e313f70a0ce568c853206751a334ef490068d3c8ca0e"
  }
}

```


### **export_outputs**

Export all outputs in hex format.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _outputs_data_hex_  - string; wallet outputs in hex format.

Example:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"export_outputs"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "outputs_data_hex": "...outputs..."
  }
}

```


### **finalize_multisig**

Turn this wallet into a multisig wallet, extra step for N-1/N wallets.

Alias:  _None_.

Inputs:

-   _multisig_info_  - array of string; List of multisig string from peers.
-   _password_  - string; Wallet password

Outputs:

-   _address_  - string; multisig wallet address.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"finalize_multisig","params":{"multisig_info":["MultisigxV1JNC6Ja2oBt5Sqea9LN2YEF7WYZCpHqr2EKvPG89Trf3X4E8RWkLaGRf29fJ3stU471MELKxwufNYeigP7LoE4tn2McPr4SbL9q15xNvZT5uwC9YRr7UwjXqSZHmTWN9PBuZEKVAQ4HPPyQciSCdNjgwsuFRBzrskMdMUwNMgKst1debYfm37i6PSzDoS2tk4kYTYj83kkAdR7kdshet1axQPd6HQ","MultisigxV1Unma7Ko4zdd8Ps3Af4oZwtj2JdWKzwNfP6s2G9ZvXhMoSscwn5g7PyCfcBc1V4ffRHY3Kxqq6VocSCUTncpVeUskMcPr4SbL9q15xNvZT5uwC9YRr7UwjXqSZHmTWN9PBuZE1LTpWxLoC3vPMSrqVVcjnmL9LYfdCZz3fECjNZbCEDq3PHDiUuY5jurQTcNoGhDTio5WM9xaAdim9YByiS5KyqF4"]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "address": "5B9gZUTDuHTcGGuY3nL3t8K2tDnEHeRVHSBQgLZUTQxtFYVLnho5JJjWJyFp5YZgZRQ44RiviJi1sPHgLVMbckRsDqDx1gV"
  }
}

```


### **get_account_tags**

Get a list of user-defined account tags.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _account_tags_  - array of account tag information:
    -   _tag_  - string; Filter tag.
    -   _label_  - string; Label for the tag.
    -   _accounts_  - array of int; List of tagged account indices.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_account_tags","params":""}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "account_tags": [{
      "accounts": [0],
      "label": "Test tag",
      "tag": "myTag"
    }]
  }
}

```

### **get_accounts**

Get all accounts for a wallet. Optionally filter accounts by tag.

Alias:  _None_.

Inputs:

-   _tag_  - string; (Optional) Tag for filtering accounts.

Outputs:

-   _subaddress_accounts_  - array of subaddress account information:
    -   _account_index_  - unsigned int; Index of the account.
    -   _balance_  - unsigned int; Balance of the account (locked or unlocked).
    -   _base_address_  - string; Base64 representation of the first subaddress in the account.
    -   _label_  - string; (Optional) Label of the account.
    -   _tag_  - string; (Optional) Tag for filtering accounts.
    -   _unlocked_balance_  - unsigned int; Unlocked balance for the account.
-   _total_balance_  - unsigned int; Total balance of the selected accounts (locked or unlocked).
-   _total_unlocked_balance_  - unsigned int; Total unlocked balance of the selected accounts.

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_accounts","params":{"tag":"myTag"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "subaddress_accounts": [{
      "account_index": 0,
      "balance": 157663195572433688,
      "base_address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
      "label": "Primary account",
      "tag": "myTag",
      "unlocked_balance": 157443303037455077
    },{
      "account_index": 1,
      "balance": 0,
      "base_address": "77Vx9cs1VPicFndSVgYUvTdLCJEZw9h81hXLMYsjBCXSJfUehLa9TDW3Ffh45SQa7xb6dUs18mpNxfUhQGqfwXPSMrvKhVp",
      "label": "Secondary account",
      "tag": "myTag",
      "unlocked_balance": 0
    }],
    "total_balance": 157663195572433688,
    "total_unlocked_balance": 157443303037455077
  }
}

```


### **get_address**

Return the wallet's addresses for an account. Optionally filter for specific set of subaddresses.

Alias:  _getaddress_.

Inputs:

-   _account_index_  - unsigned int; Return subaddresses for this account.
-   _address_index_  - array of unsigned int; (Optional) List of subaddresses to return from an account.

Outputs:

-   _address_  - string; The 95-character hex address string of the monero-wallet-rpc in session.
-   _addresses_  array of addresses informations
    -   _address_  string; The 95-character hex (sub)address string.
    -   _label_  string; Label of the (sub)address
    -   _address_index_  unsigned int; index of the subaddress
    -   _used_  boolean; states if the (sub)address has already received funds

Example:

``` Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_address","params":{"account_index":0,"address_index":[0,1,4]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
    "addresses": [{
      "address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
      "address_index": 0,
      "label": "Primary account",
      "used": true
    },{
      "address": "7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o",
      "address_index": 1,
      "label": "",
      "used": true
    },{
      "address": "77xa6Dha7kzCQuvmd8iB5VYoMkdenwCNRU9khGhExXQ8KLL3z1N1ZATBD1sFPenyHWT9cm4fVFnCAUApY53peuoZFtwZiw5",
      "address_index": 4,
      "label": "test2",
      "used": true
    }]
  }
}

```


### **get_address_book**

Retrieves entries from the address book.

Alias:  _None_.

Inputs:

-   _entries_  - array of unsigned int; indices of the requested address book entries

Outputs:

-   _entries_  - array of entries:
    -   _address_  - string; Public address of the entry
    -   _description_  - string; Description of this address entry
    -   _index_  - unsigned int;
    -   _payment_id_  - string;

Example:

``` Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_address_book","params":{"entries":[0,1]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "entries": [{
      "address": "77Vx9cs1VPicFndSVgYUvTdLCJEZw9h81hXLMYsjBCXSJfUehLa9TDW3Ffh45SQa7xb6dUs18mpNxfUhQGqfwXPSMrvKhVp",
      "description": "Second account",
      "index": 0,
      "payment_id": "0000000000000000000000000000000000000000000000000000000000000000"
    },{
      "address": "78P16M3XmFRGcWFCcsgt1WcTntA1jzcq31seQX1Eg92j8VQ99NPivmdKam4J5CKNAD7KuNWcq5xUPgoWczChzdba5WLwQ4j",
      "description": "Third account",
      "index": 1,
      "payment_id": "0000000000000000000000000000000000000000000000000000000000000000"
    }]
  }
}

```


### **get_address_index**

Get account and address indexes from a specific (sub)address

Alias:  _None_.

Inputs:

-   _address_  - String; (sub)address to look for.

Outputs:

-   _index_  - subaddress informations
    -   _major_  unsigned int; Account index.
    -   _minor_  unsigned int; Address index.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_address_index","params":{"address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "index": {
      "major": 0,
      "minor": 1
    }
  }
}
```

### **get_attribute**

Get attribute value by name.

Alias:  _None_.

Inputs:

-   _key_  - string; attribute name

Outputs:

-   _value_  - string; attribute value

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_attribute","params":{"key":"my_attribute"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "value": "my_value"
  }
}
```

### **get_bulk_payments**

Get a list of incoming payments using a given payment id, or a list of payments ids, from a given height. This method is the preferred method over  `get_payments`because it has the same functionality but is more extendable. Either is fine for looking up transactions by a single payment ID.

Alias:  _None_.

Inputs:

-   _payment_ids_  - array of: string; Payment IDs used to find the payments (16 characters hex).
-   _min_block_height_  - unsigned int; The block height at which to start looking for payments.

Outputs:

-   _payments_  - list of:
    -   _payment_id_  - string; Payment ID matching one of the input IDs.
    -   _tx_hash_  - string; Transaction hash used as the transaction ID.
    -   _amount_  - unsigned int; Amount for this payment.
    -   _block_height_  - unsigned int; Height of the block that first confirmed this payment.
    -   _unlock_time_  - unsigned int; Time (in block height) until this payment is safe to spend.
    -   _subaddr_index_  - subaddress index:
        -   _major_  - unsigned int; Account index for the subaddress.
        -   _minor_  - unsigned int; Index of the subaddress in the account.
    -   _address_  - string; Address receiving the payment; Base58 representation of the public keys.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_bulk_payments","params":{"payment_ids":["60900e5603bf96e3"],"min_block_height":"120000"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "payments": [{
      "address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
      "amount": 1000000000000,
      "block_height": 127606,
      "payment_id": "60900e5603bf96e3",
      "subaddr_index": {
        "major": 0,
        "minor": 0
      },
      "tx_hash": "3292e83ad28fc1cc7bc26dbd38862308f4588680fbf93eae3e803cddd1bd614f",
      "unlock_time": 0
    }]
  }
}
```

### **get_height**

Returns the wallet's current block height.

Alias:  _getheight_.

Inputs:  _None_.

Outputs:

-   _height_  - unsigned int; The current monero-wallet-rpc's blockchain height. If the wallet has been offline for a long time, it may need to catch up with the daemon.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_height"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "height": 145545
  }
}
```

### **get_languages**

Get a list of available languages for your wallet's seed.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _languages_  - array of string; List of available languages

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_languages"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "languages": ["Deutsch","English","Español","Français","Italiano","Nederlands","Português","русский язык","日本語","简体中文 (中国)","Esperanto","Lojban"]
  }
}
```

### **get_payments**

Get a list of incoming payments using a given payment id.

Alias:  _None_.

Inputs:

-   _payment_id_  - string; Payment ID used to find the payments (16 characters hex).

Outputs:

-   _payments_  - list of:
    -   _payment_id_  - string; Payment ID matching the input parameter.
    -   _tx_hash_  - string; Transaction hash used as the transaction ID.
    -   _amount_  - unsigned int; Amount for this payment.
    -   _block_height_  - unsigned int; Height of the block that first confirmed this payment.
    -   _unlock_time_  - unsigned int; Time (in block height) until this payment is safe to spend.
    -   _subaddr_index_  - subaddress index:
        -   _major_  - unsigned int; Account index for the subaddress.
        -   _minor_  - unsigned int; Index of the subaddress in the account.
    -   _address_  - string; Address receiving the payment; Base58 representation of the public keys.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_payments","params":{"payment_id":"60900e5603bf96e3"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "payments": [{
      "address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
      "amount": 1000000000000,
      "block_height": 127606,
      "payment_id": "60900e5603bf96e3",
      "subaddr_index": {
        "major": 0,
        "minor": 0
      },
      "tx_hash": "3292e83ad28fc1cc7bc26dbd38862308f4588680fbf93eae3e803cddd1bd614f",
      "unlock_time": 0
    }]
  }
}
```

### **get_reserve_proof**

Generate a signature to prove of an available amount in a wallet.

Alias:  _None_.

Inputs:

-   _all_  - boolean; Proves all wallet balance to be disposable.
-   _account_index_  - unsigned int; Specify the account from witch to prove reserve. (ignored if  `all`  is set to true)
-   _amount_  - unsigned int; Amount (in  atomic units) to prove the account has for reserve. (ignored if  `all`  is set to true)
-   _message_  - string; (Optional) add a message to the signature to further authenticate the prooving process.

Outputs:

-   _signature_  - string; reserve signature.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_reserve_proof","params":{"all":false,"account_index":0,"amount":100000000000}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "signature": "ReserveProofV11BZ23sBt9sZJeGccf84mzyAmNCP3KzYbE1111112VKmH111118NfCYJQjZ6c46gT2kXgcHCaSSZeL8sRdzqjqx7i1e7FQfQGu2o113UYFVdwzHQi3iENDPa76Kn1BvywbKz3bMkXdZkBEEhBSF4kjjGaiMJ1ucKb6wvMVC4A8sA4nZEdL2Mk3wBucJCYTZwKqA8i1M113kqakDkG25FrjiDqdQTCYz2wDBmfKxF3eQiV5FWzZ6HmAyxnqTWUiMWukP9A3Edy3ZXqjP1b23dhz7Mbj39bBxe3ZeDNu9HnTSqYvHNRyqCkeUMJpHyQweqjGUJ1DSfFYr33J1E7MkhMnEi1o7trqWjVix32XLetYfePG73yvHbS24837L7Q64i5n1LSpd9yMiQZ3Dyaysi5y6jPx7TpAvnSqBFtuCciKoNzaXoA3dqt9cuVFZTXzdXKqdt3cXcVJMNxY8RvKPVQHhUur94Lpo1nSpxf7BN5a5rHrbZFqoZszsZmiWikYPkLX72XUdw6NWjLrTBxSy7KuPYH86c6udPEXLo2xgN6XHMBMBJzt8FqqK7EcpNUBkuHm2AtpGkf9CABY3oSjDQoRF5n4vNLd3qUaxNsG4XJ12L9gJ7GrK273BxkfEA8fDdxPrb1gpespbgEnCTuZHqj1A"
  }
}
```

### **get_spend_proof**

Generate a signature to prove a spend. Unlike proving a transaction, it does not requires the destination public address.

Alias:  _None_.

Inputs:

-   _txid_  - string; transaction id.
-   _message_  - string; (Optional) add a message to the signature to further authenticate the prooving process.

Outputs:

-   _signature_  - string; spend signature.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_spend_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","message":"this is my transaction"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "signature": "SpendProofV1aSh8Todhk54736iXgV6vJAFP7egxByuMWZeyNDaN2JY737S95X5zz5mNMQSuCNSLjjhi5HJCsndpNWSNVsuThxwv285qy1KkUrLFRkxMSCjfL6bbycYN33ScZ5UB4Fzseceo1ndpL393T1q638VmcU3a56dhNHF1RPZFiGPS61FA78nXFSqE9uoKCCoHkEz83M1dQVhxZV5CEPF2P6VioGTKgprLCH9vvj9k1ivd4SX19L2VSMc3zD1u3mkR24ioETvxBoLeBSpxMoikyZ6inhuPm8yYo9YWyFtQK4XYfAV9mJ9knz5fUPXR8vvh7KJCAg4dqeJXTVb4mbMzYtsSZXHd6ouWoyCd6qMALdW8pKhgMCHcVYMWp9X9WHZuCo9rsRjRpg15sJUw7oJg1JoGiVgj8P4JeGDjnZHnmLVa5bpJhVCbMhyM7JLXNQJzFWTGC27TQBbthxCfQaKdusYnvZnKPDJWSeceYEFzepUnsWhQtyhbb73FzqgWC4eKEFKAZJqT2LuuSoxmihJ9acnFK7Ze23KTVYgDyMKY61VXADxmSrBvwUtxCaW4nQtnbMxiPMNnDMzeixqsFMBtN72j5UqhiLRY99k6SE7Qf5f29haNSBNSXCFFHChPKNTwJrehkofBdKUhh2VGPqZDNoefWUwfudeu83t85bmjv8Q3LrQSkFgFjRT5tLo8TMawNXoZCrQpyZrEvnodMDDUUNf3NL7rxyv3gM1KrTWjYaWXFU2RAsFee2Q2MTwUW7hR25cJvSFuB1BX2bfkoCbiMk923tHZGU2g7rSKF1GDDkXAc1EvFFD4iGbh1Q5t6hPRhBV8PEncdcCWGq5uAL5D4Bjr6VXG8uNeCy5oYWNgbZ5JRSfm7QEhPv8Fy9AKMgmCxDGMF9dVEaU6tw2BAnJavQdfrxChbDBeQXzCbCfep6oei6n2LZdE5Q84wp7eoQFE5Cwuo23tHkbJCaw2njFi3WGBbA7uGZaGHJPyB2rofTWBiSUXZnP2hiE9bjJghAcDm1M4LVLfWvhZmFEnyeru3VWMETnetz1BYLUC5MJGFXuhnHwWh7F6r74FDyhdswYop4eWPbyrXMXmUQEccTGd2NaT8g2VHADZ76gMC6BjWESvcnz2D4n8XwdmM7ZQ1jFwhuXrBfrb1dwRasyXxxHMGAC2onatNiExyeQ9G1W5LwqNLAh9hvcaNTGaYKYXoceVzLkgm6e5WMkLsCwuZXvB"
  }
}
```

### **get_transfer_by_txid**

Show information about a transfer to/from this address.

Alias:  _None_.

Inputs:

-   _txid_  - string; Transaction ID used to find the transfer.
-   _account_index_  - unsigned int; (Optional) Index of the account to query for the transfer.

Outputs:

-   _transfer_  - JSON object containing payment information:
    -   _address_  - string; Address that transferred the funds. Base58 representation of the public keys.
    -   _amount_  - unsigned int; Amount of this transfer.
    -   _confirmations_  - unsigned int; Number of block mined since the block containing this transaction (or block height at which the transaction should be added to a block if not yet confirmed).
    -   _destinations_  - array of JSON objects containing transfer destinations:
        -   _amount_  - unsigned int; Amount transferred to this destination.
        -   _address_  - string; Address for this destination. Base58 representation of the public keys.
    -   _double_spend_seen_  - boolean; True if the key image(s) for the transfer have been seen before.
    -   _fee_  - unsigned int; Transaction fee for this transfer.
    -   _height_  - unsigned int; Height of the first block that confirmed this transfer.
    -   _note_  - string; Note about this transfer.
    -   _payment_id_  - string; Payment ID for this transfer.
    -   _subaddr_index_  - JSON object containing the major & minor subaddress index:
        -   _major_  - unsigned int; Account index for the subaddress.
        -   _minor_  - unsigned int; Index of the subaddress under the account.
    -   _suggested_confirmations_threshold_  - unsigned int; Estimation of the confirmations needed for the transaction to be included in a block.
    -   _timestamp_  - unsigned int; POSIX timestamp for the block that confirmed this transfer (or timestamp submission if not mined yet).
    -   _txid_  - string; Transaction ID of this transfer (same as input TXID).
    -   _type_  - string; Type of transfer, one of the following: "in", "out", "pending", "failed", "pool"
    -   _unlock_time_  - unsigned int; Number of blocks until transfer is safely spendable.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_transfer_by_txid","params":{"txid":"c36258a276018c3a4bc1f195a7fb530f50cd63a4fa765fb7c6f7f49fc051762a"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "transfer": {
      "address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
      "amount": 300000000000,
      "confirmations": 1,
      "destinations": [{
        "address": "7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o",
        "amount": 100000000000
      },{
        "address": "77Vx9cs1VPicFndSVgYUvTdLCJEZw9h81hXLMYsjBCXSJfUehLa9TDW3Ffh45SQa7xb6dUs18mpNxfUhQGqfwXPSMrvKhVp",
        "amount": 200000000000
      }],
      "double_spend_seen": false,
      "fee": 21650200000,
      "height": 153624,
      "note": "",
      "payment_id": "0000000000000000",
      "subaddr_index": {
        "major": 0,
        "minor": 0
      },
      "suggested_confirmations_threshold": 1,
      "timestamp": 1535918400,
      "txid": "c36258a276018c3a4bc1f195a7fb530f50cd63a4fa765fb7c6f7f49fc051762a",
      "type": "out",
      "unlock_time": 0
    }
  }
}
```

### **get_transfers**

Returns a list of transfers.

Alias:  _None_.

Inputs:

-   _in_  - boolean; (Optional) Include incoming transfers.
-   _out_  - boolean; (Optional) Include outgoing transfers.
-   _pending_  - boolean; (Optional) Include pending transfers.
-   _failed_  - boolean; (Optional) Include failed transfers.
-   _pool_  - boolean; (Optional) Include transfers from the daemon's transaction pool.
-   _filter_by_height_  - boolean; (Optional) Filter transfers by block height.
-   _min_height_  - unsigned int; (Optional) Minimum block height to scan for transfers, if filtering by height is enabled.
-   _max_height_  - unsigned int; (Optional) Maximum block height to scan for transfers, if filtering by height is enabled (defaults to max block height).
-   _account_index_  - unsigned int; (Optional) Index of the account to query for transfers. (defaults to 0)
-   _subaddr_indices_  - array of unsigned int; (Optional) List of subaddress indices to query for transfers. (defaults to 0)

Outputs:

-   _in_  array of transfers:
    -   _address_  - string; Public address of the transfer.
    -   _amount_  - unsigned int; Amount transferred.
    -   _confirmations_  - unsigned int; Number of block mined since the block containing this transaction (or block height at which the transaction should be added to a block if not yet confirmed).
    -   _double_spend_seen_  - boolean; True if the key image(s) for the transfer have been seen before.
    -   _fee_  - unsigned int; Transaction fee for this transfer.
    -   _height_  - unsigned int; Height of the first block that confirmed this transfer (0 if not mined yet).
    -   _note_  - string; Note about this transfer.
    -   _payment_id_  - string; Payment ID for this transfer.
    -   _subaddr_index_  - JSON object containing the major & minor subaddress index:
        -   _major_  - unsigned int; Account index for the subaddress.
        -   _minor_  - unsigned int; Index of the subaddress under the account.
    -   _suggested_confirmations_threshold_  - unsigned int; Estimation of the confirmations needed for the transaction to be included in a block.
    -   _timestamp_  - unsigned int; POSIX timestamp for when this transfer was first confirmed in a block (or timestamp submission if not mined yet).
    -   _txid_  - string; Transaction ID for this transfer.
    -   _type_  - string; Transfer type: "in"
    -   _unlock_time_  - unsigned int; Number of blocks until transfer is safely spendable.
-   _out_  array of transfers (see above).
-   _pending_  array of transfers (see above).
-   _failed_  array of transfers (see above).
-   _pool_  array of transfers (see above).

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_transfers","params":{"in":true,"account_index":1}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "in": [{
      "address": "77Vx9cs1VPicFndSVgYUvTdLCJEZw9h81hXLMYsjBCXSJfUehLa9TDW3Ffh45SQa7xb6dUs18mpNxfUhQGqfwXPSMrvKhVp",
      "amount": 200000000000,
      "confirmations": 1,
      "double_spend_seen": false,
      "fee": 21650200000,
      "height": 153624,
      "note": "",
      "payment_id": "0000000000000000",
      "subaddr_index": {
        "major": 1,
        "minor": 0
      },
      "suggested_confirmations_threshold": 1,
      "timestamp": 1535918400,
      "txid": "c36258a276018c3a4bc1f195a7fb530f50cd63a4fa765fb7c6f7f49fc051762a",
      "type": "in",
      "unlock_time": 0
    }]
  }
}
```

### **get_tx_key**

Get transaction secret key from transaction id.

Alias:  _None_.

Inputs:

-   _txid_  - string; transaction id.

Outputs:

-   _tx_key_  - string; transaction secret key.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_tx_key","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_key": "feba662cf8fb6d0d0da18fc9b70ab28e01cc76311278fdd7fe7ab16360762b06"
  }
}
```

### **get_tx_notes**

Get string notes for transactions.

Alias:  _None_.

Inputs:

-   _txids_  - array of string; transaction ids

Outputs:

-   _notes_  - array of string; notes for the transactions

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_tx_notes","params":{"txids":["3292e83ad28fc1cc7bc26dbd38862308f4588680fbf93eae3e803cddd1bd614f"]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "notes": ["This is an example"]
  }
}
```

### **get_tx_proof**

Get transaction signature to prove it.

Alias:  _None_.

Inputs:

-   _txid_  - string; transaction id.
-   _address_  - string; destination public address of the transaction.
-   _message_  - string; (Optional) add a message to the signature to further authenticate the prooving process.

Outputs:

-   _signature_  - string; transaction signature.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_tx_proof","params":{"txid":"19d5089f9469db3d90aca9024dfcb17ce94b948300101c8345a5e9f7257353be","address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o","message":"this is my transaction"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "signature": "InProofV13vqBCT6dpSAXkypZmSEMPGVnNRFDX2vscUYeVS4WnSVnV5BwLs31T9q6Etfj9Wts6tAxSAS4gkMeSYzzLS7Gt4vvCSQRh9niGJMUDJsB5hTzb2XJiCkUzWkkcjLFBBRVD5QZ"
  }
}
```

### **get_version**

Get RPC version Major & Minor integer-format, where Major is the first 16 bits and Minor the last 16 bits.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _version_  - unsigned int; RPC version, formatted with  `Major * 2^16 + Minor`(Major encoded over the first 16 bits, and Minor over the last 16 bits).

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_version"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "version": 65539
  }
}
```

### **import_key_images**

Import signed key images list and verify their spent status.

Alias:  _None_.

Inputs:

-   _signed_key_images_  - array of signed key images:
    -   _key_image_  - string;
    -   _signature_  - string;

Outputs:

-   _height_  - unsigned int;
-   _spent_  - unsigned int; Amount (in  atomic units) spent from those key images.
-   _unspent_  - unsigned int; Amount (in  atomic units) still available from those key images.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"import_key_images", "params":{"signed_key_images":[{"key_image":"cd35239b72a35e26a57ed17400c0b66944a55de9d5bda0f21190fed17f8ea876","signature":"c9d736869355da2538ab4af188279f84138c958edbae3c5caf388a63cd8e780b8c5a1aed850bd79657df659422c463608ea4e0c730ba9b662c906ae933816d00"},{"key_image":"65158a8ee5a3b32009b85a307d85b375175870e560e08de313531c7dbbe6fc19","signature":"c96e40d09dfc45cfc5ed0b76bfd7ca793469588bb0cf2b4d7b45ef23d40fd4036057b397828062e31700dc0c2da364f50cd142295a8405b9fe97418b4b745d0c"}]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "height": 76428,
    "spent": 62708953408711,
    "unspent": 0
  }
}
```

### **import_multisig_info**

Import multisig info from other participants.

Alias:  _None_.

Inputs:

-   _info_  - array of string; List of multisig info in hex format from other participants.

Outputs:

-   _n_outputs_  - unsigned int; Number of outputs signed with those multisig info.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"import_multisig_info","params":{"info":["...multisig_info..."]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "n_outputs": 35
  }
}
```

### **import_outputs**

Import outputs in hex format.

Alias:  _None_.

Inputs:

-   _outputs_data_hex_  - string; wallet outputs in hex format.

Outputs:

-   _num_imported_  - unsigned int; number of outputs imported.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"import_outputs","params":{"outputs_data_hex":"...outputs..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "num_imported": 6400
  }
}
```

### **incoming_transfers**

Return a list of incoming transfers to the wallet.

Inputs:

-   _transfer_type_  - string; "all": all the transfers, "available": only transfers which are not yet spent, OR "unavailable": only transfers which are already spent.
-   _account_index_  - unsigned int; (Optional) Return transfers for this account. (defaults to 0)
-   _subaddr_indices_  - array of unsigned int; (Optional) Return transfers sent to these subaddresses.
-   _verbose_  - boolean; (Optional) Enable verbose output, return key image if true.

Outputs:

-   _transfers_  - list of:
    -   _amount_  - unsigned int; Amount of this transfer.
    -   _global_index_  - unsigned int; Mostly internal use, can be ignored by most users.
    -   _key_image_  - string; Key image for the incoming transfer's unspent output (empty unless verbose is true).
    -   _spent_  - boolean; Indicates if this transfer has been spent.
    -   _subaddr_index_  - unsigned int; Subaddress index for incoming transfer.
    -   _tx_hash_  - string; Several incoming transfers may share the same hash if they were in the same transaction.
    -   _tx_size_  - unsigned int; Size of transaction in bytes.

Example, get all transfers:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"incoming_transfers","params":{"transfer_type":"all","account_index":0,"subaddr_indices":[3],"verbose":true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "transfers": [{
      "amount": 60000000000000,
      "global_index": 122405,
      "key_image": "768f5144777eb23477ab7acf83562581d690abaf98ca897c03a9d2b900eb479b",
      "spent": true,
      "subaddr_index": 3,
      "tx_hash": "f53401f21c6a43e44d5dd7a90eba5cf580012ad0e15d050059136f8a0da34f6b",
      "tx_size": 159
    },{
      "amount": 27126892247503,
      "global_index": 594994,
      "key_image": "7e561394806afd1be61980cc3431f6ef3569fa9151cd8d234f8ec13aa145695e",
      "spent": false,
      "subaddr_index": 3,
      "tx_hash": "106d4391a031e5b735ded555862fec63233e34e5fa4fc7edcfdbe461c275ae5b",
      "tx_size": 157
    },{
      "amount": 27169374733655,
      "global_index": 594997,
      "key_image": "e76c0a3bfeaae35e4173712f782eb34011198e26b990225b71aa787c8ba8a157",
      "spent": false,
      "subaddr_index": 3,
      "tx_hash": "0bd959b59117ee1254bd8e5aa8e77ec04ef744144a1ffb2d5c1eb9380a719621",
      "tx_size": 158
    }]
  }
}
```

Example, get available transfers:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"incoming_transfers","params":{"transfer_type":"available","account_index":0,"subaddr_indices":[3],"verbose":true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "transfers": [{
      "amount": 27126892247503,
      "global_index": 594994,
      "key_image": "7e561394806afd1be61980cc3431f6ef3569fa9151cd8d234f8ec13aa145695e",
      "spent": false,
      "subaddr_index": 3,
      "tx_hash": "106d4391a031e5b735ded555862fec63233e34e5fa4fc7edcfdbe461c275ae5b",
      "tx_size": 157
    },{
      "amount": 27169374733655,
      "global_index": 594997,
      "key_image": "e76c0a3bfeaae35e4173712f782eb34011198e26b990225b71aa787c8ba8a157",
      "spent": false,
      "subaddr_index": 3,
      "tx_hash": "0bd959b59117ee1254bd8e5aa8e77ec04ef744144a1ffb2d5c1eb9380a719621",
      "tx_size": 158
    }]
  }
}
```

Example, get unavailable transfers:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"incoming_transfers","params":{"transfer_type":"unavailable","account_index":0,"subaddr_indices":[3],"verbose":true}}' -H 'Content-Type: application/json'
{
"id": "0",
"jsonrpc": "2.0",
"result": {
  "transfers": [{
    "amount": 60000000000000,
    "global_index": 122405,
    "key_image": "768f5144777eb23477ab7acf83562581d690abaf98ca897c03a9d2b900eb479b",
    "spent": true,
    "subaddr_index": 3,
    "tx_hash": "f53401f21c6a43e44d5dd7a90eba5cf580012ad0e15d050059136f8a0da34f6b",
    "tx_size": 159
  }]
}
}
```

### **is_multisig**

Check if a wallet is a multisig one.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _multisig_  - boolean; States if the wallet is multisig
-   _ready_  - boolean;
-   _threshold_  - unsigned int; Amount of signature needed to sign a transfer.
-   _total_  - unsigned int; Total amount of signature in the multisig wallet.

Example for a non-multisig wallet:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"is_multisig"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "multisig": false,
    "ready": false,
    "threshold": 0,
    "total": 0
  }
}
```

Example for a multisig wallet:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"is_multisig"}' -H 'Content-Type: application/json'                  {
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "multisig": true,
    "ready": true,
    "threshold": 2,
    "total": 2
  }
}
```

### **label_account**

Label an account.

Alias:  _None_.

Inputs:

-   _account_index_  - unsigned int; Apply label to account at this index.
-   _label_  - string; Label for the account.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"label_account","params":{"account_index":0,"label":"Primary account"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "account_tags": [{
      "accounts": [0,1],
      "label": "",
      "tag": "myTag"
    }]
  }
}
```

### **label_address**

Label an address.

Alias:  _None_.

Inputs:

-   _index_  - subaddress index; JSON Object containing the major & minor address index:
    -   _major_  - unsigned int; Account index for the subaddress.
    -   _minor_  - unsigned int; Index of the subaddress in the account.
-   _label_  - string; Label for the address.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"label_address","params":{"index":{"major":0,"minor":5},"label":"myLabel"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}
```

### **make_integrated_address**

Make an integrated address from the wallet address and a payment id.

Alias:  _None_.

Inputs:

-   _standard_address_  - string; (Optional, defaults to primary address) Destination public address.
-   _payment_id_  - string; (Optional, defaults to a random ID) 16 characters hex encoded.

Outputs:

-   _integrated_address_  - string
-   _payment_id_  - string; hex encoded;

Example (Payment ID is empty, use a random ID):

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_integrated_address","params":{"standard_address":"55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "integrated_address": "5F38Rw9HKeaLQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZXCkbHUXdPHyiUeRyokn",
    "payment_id": "420fa29b2d9a49f5"
  }
}
```

### **make_multisig**

Make a wallet multisig by importing peers multisig string.

Alias:  _None_.

Inputs:

-   _multisig_info_  - array of string; List of multisig string from peers.
-   _threshold_  - unsigned int; Amount of signatures needed to sign a transfer. Must be less or equal than the amount of signature in  `multisig_info`.
-   _password_  - string; Wallet password

Outputs:

-   _address_  - string; multisig wallet address.
-   _multisig_info_  - string; Multisig string to share with peers to create the multisig wallet (extra step for N-1/N wallets).

Example for 2/2 Multisig Wallet:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_multisig","params":{"multisig_info":["MultisigV1K4tGGe8QirZdHgTYoBZMumSug97fdDyM3Z63M3ZY5VXvAdoZvx16HJzPCP4Rp2ABMKUqLD2a74ugMdBfrVpKt4BwD8qCL5aZLrsYWoHiA7JJwDESuhsC3eF8QC9UMvxLXEMsMVh16o98GnKRYz1HCKXrAEWfcrCHyz3bLW1Pdggyowop"],"threshold":2}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "address": "55SoZTKH7D39drxfgT62k8T4adVFjmDLUXnbzEKYf1MoYwnmTNKKaqGfxm4sqeKCHXQ5up7PVxrkoeRzXu83d8xYURouMod",
    "multisig_info": ""
  }
}
```

Example for 2/3 Multisig Wallet:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_multisig","params":{"multisig_info":["MultisigV1MTVm4DZAdJw1PyVutpSy8Q4WisZBCFRAaZY7hhQnMwr5AZ4swzThyaSiVVQM5FHj1JQi3zPKhQ4k81BZkPSEaFjwRJtbfqfJcVvCqRnmBVcWVxhnihX5s8fZWBCjKrzT3CS95spG4dzNzJSUcjheAkLzCpVmSzGtgwMhAS3Vuz9Pas24","MultisigV1TEx58ycKCd6ADCfxF8hALpcdSRAkhZTi1bu4Rs6FdRC98EdB1LY7TAkMxasM55khFgcxrSXivaSr5FCMyJGHmojm1eE4HpGWPeZKv6cgCTThRzC4u6bkkSoFQdbzWN92yn1XEjuP2XQrGHk81mG2LMeyB51MWKJAVF99Pg9mX2BpmYFj"],"threshold":2}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "address": "51sLpF8fWaK1111111111111111111111111111111111ABVbHNf1JFWJyFp5YZgZRQ44RiviJi1sPHgLVMbckRsDkTRgKS",
    "multisig_info": "MultisigxV18jCaYAQQvzCMUJaAWMCaAbAoHpAD6WPmYDmLtBtazD654E8RWkLaGRf29fJ3stU471MELKxwufNYeigP7LoE4tn2Sscwn5g7PyCfcBc1V4ffRHY3Kxqq6VocSCUTncpVeUskaDKuTAWtdB9VTBGW7iG1cd7Zm1dYgur3CiemkGjRUAj9bL3xTEuyaKGYSDhtpFZFp99HQX57EawhiRHk3qq4hjWX"
  }
}
```

### **make_uri**

Create a payment URI using the official URI spec.

Alias:  _None_.

Inputs:

-   _address_  - string; Wallet address
-   _amount_  - unsigned int; (optional) the integer amount to receive, in  **atomic**units
-   _payment_id_  - string; (optional) 16 or 64 character hexadecimal payment id
-   _recipient_name_  - string; (optional) name of the payment recipient
-   _tx_description_  - string; (optional) Description of the reason for the tx

Outputs:

-   _uri_  - string; This contains all the payment input information as a properly formatted payment URI

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_uri","params":{"address":"55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt","amount":10,"payment_id":"420fa29b2d9a49f5","tx_description":"Testing out the make_uri function.","recipient_name":"el00ruobuob Stagenet wallet"}}'  -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "uri": "monero:55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt?tx_payment_id=420fa29b2d9a49f5&tx_amount=0.000000000010&recipient_name=el00ruobuob%20Stagenet%20wallet&tx_description=Testing%20out%20the%20make_uri%20function."
  }
}
```

### **open_wallet**

Open a wallet. You need to have set the argument "–wallet-dir" when launching monero-wallet-rpc to make this work.

Alias:  _None_.

Inputs:

-   _filename_  - string; wallet name stored in –wallet-dir.
-   _password_  - string; (Optional) only needed if the wallet has a password defined.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"open_wallet","params":{"filename":"mytestwallet","password":"mytestpassword"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}
```

### **parse_uri**

Parse a payment URI to get payment information.

Alias:  _None_.

Inputs:

-   _uri_  - string; This contains all the payment input information as a properly formatted payment URI

Outputs:

-   _uri_  - JSON object containing payment information:
    -   _address_  - string; Wallet address
    -   _amount_  - unsigned int; Decimal amount to receive, in  **coin**  units (0 if not provided)
    -   _payment_id_  - string; 16 or 64 character hexadecimal payment id (empty if not provided)
    -   _recipient_name_  - string; Name of the payment recipient (empty if not provided)
    -   _tx_description_  - string; Description of the reason for the tx (empty if not provided)

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"parse_uri","params":{"uri":"monero:55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt?tx_payment_id=420fa29b2d9a49f5&tx_amount=0.000000000010&recipient_name=el00ruobuob%20Stagenet%20wallet&tx_description=Testing%20out%20the%20make_uri%20function."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "uri": {
      "address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt",
      "amount": 10,
      "payment_id": "420fa29b2d9a49f5",
      "recipient_name": "el00ruobuob Stagenet wallet",
      "tx_description": "Testing out the make_uri function."
    }
  }
}
```

### **prepare_multisig**

Prepare a wallet for multisig by generating a multisig string to share with peers.

Alias:  _None_.

Inputs:  _None_.

Outputs:

-   _multisig_info_  - string; Multisig string to share with peers to create the multisig wallet.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"prepare_multisig"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "multisig_info": "MultisigV1BFdxQ653cQHB8wsj9WJQd2VdnjxK89g5M94dKPBNw22reJnyJYKrz6rJeXdjFwJ3Mz6n4qNQLd6eqUZKLiNzJFi3UPNVcTjtkG2aeSys9sYkvYYKMZ7chCxvoEXVgm74KKUcUu4V8xveCBFadFuZs8shnxBWHbcwFr5AziLr2mE7KHJT"
  }
}
```

### **query_key**

Return the spend or view private key.

Alias:  _None_.

Inputs:

-   _key_type_  - string; Which key to retrieve: "mnemonic" - the mnemonic seed (older wallets do not have one) OR "view_key" - the view key

Outputs:

-   _key_  - string; The view key will be hex encoded, while the mnemonic will be a string of words.

Example (Query view key):

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"query_key","params":{"key_type":"view_key"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "key": "0a1a38f6d246e894600a3e27238a064bf5e8d91801df47a17107596b1378e501"
  }
}
```

Example (Query mnemonic key):

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"query_key","params":{"key_type":"mnemonic"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "key": "vocal either anvil films dolphin zeal bacon cuisine quote syndrome rejoices envy okay pancakes tulips lair greater petals organs enmity dedicated oust thwart tomorrow tomorrow"
  }
}
```

### **refresh**

Refresh a wallet after openning.

Alias:  _None_.

Inputs:

-   _start_height_  - unsigned int; (Optional) The block height from which to start refreshing.

Outputs:

-   _blocks_fetched_  - unsigned int; Number of new blocks scanned.
-   _received_money_  - boolean; States if transactions to the wallet have been found in the blocks.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"refresh","params":{"start_height":100000}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "blocks_fetched": 24,
    "received_money": true
  }
}

```

### **relay_tx**

Relay a transaction previously created with  `"do_not_relay":true`.

Alias:  _None_.

Inputs:

-   _hex_  - string; transaction metadata returned from a  `transfer`  method with  `get_tx_metadata`  set to  `true`.

Outputs:

-   _tx_hash_  - String for the publically searchable transaction hash.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"relay_tx","params":{"hex":"...tx_metadata..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_hash": "1c42dcc5672bb09bccf33fb1e9ab4a498af59a6dbd33b3d0cfb289b9e0e25fa5"
  }
}
```

### **rescan_blockchain**

Rescan the blockchain from scratch, losing any information which can not be recovered from the blockchain itself.
This includes destination addresses, tx secret keys, tx notes, etc.

Alias:  _None_.

Inputs:  _None_.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"rescan_blockchain"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}
```

### **rescan_spent**

Rescan the blockchain for spent outputs.

Alias:  _None_.

Inputs:  _None_.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"rescan_spent"}' -H 'Content-Type: application/json'

{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```

### **set_account_tag_description**

Set description for an account tag.

Alias:  _None_.

Inputs:

-   _tag_  - string; Set a description for this tag.
-   _description_  - string; Description for the tag.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"set_account_tag_description","params":{"tag":"myTag","description":"Test tag"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```

### **set_attribute**

Set arbitrary attribute.

Alias:  _None_.

Inputs:

-   _key_  - string; attribute name
-   _value_  - string; attribute value

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"set_attribute","params":{"key":"my_attribute","value":"my_value"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```

### **set_tx_notes**

Set arbitrary string notes for transactions.

Alias:  _None_.

Inputs:

-   _txids_  - array of string; transaction ids
-   _notes_  - array of string; notes for the transactions

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"set_tx_notes","params":{"txids":["3292e83ad28fc1cc7bc26dbd38862308f4588680fbf93eae3e803cddd1bd614f"],"notes":["This is an example"]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```

### **sign**

Sign a string.

Alias:  _None_.

Inputs:

-   _data_  - string; Anything you need to sign.

Outputs:

-   _signature_  - string; Signature generated against the "data" and the account public address.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"sign","params":{"data":"This is sample data to be signed"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "signature": "SigV14K6G151gycjiGxjQ74tKX6A2LwwghvuHjcDeuRFQio5LS6Gb27BNxjYQY1dPuUvXkEbGQUkiHSVLPj4nJAHRrrw3"
  }
}

```

### **sign_multisig**

Sign a transaction in multisig.

Alias:  _None_.

Inputs:

-   _tx_data_hex_  - string; Multisig transaction in hex format, as returned by  `transfer`  under  `multisig_txset`.

Outputs:

-   _tx_data_hex_  - string; Multisig transaction in hex format.
-   _tx_hash_list_  - array of string; List of transaction Hash.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"sign_multisig","params":{"tx_data_hex":"...multisig_txset..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_data_hex": "...multisig_txset...",
    "tx_hash_list": ["4996091b61c1be112c1097fd5e97d8ff8b28f0e5e62e1137a8c831bacf034f2d"]
  }
}

```

### **sign_transfer**

Sign a transaction created on a read-only wallet (in cold-signing process)

Alias:  _None_.

Inputs:

-   _unsigned_txset_  - string. Set of unsigned tx returned by "transfer" or "transfer_split" methods.
-   _export_raw_  - boolean; (Optional) If true, return the raw transaction data. (Defaults to false)

Outputs:

-   _signed_txset_  - string. Set of signed tx to be used for submitting transfer.
-   _tx_hash_list_  - array of: string. The tx hashes of every transaction.
-   _tx_raw_list_  - array of: string. The tx raw data of every transaction.

In the example below, we first generate an unsigned_txset on a read only wallet before signing it:

Generate unsigned_txset using the above "transfer" method on read-only wallet:

```Bash
curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"transfer","params":{"destinations":[{"amount":1000000000000,"address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o"}],"account_index":0,"subaddr_indices":[0],"priority":0,"ring_size":7,"do_not_relay":true,"get_tx_hex":true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "amount": 1000000000000,
    "fee": 15202740000,
    "multisig_txset": "",
    "tx_blob": "...long_hex...",
    "tx_hash": "c648ba0a049e5ce4ec21361dbf6e4b21eac0f828eea9090215de86c76b31d0a4",
    "tx_key": "",
    "tx_metadata": "",
    "unsigned_txset": "...long_hex..."
  }
}

```

Sign tx using the previously generated unsigned_txset

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"sign_transfer","params":{"unsigned_txset":...long_hex..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "signed_txset": "...long_hex...",
    "tx_hash_list": ["ff2e2d49fbfb1c9a55754f786576e171c8bf21b463a74438df604b7fa6cebc6d"]
  }
}

```

### **split_integrated_address**

Retrieve the standard address and payment id corresponding to an integrated address.

Alias:  _None_.

Inputs:

-   _integrated_address_  - string

Outputs:

-   _is_subaddress_  - boolean; States if the address is a subaddress
-   _payment_  - string; hex encoded
-   _standard_address_  - string

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"split_integrated_address","params":{"integrated_address": "5F38Rw9HKeaLQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZXCkbHUXdPHyiUeRyokn"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "is_subaddress": false,
    "payment_id": "420fa29b2d9a49f5",
    "standard_address": "55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt"
  }
}

```

### **start_mining**

Start mining in the Monero daemon.

Alias:  _None_.

Inputs:

-   _threads_count_  - unsigned int; Number of threads created for mining.
-   _do_background_mining_  - boolean; Allow to start the miner in  [smart mining](https://ww.getmonero.org/resources/moneropedia/smartmining.html)mode.
-   _ignore_battery_  - boolean; Ignore battery status (for  [smart mining](https://ww.getmonero.org/resources/moneropedia/smartmining.html)  only)

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"start_mining","params":{"threads_count":1,"do_background_mining":true,"ignore_battery":false}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```

### **stop_mining**

Stop mining in the Monero daemon.

Alias:  _None_.

Inputs:  _None_.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"stop_mining"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```

### **stop_wallet**

Stops the wallet, storing the current state.

Alias:  _None_.

Inputs:  _None_.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"stop_wallet"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **store**

Save the wallet file.

Alias:  _None_.

Inputs:  _None_.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"store"}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **submit_multisig**

Submit a signed multisig transaction.

Alias:  _None_.

Inputs:

-   _tx_data_hex_  - string; Multisig transaction in hex format, as returned by  `sign_multisig`  under  `tx_data_hex`.

Outputs:

-   _tx_hash_list_  - array of string; List of transaction Hash.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"submit_multisig","params":{"tx_data_hex":"...tx_data_hex..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_hash_list": ["4996091b61c1be112c1097fd5e97d8ff8b28f0e5e62e1137a8c831bacf034f2d"]
  }
}

```


### **submit_transfer**

Submit a previously signed transaction on a read-only wallet (in cold-signing process).

Alias:  _None_.

Inputs:

-   _tx_data_hex_  - string; Set of signed tx returned by "sign_transfer"

Outputs:

-   _tx_hash_list_  - array of: string. The tx hashes of every transaction.

In the example below, we submit the transfer using the signed_txset generated above:

```Bash
curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"submit_transfer","params":{"tx_data_hex":...long_hex..."}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_hash_list": ["40fad7c828bb383ac02648732f7afce9adc520ba5629e1f5d9c03f584ac53d74"]
  }
}

```


### **sweep_all**

Send all unlocked balance to an address.

Alias:  _None_.

Inputs:

-   _address_  - string; Destination public address.
-   _account_index_  - unsigned int; Sweep transactions from this account.
-   _subaddr_indices_  - array of unsigned int; (Optional) Sweep from this set of subaddresses in the account.
-   _priority_  - unsigned int; (Optional) Priority for sending the sweep transfer, partially determines fee.
-   _mixin_  - unsigned int; Number of outputs from the blockchain to mix with (0 means no mixing).
-   _ring_size_  - unsigned int; Sets ringsize to n (mixin + 1).
-   _unlock_time_  - unsigned int; Number of blocks before the monero can be spent (0 to not add a lock).
-   _payment_id_  - string; (Optional) Random 32-byte/64-character hex string to identify a transaction.
-   _get_tx_keys_  - boolean; (Optional) Return the transaction keys after sending.
-   _below_amount_  - unsigned int; (Optional) Include outputs below this amount.
-   _do_not_relay_  - boolean; (Optional) If true, do not relay this sweep transfer. (Defaults to false)
-   _get_tx_hex_  - boolean; (Optional) return the transactions as hex encoded string. (Defaults to false)
-   _get_tx_metadata_  - boolean; (Optional) return the transaction metadata as a string. (Defaults to false)

Outputs:

-   _tx_hash_list_  - array of: string. The tx hashes of every transaction.
-   _tx_key_list_  - array of: string. The transaction keys for every transaction.
-   _amount_list_  - array of: integer. The amount transferred for every transaction.
-   _fee_list_  - array of: integer. The amount of fees paid for every transaction.
-   _tx_blob_list_  - array of: string. The tx as hex string for every transaction.
-   _tx_metadata_list_  - array of: string. List of transaction metadata needed to relay the transactions later.
-   _multisig_txset_  - string. The set of signing keys used in a multisig transaction (empty for non-multisig).
-   _unsigned_txset_  - string. Set of unsigned tx for cold-signing purposes.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"sweep_all","params":{"address":"55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt","subaddr_indices":[4],"ring_size":7,"unlock_time":0,"get_tx_keys":true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "amount_list": [9985885770000],
    "fee_list": [14114230000],
    "multisig_txset": "",
    "tx_hash_list": ["ab4b6b65cc8cd8c9dd317d0b90d97582d68d0aa1637b0065b05b61f9a66ea5c5"],
    "tx_key_list": ["b9b4b39d3bb3062ddb85ec0266d4df39058f4c86077d99309f218ce4d76af607"],
    "unsigned_txset": ""
  }
}
```


### **sweep_dust**

Send all dust outputs back to the wallet's, to make them easier to spend (and mix).

Alias:  _sweep_unmixable_.

Inputs:

-   _get_tx_keys_  - boolean; (Optional) Return the transaction keys after sending.
-   _do_not_relay_  - boolean; (Optional) If true, the newly created transaction will not be relayed to the monero network. (Defaults to false)
-   _get_tx_hex_  - boolean; (Optional) Return the transactions as hex string after sending. (Defaults to false)
-   _get_tx_metadata_  - boolean; (Optional) Return list of transaction metadata needed to relay the transfer later. (Defaults to false)

Outputs:

-   _tx_hash_list_  - array of: string. The tx hashes of every transaction.
-   _tx_key_list_  - array of: string. The transaction keys for every transaction.
-   _amount_list_  - array of: integer. The amount transferred for every transaction.
-   _fee_list_  - array of: integer. The amount of fees paid for every transaction.
-   _tx_blob_list_  - array of: string. The tx as hex string for every transaction.
-   _tx_metadata_list_  - array of: string. List of transaction metadata needed to relay the transactions later.
-   _multisig_txset_  - string. The set of signing keys used in a multisig transaction (empty for non-multisig).
-   _unsigned_txset_  - string. Set of unsigned tx for cold-signing purposes.

Example (In this example,  `sweep_dust`  returns nothing because there are no funds to sweep):

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"sweep_dust","params":{"get_tx_keys":true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "multisig_txset": "",
    "unsigned_txset": ""
  }
}

```


### **sweep_single**

Send all of a specific unlocked output to an address.

Alias:  _None_.

Inputs:

-   _address_  - string; Destination public address.
-   _account_index_  - unsigned int; Sweep transactions from this account.
-   _subaddr_indices_  - array of unsigned int; (Optional) Sweep from this set of subaddresses in the account.
-   _priority_  - unsigned int; (Optional) Priority for sending the sweep transfer, partially determines fee.
-   _mixin_  - unsigned int; Number of outputs from the blockchain to mix with (0 means no mixing).
-   _ring_size_  - unsigned int; Sets ringsize to n (mixin + 1).
-   _unlock_time_  - unsigned int; Number of blocks before the monero can be spent (0 to not add a lock).
-   _payment_id_  - string; (Optional) Random 32-byte/64-character hex string to identify a transaction.
-   _get_tx_keys_  - boolean; (Optional) Return the transaction keys after sending.
-   _key_image_  - string; Key image of specific output to sweep.
-   _below_amount_  - unsigned int; (Optional) Include outputs below this amount.
-   _do_not_relay_  - boolean; (Optional) If true, do not relay this sweep transfer. (Defaults to false)
-   _get_tx_hex_  - boolean; (Optional) return the transactions as hex encoded string. (Defaults to false)
-   _get_tx_metadata_  - boolean; (Optional) return the transaction metadata as a string. (Defaults to false)

Outputs:

-   _tx_hash_list_  - array of: string. The tx hashes of every transaction.
-   _tx_key_list_  - array of: string. The transaction keys for every transaction.
-   _amount_list_  - array of: integer. The amount transferred for every transaction.
-   _fee_list_  - array of: integer. The amount of fees paid for every transaction.
-   _tx_blob_list_  - array of: string. The tx as hex string for every transaction.
-   _tx_metadata_list_  - array of: string. List of transaction metadata needed to relay the transactions later.
-   _multisig_txset_  - string. The set of signing keys used in a multisig transaction (empty for non-multisig).
-   _unsigned_txset_  - string. Set of unsigned tx for cold-signing purposes.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"sweep_single","params":{"address":"74Jsocx8xbpTBEjm3ncKE5LBQbiJouyCDaGhgSiebpvNDXZnTAbW2CmUR5SsBeae2pNk9WMVuz6jegkC4krUyqRjA6VjoLD","ring_size":7,"unlock_time":0,"key_image":"a7834459ef795d2efb6f665d2fd758c8d9288989d8d4c712a68f8023f7804a5e","get_tx_keys":true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "amount": 27126892247503,
    "fee": 14111630000,
    "multisig_txset": "",
    "tx_blob": "",
    "tx_hash": "106d4391a031e5b735ded555862fec63233e34e5fa4fc7edcfdbe461c275ae5b",
    "tx_key": "",
    "tx_metadata": "",
    "unsigned_txset": ""
  }
}

```


### **tag_accounts**

Apply a filtering tag to a list of accounts.

Alias:  _None_.

Inputs:

-   _tag_  - string; Tag for the accounts.
-   _accounts_  - array of unsigned int; Tag this list of accounts.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"tag_accounts","params":{"tag":"myTag","accounts":[0,1]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **transfer**

Send monero to a number of recipients.

Alias:  _None_.

Inputs:

-   _destinations_  - array of destinations to receive XMR:
    -   _amount_  - unsigned int; Amount to send to each destination, in  atomic units.
    -   _address_  - string; Destination public address.
-   _account_index_  - unsigned int; (Optional) Transfer from this account index. (Defaults to 0)
-   _subaddr_indices_  - array of unsigned int; (Optional) Transfer from this set of subaddresses. (Defaults to 0)
-   _priority_  - unsigned int; Set a priority for the transaction. Accepted Values are: 0-3 for: default, unimportant, normal, elevated, priority.
-   _mixin_  - unsigned int; Number of outputs from the blockchain to mix with (0 means no mixing).
-   _ring_size_  - unsigned int; Number of outputs to mix in the transaction (this output + N decoys from the blockchain).
-   _unlock_time_  - unsigned int; Number of blocks before the monero can be spent (0 to not add a lock).
-   _payment_id_  - string; (Optional) Random 32-byte/64-character hex string to identify a transaction.
-   _get_tx_key_  - boolean; (Optional) Return the transaction key after sending.
-   _do_not_relay_  - boolean; (Optional) If true, the newly created transaction will not be relayed to the monero network. (Defaults to false)
-   _get_tx_hex_  - boolean; Return the transaction as hex string after sending (Defaults to false)
-   _get_tx_metadata_  - boolean; Return the metadata needed to relay the transaction. (Defaults to false)

Outputs:

-   _amount_  - Amount transferred for the transaction.
-   _fee_  - Integer value of the fee charged for the txn.
-   _multisig_txset_  - Set of multisig transactions in the process of being signed (empty for non-multisig).
-   _tx_blob_  - Raw transaction represented as hex string, if get_tx_hex is true.
-   _tx_hash_  - String for the publically searchable transaction hash.
-   _tx_key_  - String for the transaction key if get_tx_key is true, otherwise, blank string.
-   _tx_metadata_  - Set of transaction metadata needed to relay this transfer later, if get_tx_metadata is true.
-   _unsigned_txset_  - String. Set of unsigned tx for cold-signing purposes.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"transfer","params":{"destinations":[{"amount":100000000000,"address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o"},{"amount":200000000000,"address":"75sNpRwUtekcJGejMuLSGA71QFuK1qcCVLZnYRTfQLgFU5nJ7xiAHtR5ihioS53KMe8pBhH61moraZHyLoG4G7fMER8xkNv"}],"account_index":0,"subaddr_indices":[0],"priority":0,"ring_size":7,"get_tx_key": true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "amount": 300000000000,
    "fee": 86897600000,
    "multisig_txset": "",
    "tx_blob": "",
    "tx_hash": "7663438de4f72b25a0e395b770ea9ecf7108cd2f0c4b75be0b14a103d3362be9",
    "tx_key": "25c9d8ec20045c80c93d665c9d3684aab7335f8b2cd02e1ba2638485afd1c70e236c4bdd7a2f1cb511dbf466f13421bdf8df988b7b969c448ca6239d7251490e4bf1bbf9f6ffacffdcdc93b9d1648ec499eada4d6b4e02ce92d4a1c0452e5d009fbbbf15b549df8856205a4c7bda6338d82c823f911acd00cb75850b198c5803",
    "tx_metadata": "",
    "unsigned_txset": ""
  }
}

```


### **transfer_split**

Same as transfer, but can split into more than one tx if necessary.

Alias:  _None_.

Inputs:

-   _destinations_  - array of destinations to receive XMR:
    -   _amount_  - unsigned int; Amount to send to each destination, in  atomic units.
    -   _address_  - string; Destination public address.
-   _account_index_  - unsigned int; (Optional) Transfer from this account index. (Defaults to 0)
-   _subaddr_indices_  - array of unsigned int; (Optional) Transfer from this set of subaddresses. (Defaults to 0)
-   _mixin_  - unsigned int; Number of outputs from the blockchain to mix with (0 means no mixing).
-   _ring_size_  - unsigned int; Sets ringsize to n (mixin + 1).
-   _unlock_time_  - unsigned int; Number of blocks before the monero can be spent (0 to not add a lock).
-   _payment_id_  - string; (Optional) Random 32-byte/64-character hex string to identify a transaction.
-   _get_tx_keys_  - boolean; (Optional) Return the transaction keys after sending.
-   _priority_  - unsigned int; Set a priority for the transactions. Accepted Values are: 0-3 for: default, unimportant, normal, elevated, priority.
-   _do_not_relay_  - boolean; (Optional) If true, the newly created transaction will not be relayed to the monero network. (Defaults to false)
-   _get_tx_hex_  - boolean; Return the transactions as hex string after sending
-   _new_algorithm_  - boolean; True to use the new transaction construction algorithm, defaults to false.
-   _get_tx_metadata_  - boolean; Return list of transaction metadata needed to relay the transfer later.

Outputs:

-   _tx_hash_list_  - array of: string. The tx hashes of every transaction.
-   _tx_key_list_  - array of: string. The transaction keys for every transaction.
-   _amount_list_  - array of: integer. The amount transferred for every transaction.
-   _fee_list_  - array of: integer. The amount of fees paid for every transaction.
-   _tx_blob_list_  - array of: string. The tx as hex string for every transaction.
-   _tx_metadata_list_  - array of: string. List of transaction metadata needed to relay the transactions later.
-   _multisig_txset_  - string. The set of signing keys used in a multisig transaction (empty for non-multisig).
-   _unsigned_txset_  - string. Set of unsigned tx for cold-signing purposes.

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"transfer_split","params":{"destinations":[{"amount":1000000000000,"address":"7BnERTpvL5MbCLtj5n9No7J5oE5hHiB3tVCK5cjSvCsYWD2WRJLFuWeKTLiXo5QJqt2ZwUaLy2Vh1Ad51K7FNgqcHgjW85o"},{"amount":2000000000000,"address":"75sNpRwUtekcJGejMuLSGA71QFuK1qcCVLZnYRTfQLgFU5nJ7xiAHtR5ihioS53KMe8pBhH61moraZHyLoG4G7fMER8xkNv"}],"account_index":0,"subaddr_indices":[0],"priority":0,"ring_size":7,"get_tx_key": true}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "amount_list": [3000000000000],
    "fee_list": [85106400000],
    "multisig_txset": "",
    "tx_hash_list": ["c8d815f48f27d53fdaf198a74b292a91bfaf87529a9a9a9ee66079a890b3b58b"],
    "unsigned_txset": ""
  }
}

```


### **untag_accounts**

Remove filtering tag from a list of accounts.

Alias:  _None_.

Inputs:

-   _accounts_  - array of unsigned int; Remove tag from this list of accounts.

Outputs:  _None_.

Example:

```Bash
$ curl -X POST http://localhost:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"untag_accounts","params":{"accounts":[1]}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
  }
}

```


### **verify**

Verify a signature on a string.

Alias:  _None_.

Inputs:

-   _data_  - string; What should have been signed.
-   _address_  - string; Public address of the wallet used to  `sign`  the data.
-   _signature_  - string; signature generated by  `sign`  method.

Outputs:

-   _good_  - boolean;

Example:

```Bash
$ curl -X POST http://127.0.0.1:18082/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"verify","params":{"data":"This is sample data to be signed","address":"55LTR8KniP4LQGJSPtbYDacR7dz8RBFnsfAKMaMuwUNYX6aQbBcovzDPyrQF9KXF9tVU6Xk3K8no1BywnJX6GvZX8yJsXvt","signature":"SigV14K6G151gycjiGxjQ74tKX6A2LwwghvuHjcDeuRFQio5LS6Gb27BNxjYQY1dPuUvXkEbGQUkiHSVLPj4nJAHRrrw3"}}' -H 'Content-Type: application/json'
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "good": true
  }
}

```

## Sources:

Reworked from [GetMonero.org](https://ww.getmonero.org/resources/developer-guides/wallet-rpc.html)
