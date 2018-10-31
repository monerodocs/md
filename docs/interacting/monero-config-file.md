---
title: Monero Configuration File | Monero Documentation
---
# Monero Configuration File

## Applicability

By default Monero looks for `bitmonero.conf` in Monero [data directory](/interacting/overview/#data-directory).

To use a specific config file add `--config-file` option:

`./monerod --config-file=/etc/monero.conf`

The `--config-file` option is available for: 

* `monerod`
* `monero-wallet-cli`
* `monero-wallet-rpc`
* `monero-gen-trusted-multisig`

## Syntax

* `option-name=value`
* `valueless-option-name=1` for options that don't expect value
* `# I am a comment`
* whitespace is ignored

## Reference

All command line options work as configuration file options. See [monerod reference](/interacting/monerod-reference).

Skip the `--` from `--option-name`.

Example:

`./monerod --log-level=4 --stagenet`

translates to:

    log-level=4
    stagenet=1     # use value "1" for 

## Example

    # /etc/monero.conf
    
    # Data directory (blockchain db and indices)
    data-dir=/home/monero/.monero
    
    # Log file
    log-file=/var/log/monero/monero.log
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
