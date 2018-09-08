# `monerod` - reference

## Syntax

`./monerod [options] [command]` 

Options affect how daemon should be working. Commands assume the daemon is already running. Commands give access to specific services provided by the daemon.

## Options

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

| Option              | Description
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------
| `--detach`          | Go to background (decouple from the terminal). This is useful for long-running / server scenarios. Typically, you will also want to manage `monerod` daemon with systemd or similar.
| `--non-interactive` | Do not require tty in a foreground mode. Helpful when running in a container. By default `monerod` runs in a foreground and opens stdin for reading. This breaks containerization because no tty getss assigned and `monerod` process crashes. You can make it run in a background with `--detach` but this is inconvenient in a containerized environment because the canonical usage is that the container waits on the main process to exist (forking makes things more complicated).
| `--pidfile`         | Full path to the PID file. Works only with `--detach`. Example: <br />`./monerod --detach --pidfile=/run/monero/monerod.pid`

TO BE CONTINUED

## Reference

* [Reddit answer](https://www.reddit.com/r/Monero/comments/3jhyqc/0mq_help_share_this_exciting_news/)
* [SE 1](https://monero.stackexchange.com/questions/1482/how-much-information-is-passed-from-the-daemon-to-simplewallet-when-scanning-for?rq=1)
* [SE 2](https://monero.stackexchange.com/questions/1134/is-it-safe-to-share-a-daemon-with-a-roommate?noredirect=1&lq=1)
