---
title: Monero Configuration File
---
# Monero Configuration File

## Applicability

By default Monero looks for `bitmonero.conf` in Monero [data directory](/interacting/overview/#data-directory).

To use a specific config file add `--config-file` option:

`./monerod --config-file=/etc/monerod.conf`

The `--config-file` option is available for: 

* `monerod`
* `monero-wallet-cli`
* `monero-wallet-rpc`
* `monero-gen-trusted-multisig`

## Syntax

* `option-name=value`
* `valueless-option-name=1` for options that don't expect value
* `# comment`
* whitespace is ignored

## Reference

All configuration options are the same as command line options for the binary.

* [monerod reference](/interacting/monerod-reference)
* [monero-wallet-cli reference](/interacting/monero-wallet-cli-reference)
* [monero-wallet-rpc reference](/interacting/monero-wallet-rpc-reference)

Skip the `--` from `--option-name`.

Example:

`./monerod --log-level=4 --stagenet`

translates to:

    log-level=4
    stagenet=1     # use value "1" to enable the value-less options like --stagenet 

## Examples

### `monerod.conf`

This config is tailored for production server usage.

    # /etc/monero/monerod.conf
    
    # Data directory (blockchain db and indices)
    data-dir=/home/monero/.monero  # Remember to create the monero user first
    
    # Log file
    log-file=/var/log/monero/monerod.log
    max-log-file-size=0            # Prevent monerod from managing the log files; we want logrotate to take care of that
    
    # P2P full node
    p2p-bind-ip=0.0.0.0            # Bind to all interfaces (the default)
    p2p-bind-port=18080            # Bind to default port
    
    # RPC open node
    rpc-bind-ip=0.0.0.0            # Bind to all interfaces
    rpc-bind-port=18081            # Bind on default port
    confirm-external-bind=1        # Open node (confirm)
    restricted-rpc=1               # Prevent unsafe RPC calls
    no-igd=1                       # Disable UPnP port mapping
    
    # Slow but reliable db writes
    db-sync-mode=safe
    
    # Emergency checkpoints set by MoneroPulse operators will be enforced to workaround potential consensus bugs
    # Check https://monerodocs.org/infrastructure/monero-pulse/ for explanation and trade-offs
    enforce-dns-checkpointing=1
    
    out-peers=64              # This will enable much faster sync and tx awareness; the default 8 is suboptimal nowadays
    in-peers=1024             # The default is unlimited; we prefer to put a cap on this
    
    limit-rate-up=1048576     # 1048576 kB/s == 1GB/s; a raise from default 2048 kB/s; contribute more to p2p network
    limit-rate-down=1048576   # 1048576 kB/s == 1GB/s; a raise from default 8192 kB/s; allow for faster initial sync

### `monero-wallet-cli.conf`

This config is tailored for desktop usage on [stagenet](/infrastructure/networks).

    # $HOME/.bitmonero/stagenet/monero-wallet-cli.conf

    # Pick network
    stagenet=1

    # Connect to a remote full node    
    daemon-address=monero-stagenet.exan.tech:38081
    untrusted-daemon=1

    # Log file
    log-file=/tmp/monero-wallet-cli.log
    
    # wallet-file=/home/YOUR-USER/.bitmonero/stagenet/wallets/MoneroExampleStagenetWallet
