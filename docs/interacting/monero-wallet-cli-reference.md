---
title: monero-wallet-cli - Reference
---
# `monero-wallet-cli` - Reference

!!! note
    Get yourself comfortable with a friendly Monero CLI wallet.
    It is the most reliable and most complete wallet for Monero.
    Use [stagenet](/infrastructure/networks) for learning.

## Overview

### Command line wallet

The "official" command line wallet for Monero. Available for Linux, macOS and Windows.

Wallet uses your private keys to understand your total balance,
transactions history, and to facilitate creating transactions.

However, wallet does not store the blockchain and does not directly participate in the p2p network.

The CLI wallet is the most reliable and most feature complete wallet for Monero.

### Depends on the full node

Wallet connects to a [full node](/interacting/monerod-reference) to scan the blockchain for your transaction outputs and to send your transactions out to the network.

The full node can be either local (same computer) or remote.

Normally, you run the full node on the same computer as wallet (or within your home network).

Connection happens over HTTP and uses [this API](https://www.getmonero.org/resources/developer-guides/wallet-rpc.html).

Any transaction leaving the wallet is already blinded by all Monero privacy features.
This means plain text HTTP communication isn't an issue on its own even if you connect to a remote node.

However, connecting to a remote node has other nuanced trade-offs, which is a topic for a separate article.

## Syntax

`./monero-wallet-cli [options] [command]`

Example:

`./monero-wallet-cli --stagenet`

## Running

Go to directory where you unpacked Monero.

Run the full node and wait until it syncs up with the network (may take up to a few days):

`./monerod --stagenet`

In a separate terminal window, run the wallet:

`./monero-wallet-cli --stagenet --generate-new-wallet MoneroExampleStagenetWallet`

## Options

#### Help and version

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--help`            | Enlist available options.
| `--version`         | Show `monero-wallet-cli` version to stdout. Example: <br />`Monero 'Boron Butterfly' (v0.14.0.0-release)`

#### Pick network

| Option           | Description
|------------------|------------------------------------------------------------------------------------------------
| (missing)        | By default wallet assumes [mainnet](/infrastructure/networks).
| `--stagenet`     | Run on [stagenet](/infrastructure/networks). Remember to run your daemon with `--stagenet` as well.
| `--testnet`      | Run on [testnet](/infrastructure/networks). Remember to run your daemon with `--testnet` as well.

#### Logging

| Option                      | Description
|-----------------------------|----------------------------------------------------------------------------------------------------------------------------------------
| `--log-file <arg>`          | Full path to the log file.
| `--log-level <arg>`         | `0-4` with `0` being minimal logging and `4` being full tracing. Defaults to `0`. These are general presets and do not directly map to severity levels. For example, even with minimal `0`, you may see some most important `INFO` entries.
| `--max-log-file-size <arg>` | Soft limit in bytes for the log file (=104850000 by default, which is just under 100MB). Once log file grows past that limit, monero creates the next log file with a UTC timestamp postfix `-YYYY-MM-DD-HH-MM-SS`.<br /><br />In production deployments, you would probably prefer to use established solutions like logrotate instead. In that case, set `--max-log-file-size 0` to prevent monero from managing the log files.
| `--max-log-files <arg>`     | Limit on the number of log files (=50 by default). The oldest log files are removed. In production deployments, you would probably prefer to use established solutions like logrotate instead.

#### Full node connection

Wallet depends on a full node for all non-local operations. The following options define how to connect to [`monerod`](/interacting/monerod-reference):

| Option                   | Description
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--daemon-address <arg>` | Use `monerod` instance at `<host>:<port>`. Example: <br />`./monero-wallet-cli --daemon-address monero-stagenet.exan.tech:38081 --stagenet`
| `--daemon-host <arg>`    | Use `monerod` instance at host `<arg>` instead of localhost.
| `--daemon-port <arg>`    | Use `monerod` instance at port `<arg>` instead of 18081.
| `--daemon-login <arg>`   | Specify `username[:password]` for `monerod` RPC API. It is based on HTTP Basic Auth. Mind that connections are by default unencrypted. Authentication only makes sense if you establish a secure connection (maybe via Tor, or SSH tunneling, or reverse proxy w/ TLS).
| `--trusted-daemon`       | Enable commands and behaviors which rely on `monerod` instance being trusted. Default for localhost connection. The trust in this context concerns preserving your privacy. Only use this flag if you do control `monerod`. Trusted daemon allows for commands like `rescan_spent`, `start_mining`, `import_key_images` and behaviors like **not** warning about potential attack on transient problems with transaction sending.
| `--untrusted-daemon`     | Disable commands and behaviors which rely on `monerod` instance being trusted. Default for a non-localhost connections. See `--trusted-daemon` for more details.
| `--do-not-relay`         | The newly created transaction will not be relayed to the Monero network. Instead it will be dumped to a file in a raw hexadecimal format. Useful if you want to push the transaction through a gateway like [https://xmrchain.net/rawtx](https://xmrchain.net/rawtx). This may be easier to use over Tor than Monero wallet.
| `--allow-mismatched-daemon-version` | Allow communicating with `monerod` that uses a different RPC version.

#### Create new wallet

| Option                         | Description
|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--generate-new-wallet <arg>`  | Create a new Monero wallet and save it to `<arg>` file. You will be asked for a password. The password is used to encrypt the wallet file but it is unrelated to your master spend key or mnemonic seed. Generate a very strong password with your password manager (~256 bits of entropy). Example:<br /><br />`./monero-wallet-cli --stagenet --generate-new-wallet $HOME/.bitmonero/stagenet/wallets/MoneroExampleStagenetWallet`
| `--kdf-rounds <arg>`           | Concerns encrypting the wallet file. The wallet file is encrypted with ChaCha stream cipher. The encryption key is derived from the user supplied password by hashing the password with CryptoNight. This option defines how many times the CryptoNight hashing will be applied. The default is `1` round of hashing. <br /><br />Note this is **unrelated** to spend key generation. <br /><br />The more rounds the longer you will wait to open the wallet or send transaction. But also the attacker will have it harder to brute force your wallet password. <br /><br />**Note:** You will have to remember and provide the same `kdf-rounds` on every wallet access!<br /><br />**Recommendation:** Do not change the default value. Instead generate a very strong wallet password with your password manager (256 bits of entropy).

#### Open existing wallet

| Option                      | Description
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--wallet-file <arg>`       | Open existing wallet. Example: <br/><br/>`./monero-wallet-cli --stagenet --wallet-file $HOME/.bitmonero/stagenet/wallets/MoneroExampleStagenetWallet` <br /><br/>This is only for wallet files generated with `monero-wallet-cli`, `monero-wallet-gui`, or `monero-wallet-rpc` tools. If you have other type of wallet then see importing options.
| `--password <arg>`          | Provide wallet password as a parameter instead of interactively. Remember to escape/quote as needed. <br /><br />**Not recommended** because the password will remain in your command history and will also be visible in the process table. For automation prefer `--password-file`. <br /><br />The option also works in combination with `--generate-new-wallet`.
| `--password-file <arg>`     | Provide password as a file in stead of interactively. Trailing `\n` are discarded when reading the password file. <br /><br />Prefer this over `--password` if you automate wallet access. Make sure the password file is meaningfully separated from the wallet file. Otherwise it provides no security benefit. <br /><br />The option also works in combination with `--generate-new-wallet`.

#### Restore wallet

| Option                           | Description
|----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--generate-from-device <arg>`   | Restore/generate a special wallet to work with a **hardware device** like [Ledger](https://www.ledger.com/) or [Trezor](https://trezor.io/) and save it to `<arg>` file. Example: <br /><br />`./monero-wallet-cli --stagenet --generate-from-device MoneroExampleDeviceWallet --subaddress-lookahead 5:20` <br /><br />This is a one-time action. Next time you simply [open the wallet](#open-existing-wallet).<br /><br />By default the command expects Ledger hardware connected. For Trezor hardware add `--hw-device Trezor` (expected ~May 2019).<br /><br />It will take **up to 25 minutes** with default settings. This is because hardware devices are slow to pre-generate subaddresses. To mitigate use low `--subaddress-lookahead 5:20`. <br /><br />The local wallet will not have private spend key and will not be able to spend on its own. It serves as a user interface and a bridge for low-power hardware devices. Transaction signing with a private spend key always happens on the hardware device. <br /><br />See the [complete guide to hardware wallet setup](https://www.reddit.com/r/Monero/comments/8op6cp/ledger_cli_guides_requires_cli_v01220/).
| `--generate-from-view-key <arg>` | Restore a view-only version of the wallet to track incoming transactions and save it to `<arg>` file. The wallet is created based on a **secret view key** and **standard address**. The secret view key is meant to be pasted as hexadecimal.
| `--generate-from-spend-key <arg>`| Restore a wallet from **secret spend key** and save it to `<arg>` file. The secret spend key is meant to be pasted as hexadecimal.
| `--restore-deterministic-wallet` | Restore a wallet from **secret mnemonic seed**. Use this to restore from your 25 words backup. <br /><br />You will be asked for a password to encrypt the wallet file (once restored). Note this is **not** a passphrase to mnemonic seed. Mnemonic seeds generated by Monero official wallets are naked.
| `--restore-height <arg>`         | Only scan for transactions later than specific blockchain height. The default is `0`. Raising the value makes wallet restoration **radically faster**. The optimal value should match the day you originally created the wallet (but cannot be later). The mapping between the block height and date/time is available on block explorers like [https://xmrchain.net](https://xmrchain.net/). For instance, if you created the wallet in 2019+ use `1730000`.

#### Multisig wallet

| Option                                | Description
|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--generate-from-multisig-keys <arg>` | Create a standard wallet from multisig keys. This is useful to combine all multisig secret keys back into the standard wallet (when you no longer need the multisig). The wallet will then have control of the funds. It only supports providing all secret keys even if the multisig scheme allowed for less (only `N/N` not `N/M`).
| `--restore-multisig-wallet`           | Restore a multisig wallet from **secret seed** that was earlier exported with the `seed` interactive command. This only restores your part of the wallet. Other multisig participants will still be necessary to sign the transaction.

#### Config file

| Option                   | Description
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--config-file <arg>`    | Full path to the [configuration file](/interacting/monero-config-file). Note this should be a separate config than `monerod` uses because these tools accept different set of options.

#### Performance

| Option                         | Description
|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--subaddress-lookahead <arg>` | Accepts `m:n`, by default `50:200`. The first value is the number of accounts and the second value is the number of subaddresses per account. <br /><br />The wallet will not check for payments to subaddresses further than `n` away from the last received payment. This can happen if you generated unique subaddresses for `n` clients in a row but none of them paid. <br /><br >On the other hand the more subaddresses you set to look ahead, the longer it takes to create your wallet, because they must be pre-computed. This is normally not a concern, except for hardware wallets. On the Ledger the default value of `50:200` can take over 20 minutes (one time on wallet creation)!
| `--max-concurrency <arg>`      | Max number of threads to use for parallel jobs. The default value `0` uses the number of CPU threads.

#### Internationalization

| Option                         | Description
|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--mnemonic-language <arg>`    | Language for mnemonic seed words. One of `english`, `english_old`, `esperanto`, `french`, `german`, `italian`, `japanese`, `lojban`, `portuguese`, `russian`, `spanish`. <br /><br />It might be a good idea to stick to default English which is by far the most popular and well tested. It also avoids potential non-ASCII characters pitfalls or bugs.
| `--use-english-language-names` | If your display freezes, exit blind with ^C, then run again with `--use-english-language-names`. This can happen when Monero prompts for a language displaying language names in their natives alphabets.

#### Legacy

These options are either legacy or rarely useful.

| Option                       | Description
|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--non-deterministic`        | Generate legacy non-deterministic wallet. The view key will **not** be derived from the spend key. You would also have to backup the *.keys. To restore non-deterministic wallet (standard address) use `--generate-from-keys`. To restore fully you will need the *.keys file.
| `--generate-from-keys <arg>` | Restore legacy non-deterministic wallet by providing both spend and view keys and the standard address.
| `--shared-ringdb-dir <arg>`  | Set shared ring database path. [No longer worthwhile](https://www.reddit.com/r/Monero/comments/9rtnpx/are_there_any_updated_blackball_databases/).
| `--create-address-file`      | Has no effect. The `*.address.txt` file is created regardless of this option.
| `--electrum-seed <arg>`      | Provide mnemonic seed as a commandline option for `--restore-deterministic-wallet` instead of interactively. This is not recommended b/c the seed will be saved in your command history and also visible in the process list.
| `--generate-from-json <arg>` | You would run `monero-wallet-rpc` to use this option. It seems exposed in `monero-wallet-cli` by accident.
| `--tx-notify <arg>`          | You would run `monero-wallet-rpc` to use this option. It seems exposed in `monero-wallet-cli` by accident.

## Defaults

Wallet files are created and seek in current directory. This is rarely what you want. Use `--wallet-file` and similar options to control this.

Log files are created in the same directory as `monero-wallet-cli` binary. Use `--log-file` to specify the location.

## Commands

Commands are used interactively in the `monero-wallet-cli` prompt.

You can also run a one-off command by providing it as a commandline parameter.
This is rarely useful though. For automation prefer `monero-wallet-rpc`.

The CLI wallet has **built-in help for individual commands** - we will not attempt to reproduce that.
Instead we focus on grouping commands so you can quickly find what you are looking for.
Use `help command_name` to learn more.

### Help and version

`help` - list all commands

`help command_name` - show help for individual command

`version` - show version of the monero-wallet-cli binary

### Network status

`status` - show if synced up to the blockchain height

`fee` - show current fee-per-byte and full node's mempool (the backlog of transactions depending on the priority)

`wallet_info` - show wallet file path, standard address, type and network

### Balance

`account` - total balance; list accounts with respective balances

`balance detail` - within the current account, list addresses with respective balances

`refresh` - force refresh the balance and transactions by pulling latest blocks from the full node; this is often useful because auto-refresh only kicks in once in 90 seconds

### Manage accounts

`account`

`account new`

`account switch`

`account label`

### Manage addresses

`address all`

`address new`

`address label`

### View transactions

`show_transfers` - show all transactions on the current account; optionally provide a filter: `in` | `out` | `pending` | `failed` | `pool` | `coinbase`; optionally provide subaddress index for output selection

`show_transfer <txid>` - show details of specific transaction

`incoming_transfers [available|unavailable] [verbose] [index=<N1>[,<N2>[,...]]]` - show the incoming transactions, all or filtered by availability and address index within current account; this will only show confirmed transactions; you will not see transactions awaiting in the mempool

`get_tx_note <txid>` - get a string note for transaction id

`export_transfers [in|out|all|pending|failed|pool|coinbase] [index=<N1>[,<N2>,...]] [<min_height> [<max_height>]] [output=<filepath>] [option=<with_keys>]` - exports a list of all transfer information to a CSV file. You can filter by type `[in|out|all|pending|failed|pool|coinbase]`, by index, by minimum and maximum block height, specify the output path for the CSV file, and optionally include the tx private keys (if available) with the export.

### Keys and Passwords

#### Secret mnemonic seed

`seed` - show raw mnemonic seed

`encrypted_seed` - create mnemonic seed encrypted with the passphrase; you will need to remember or store the passphrase separately; restoring will not be possible without the passphrase

#### Secret keys

`spendkey` - show secret spend key and public spend key

`viewkey` - show secret view key and public view key

#### Wallet password

`password` - change wallet password; this password is used to encrypt the local wallet files; it does not change secret keys or backups

### Proofs

`get_reserve_proof` -> `check_reserve_proof` - prove the balance

`get_spend_proof` -> `check_spend_proof` - prove you made the payment

`sign <file>` -> `verify <filename> <address> <signature>` - prove ownership of the address; allows to verify the file was signed by the owner of specific Monero address

`get_tx_proof` -> `check_tx_proof`

### Multisig

#### Setup

`prepare_multisig`

`make_multisig`

`finalize_multisig`

#### Update

`export_multisig_info`

`import_multisig_info`

#### Other

`submit_multisig`

`exchange_multisig_keys`

`export_raw_multisig_tx`

`sign_multisig <filename>`

### Hardware wallet

`hw_reconnect` - attempts to reconnect HW wallet

### Mining

`start_mining`

`stop_mining`

### Advanced

#### Outputs

`unspent_outputs` - show a list of, and a histogram of unspent outputs (indivisible pieces of your total balance)

`export_outputs <file>` -> `import_outputs <file>` - helps with cold spending; export outputs from a view-wallet to the cold-wallet to make it aware of what had been sent to it

`mark_output_spent <amount>/<offset> | <filename> [add]` - "blackball"/mark an output known to be spent, so that it will no longer be selected as a decoy

`mark_output_unspent <amount>/<offset>` - unmark an output not known to be spent, so that it will possibly be selected as a decoy

`is_output_spent <amount>/<offset>`


#### Key images

`export_key_images <file>` -> `import_key_images <file>` - used to inform the view-only wallet about outgoing transactions so it can calculate the real balance; normally view-only wallets only learn about incoming transactions, not outgoing

#### Tx private key

These allow to learn and verify transaction's private key `r`.
This was useful to create a [proof of payment](https://www.getmonero.org/resources/user-guides/prove-payment.html)
but got superseded by `get_spend_proof`.

`get_tx_key <txid>`

`check_tx_key <txid> <txkey> <address>`

`set_tx_key <txid> <tx_key>`

### Debugging

`rescan_spent` - rescan the blockchain for spent outputs; sometimes, the wallet's idea of what outputs are spent and what outputs are not get out of sync with the blockchain. This can happen if you exit the wallet without saving after sending a tx, or if it crashes. This will look for the key images on the blockchain to make sure it's up to date.

### Cosmetics

`donate <amount>` - donate `<amount>` to development team

`address_book [(add ((<address> [pid <id>])|<integrated address>) [<description possibly with whitespaces>])|(delete <index>)]`

`set_description [free text note]` -> `get_description` - manage convenience description of the wallet (the information is local)

### Legacy

`save` - this now happens automatically

`save_bc` - this now happens automatically

`bc_height` - show blockchain height (superseded with `status`)

`sweep_unmixable` - only relevant for very old wallets (<= 2016); send all unmixable outputs to yourself with ring_size 10

`locked_sweep_all` - see


`rescan_bc` - rescan the blockchain from scratch, losing any information which can not be recovered from the blockchain itself

TODO: document remaining commands
