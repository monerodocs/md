---
title: monerod - Reference | Monero Documentation
---

# `monerod` - reference

## Syntax

`./monerod [options] [command]` 

Options define how daemon should be working. Their names follow the `--option-name` pattern.

Commands give access to specific services provided by the daemon. Commands are executed against a running daemon.
Their names follow the `command_name` pattern.

## Options

Following option groups are only to make this reference easier to follow. The daemon itself does not group options in any way.

#### Pick network

| Option           | Description                                                                                    
|------------------|------------------------------------------------------------------------------------------------
| (missing)        | By default monerod assumes [mainnet](/infrastructure/networks).                                               
| `--stagenet`     | Run on [stagenet](/infrastructure/networks). Remember to run your wallet with `--stagenet` as well.           
| `--testnet`      | Run on [testnet](/infrastructure/networks). Remember to run your wallet with `--testnet` as well.             

#### Logging

| Option                | Description                                                                                    
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------
| `--log-file`          | Full path to the log file. Example (mind file permissions): <br/>`./monerod --log-file=/var/log/monero/mainnet/monerod.log`
| `--log-level`         | `0-4` with `0` being minimal logging and `4` being full tracing. Defaults to `0`. These are general presets and do not directly map to severity levels. For example, even with minimal `0`, you may see some most important `INFO` entries. Temporarily changing to `1` allows for much better understanding of how the full node operates. Example: <br />`./monerod --log-level=1`
| `--max-log-file-size` | Soft limit in bytes for the log file (=104850000 by default, which is just under 100MB). Once log file grows past that limit, `monerod` creates next log file with a `-YYYY-MM-DD-HH-MM-SS` UTC timestamp postfix. In production deployments, you would probably prefer to use established solutions like logrotate instead.

#### Server

`monerod` defaults are adjusted for running it occasionally on the same computer as your Monero wallet.

The following options will be helpful if you intend to have an always running node &mdash; most likely on a remote server or your own separate PC.

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--config-file`     | Full path to the configuration file. By default `monerod` looks for `bitmonero.conf` in Monero [data directory](/interacting/monerod/overview/#data-directory). TODO: describe configuration file syntax.
| `--data-dir`        | Full path to data directory. This is where the blockchain, log files, and p2p network memory are stored. For defaults and details see [data directory](/interacting/monerod/overview/#data-directory).
| `--pidfile`         | Full path to the PID file. Works only with `--detach`. Example: <br />`./monerod --detach --pidfile=/run/monero/monerod.pid`
| `--detach`          | Go to background (decouple from the terminal). This is useful for long-running / server scenarios. Typically, you will also want to manage `monerod` daemon with systemd or similar. By default `monerod` runs in a foreground.
| `--non-interactive` | Do not require tty in a foreground mode. Helpful when running in a container. By default `monerod` runs in a foreground and opens stdin for reading. This breaks containerization because no tty getss assigned and `monerod` process crashes. You can make it run in a background with `--detach` but this is inconvenient in a containerized environment because the canonical usage is that the container waits on the main process to exist (forking makes things more complicated).
| `--no-igd`          | Disable UPnP port mapping on the router ("Internet Gateway Device"). Add this option to improve security if you are **not** behind a NAT (you can bind directly to public IP or you run through Tor).
| `--max-txpool-size arg`       | Set maximum transactions pool size in bytes. By default 648000000 (~618MB). These are transactions pending for confirmations (not included in any block).
| `--enforce-dns-checkpointing` | The emergency checkpoints set by MoneroPulse operators will be enforced. It is probably a good idea to set enforcing for unattended nodes. <br /><br />If encountered block hash does not match corresponding checkpoint, the local blockchain will be rolled back a few blocks, effectively blocking following what MoneroPulse operators consider invalid fork. The log entry will be produced:  `ERROR` `Local blockchain failed to pass a checkpoint, rolling back!` Eventually, the alternative ("fixed") fork will get heavier and the node will follow it, leaving the "invalid" fork behind.<br /><br />By default checkpointing only notifies about discrepancy by producing the following log entry: `ERROR` `WARNING: local blockchain failed to pass a MoneroPulse checkpoint, and you could be on a fork. You should either sync up from scratch, OR download a fresh blockchain bootstrap, OR enable checkpoint enforcing with the --enforce-dns-checkpointing command-line option`.<br /><br />Reference: [source code](https://github.com/monero-project/monero/blob/22a6591a70151840381e327f1b41dc27cbdb2ee6/src/cryptonote_core/blockchain.cpp#L3614).
| `--disable-dns-checkpoints`   | The MoneroPulse checkpoints set by core developers will be discarded. The checkpoints are apparently still fetched though.

#### P2P network

The following options define how your node participates in Monero peer-to-peer network.
This is for node-to-node communication. The following options do **not** affect wallet-to-node interface.

The node and peer words are used interchangeably.

| Option                 | Description
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--p2p-bind-ip`        | Network interface to bind to for p2p network protocol. Default value `0.0.0.0` binds to all network interfaces. This is typically what you want. <br /><br />You must change this if you want to constrain binding, for example to configure connection through Tor via torsocks: <br />`DNS_PUBLIC=tcp://1.1.1.1 TORSOCKS_ALLOW_INBOUND=1 torsocks ./monerod --p2p-bind-ip 127.0.0.1 --no-igd --hide-my-port`
| `--p2p-bind-port`      | TCP port to listen for p2p network connections. Defaults to `18080` for mainnet, `28080` for testnet, and `38080` for stagenet. You normally wouldn't change that. This is helpful to run several nodes on your machine to simulate private Monero p2p network (likely using private Testnet). Example: <br/>`./monerod --p2p-bind-port=48080`
| `--p2p-external-port`  | TCP port to listen for p2p network connections on your router. Relevant if you are behind a NAT and still want to accept incoming connections. You must then set this to relevant port on your router. This is to let `monerod` know what to advertise on the network. Default is `0`.
| `--hide-my-port`       | `monerod` will still open and listen on the p2p port. However, it will not announce itself as a peer list candidate. Technically, it will return port `0` in a response to p2p handshake (`node_data.my_port = 0` in `get_local_node_data` function). In effect nodes you connect to won't spread your IP to other nodes. To sum up, it is not really hiding, it is more like "do not advertise".
| `--seed-node`          | Connect to a node to retrieve other nodes' addresses, and disconnect. If not specified, `monerod` will use hardcoded seed nodes on the first run, and peers cached on disk on subsequent runs.  
| `--add-peer`           | Manually add node to local peer list.
| `--add-priority-node`  | Specify list of nodes to connect to and then attempt to keep the connection open. <br /><br />To add multiple nodes use the option several times. Example: <br />`./monerod --add-priority-node=178.128.192.138:18081 --add-priority-node=144.76.202.167:18081`
| `--add-exclusive-node` | Specify list of nodes to connect to only. If this option is given the options `--add-priority-node` and `--seed-node` are ignored. <br /><br />To add multiple nodes use the option several times. Example: <br />`./monerod --add-exclusive-node=178.128.192.138:18081 --add-exclusive-node=144.76.202.167:18081`
| `--out-peers`          | Set max number of outgoing connections to other nodes. By default 8. Value `-1` represents the code default.
| `--in-peers`           | Set max number of incoming connections (nodes actively connecting to you). By default unlimited. Value `-1` represents the code default.
| `--limit-rate-up`      | Set outgoing data transfer limit [kB/s]. By default 2048 kB/s. Value `-1` represents the code default.
| `--limit-rate-down`    | Set incoming data transfer limit [kB/s]. By default 8192 kB/s. Value `-1` represents the code default.
| `--limit-rate`         | Set the same limit value for incoming and outgoing data transfer. By default (`-1`) the individual up/down default limits will be used. It is better to use `--limit-rate-up` and `--limit-rate-down` instead to avoid confusion.
| `--offline`            | Do not listen for peers, nor connect to any. Useful for working with a local, archival blockchain.
| `--allow-local-ip`     | Allow adding local IP to peer list. Useful mostly for debug purposes when you may want to have multiple nodes on a single machine.

#### Legacy

These options should no longer be necessary. They are still present in `monerod` for backwards compatibility.

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--fluffy-blocks`   | Relay compact blocks. Default. Compact block is just a header and a list of transaction IDs.
| `--no-fluffy-blocks`| Relay classic full blocks. Classic block contains all transactions.
| `--db-type`         | Specify database type. The default and only available: `lmdb`.

#### Help and Version

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--help`            | Enlists available options.
| `--version`         | Shows `monerod` version to stdout. Example: <br />`Monero 'Lithium Luna' (v0.12.3.0-release)`
| `--os-version`      | Shows build timestamp and target operating system. Example output:<br />`OS: Linux #1 SMP PREEMPT Fri Aug 24 12:48:58 UTC 2018 4.18.5-arch1-1-ARCH`.


## Reference

* [Reddit answer](https://www.reddit.com/r/Monero/comments/3jhyqc/0mq_help_share_this_exciting_news/)
* [SE 1](https://monero.stackexchange.com/questions/1482/how-much-information-is-passed-from-the-daemon-to-simplewallet-when-scanning-for?rq=1)
* [SE 2](https://monero.stackexchange.com/questions/1134/is-it-safe-to-share-a-daemon-with-a-roommate?noredirect=1&lq=1)
