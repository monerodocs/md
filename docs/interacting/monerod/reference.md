---
title: monerod - Reference | Monero Documentation
---

# `monerod` - reference

## Syntax

`./monerod [options] [command]` 

Options affect how daemon should be working. Commands assume the daemon is already running. Commands give access to specific services provided by the daemon.

## Options

Following option groups are only to make the reference easier to follow. The daemon itself does not group options in any way.

#### Pick network

| Option           | Description                                                                                    
|------------------|------------------------------------------------------------------------------------------------
| (missing)        | By default monerod assumes [mainnet](/networks).                                               
| `--stagenet`     | Run on [stagenet](/networks). Remember to run your wallet with `--stagenet` as well.           
| `--testnet`      | Run on [testnet](/networks). Remember to run your wallet with `--testnet` as well.             

#### Logging

| Option                | Description                                                                                    
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------
| `--log-file`          | Full path to the log file. Example (mind file permissions): <br/>`./monerod --log-file=/var/log/monero/mainnet/monerod.log`
| `--log-level`         | `0-4` with `0` being minimal logging and `4` being full tracing. Defaults to `0`. These are general presets and do not directly map to severity levels. For example, even with minimal `0`, you may see some most important `INFO` entries. Temporarily changing to `1` allows for much better understanding of how the full node operates. Example: <br />`./monerod --log-level=1`
| `--max-log-file-size` | Soft limit in bytes for the log file (=104850000 by default, which is just under 100MB). Once log file grows past that limit, `monerod` creates next log file with a `-YYYY-MM-DD-HH-MM-SS` UTC timestamp postfix. In production deployments, you would probably prefer to use established solutions like logrotate instead.

#### Server

`monerod` defaults are adjusted for running it occasionally on the same computer as your Monero wallet.

The following options will be helpful if you intend to have an always running node - most likely on a remote server or your own separate PC.

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--config-file`     | Full path to the configuration file. By default `monerod` looks for `bitmonero.conf` in Monero [data directory](/interacting/monerod/overview/#data-directory). TODO: describe configuration file syntax.
| `--data-dir`        | Full path to data directory. This is where the blockchain, log files, and p2p network memory are stored. For defaults and details see [data directory](/interacting/monerod/overview/#data-directory).
| `--pidfile`         | Full path to the PID file. Works only with `--detach`. Example: <br />`./monerod --detach --pidfile=/run/monero/monerod.pid`
| `--detach`          | Go to background (decouple from the terminal). This is useful for long-running / server scenarios. Typically, you will also want to manage `monerod` daemon with systemd or similar. By default `monerod` runs in a foreground.
| `--non-interactive` | Do not require tty in a foreground mode. Helpful when running in a container. By default `monerod` runs in a foreground and opens stdin for reading. This breaks containerization because no tty getss assigned and `monerod` process crashes. You can make it run in a background with `--detach` but this is inconvenient in a containerized environment because the canonical usage is that the container waits on the main process to exist (forking makes things more complicated).

#### P2P network

(WORK IN PROGRESS)

The following options define how your node participates in Monero peer-to-peer network. This is for node-to-node communication. It does **not** affect wallet-to-node interface.

| Option                 | Description
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--p2p-bind-ip`        | Network interface to bind to for p2p network protocol. Default value `0.0.0.0` binds to all network interfaces. This is typically what you want. You must change this if you want to constrain binding, for example to configure connection through TOR.
| `--p2p-bind-port`      | TCP port to listen for p2p network connections. Defaults to `18080` for mainnet, `28080` for testnet, and 38080 for stagenet. You normally wouldn't change that.
| `--hide-my-port`       | `monerod` will still open and listen on the p2p port. However, it will not announce itself as a peerlist candidate. Technically, it will return port `0` in a response to p2p handshake (`node_data.my_port = 0` in `get_local_node_data` function). In effect nodes you connect to won't spread your IP to other nodes. To sum up, it is not really hiding, it is more like "do not advertise".
| `--add-priority-node`  | Specify list of peers to connect to and
| `--add-exclusive-node` | Specify list of peers to connect to options add-priority-node and seed-node
| `--seed-node`          | Connect to a node to retrieve peer
| `--p2p-external-port`  | External port for p2p network protocol (if port forwarding used with NAT). Default is `0`.
| `--no-igd`             | Disable UPnP port mapping.


#### Help and Version

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--help`            | Enlists available options.
| `--version`         | Shows `monerod` version to stdout. Example: <br />`Monero 'Lithium Luna' (v0.12.3.0-release)`


TO BE CONTINUED

## Reference

* [Reddit answer](https://www.reddit.com/r/Monero/comments/3jhyqc/0mq_help_share_this_exciting_news/)
* [SE 1](https://monero.stackexchange.com/questions/1482/how-much-information-is-passed-from-the-daemon-to-simplewallet-when-scanning-for?rq=1)
* [SE 2](https://monero.stackexchange.com/questions/1134/is-it-safe-to-share-a-daemon-with-a-roommate?noredirect=1&lq=1)
