---
title: Running Monero Open Node with Tor Onion Support
---
# Running Monero Open Node + Tor Onion

!!! success "Powerful setup"

    This is great contribution to Monero network and also a pretty sophisticated personal setup. If you are a beginner, you don't need this.

!!! info "The end goal"
    You will publicly offer the following services, where xxx.yyy.zzz.vvv is your server IP address.

    * xxx.yyy.zzz.vvv:18080 - clearnet P2P service (for other nodes)
    * xxx.yyy.zzz.vvv:18081 - clearnet RPC service (for wallets)
    * yourlongv3onionaddress.onion:18083 - onion P2P service (for other onion nodes)
    * yourlongv3onionaddress.onion:18081 - onion RPC service (for wallets connecting over Tor)

    Why different P2P ports for clearnet and onion? This is a `monerod` requirement.

!!! warning "Broadcasting bad transactions from your IP"
    As with any public data broadcast or relay service, "bad traffic" or in this case "bad transactions" may appear to originate from your server IP address from an outside observer perspective - even though they really originate from a remote wallet user. This is a potential risk you need to keep in mind.

## Why run this specific setup?

You will be able to connect your desktop and mobile Monero wallets to your own trusted Monero node,
in a secure and private way over Tor. Your node will be always ready w/o delays (always synced up, contrary to intermittently running node on a laptop).

**Serving blocks and transactions** in Monero P2P network helps new users to bootstrap and sync up their nodes.
It also strenghtens Monero P2P network against DDoS attacks and network partitioning.

**Open wallet inteface** (the "RPC") allows anyone to connect their wallets to Monero network through your node.
This is useful for beginner users who don't run their own nodes yet.

**Tor onion for wallet interface** is useful for wallet users connecting over Tor because it mitigates Tor exit nodes MiTM risks (which are very real). By connecting wallet to an onion service, no MiTM attack is realistic because within the Tor network connections are end-to-end TLS-ed.

**Tor onion for P2P network** is useful for other full node users as it allows them to broadcast transactions over Tor (using `--tx-proxy` option).

## Assumptions

You understand basic Linux administration. You seek Monero specific guidance.

You have root access to a Linux server with 2GB+ RAM and 120GB+ SSD (or 50GB+ for the pruned node version). This is current for Jan 2021.

Some commands assume Ubuntu but you will easily translate them to your distribution.

## Install Tor

[Install Tor](https://2019.www.torproject.org/docs/debian.html.en#ubuntu).

Modify `/etc/tor/torrc` as shown below.

Enable tor service with `systemctl enable tor` and restart it via `systemctl restart tor`

Verify the Tor is up `systemctl status tor@default`

A fresh onion address and corresponding key pair got created for you by the `tor` daemon in `/var/lib/tor/monero/`. You may want to backup these to secure control over your onion address. This happens on restart whenever you add new `HiddenServiceDir` to `torrc` config.

Monero daemon itself is not necessary at this point. The onion services (AKA hidden services) will just wait until localhost `monerod` shows up at specified ports 18081 and 18083.

### /etc/tor/torrc

``` ApacheConf
HiddenServiceDir /var/lib/tor/monero
HiddenServicePort 18081 127.0.0.1:18081    # interface for wallet ("RPC")
HiddenServicePort 18083 127.0.0.1:18083    # interface for P2P network
```

??? info "How Tor onion services work?"

    The `tor` daemon will simply pass over the traffic from virtual onion port to actual localhost port, where some service is listening (in our case, this will be `monerod`). A single onion address can offer multiple services at various virtual ports. We will use this to expose both P2P and RPC `monerod` services on a single onion. You could host any number of onion addresses at single server or IP address but we won't need that here.

## Install Monero

Create `monero` user and group `useradd --system monero`

Create monero **binaries** directory (empty for now) `mkdir -p /opt/monero` and `chown -R monero:monero /opt/monero`

Create monero **data** directory `mkdir -p /srv/monero` and `chown -R monero:monero /srv/monero`

Create monero **log** directory `mkdir -p /var/log/monero` and `chown -R monero:monero /var/log/monero`

Feel free to adjust above to your preferred conventions, just remember to adjust the paths accordingly.

[Download](/interacting/download-monero-binaries/) and [verify](/interacting/verify-monero-binaries/) the file.

Extract `tar -xf monero-linux-x64-v0.17.1.9.tar.bz2` (adjust filename).

Move binaries to `/opt/monero/` with `mv monero-x86_64-linux-gnu-v0.17.1.9/* /opt/monero/` then `chown -R monero:monero /opt/monero`

Create `/etc/monero.conf` as shown below and **paste your values in placeholders**.

Create `/etc/systemd/system/monero.service` as shown below.

Enable monero service with `systemctl enable monero` and restart it with `systemctl restart monero`

Verify it is up `systemctl status monero`

Verify it is working as intended `tail -n100 /var/log/monero/monero.log`

### /etc/monero.conf

This is just an example configuration and it is by no means authoritative. Feel free to modify, see [monerod reference](/interacting/monerod-reference).

Modify paths if you changed them.

Print your onion address with `cat /var/lib/tor/monero/hostname` and paste it to `anonymous-inbound` option.

``` YAML
# /etc/monero.conf
#
# Configuration file for monerod. For all available options see the MoneroDocs:
# https://monerodocs.org/interacting/monerod-reference/

# Data directory (blockchain db and indices)
data-dir=/srv/monero

# Optional prunning
# prune-blockchain=1           # Pruning saves 2/3 of disk space w/o degrading functionality but contributes less to the network
# sync-pruned-blocks=1         # Allow downloading pruned blocks instead of prunning them yourself

check-updates=disabled         # Do not check DNS TXT records for a new version

# Log file
log-file=/var/log/monero/monero.log
log-level=0                    # Minimal logs, WILL NOT log peers or wallets connecting
max-log-file-size=2147483648   # Set to 2GB to mitigate log trimming by monerod; configure logrotate instead

# P2P full node
p2p-bind-ip=0.0.0.0            # Bind to all interfaces (the default)
p2p-bind-port=18080            # Bind to default port

# RPC open node
public-node=1                  # Advertise to other users they can use this node as a remote one for connecting their wallets
confirm-external-bind=1        # Open Node (confirm)
rpc-bind-ip=0.0.0.0            # Bind to all interfaces (the Open Node)
rpc-bind-port=18081            # Bind to default port (the Open Node)
restricted-rpc=1               # Obligatory for Open Node interface
no-igd=1                       # Disable UPnP port mapping
no-zmq=1                       # Disable ZMQ RPC server to decrease attack surface (it's not used)

# RPC TLS
rpc-ssl=autodetect             # Use TLS if client wallet supports it (the default behavior); the certificate will be generated on the fly on every restart

# Mempool size
max-txpool-weight=268435456    # Maximum unconfirmed transactions pool size in bytes (here 256MB, default ~618MB)

# Slow but reliable db writes
db-sync-mode=safe

out-peers=64              # This will enable much faster sync and tx awareness; the default 8 is suboptimal nowadays
in-peers=64               # The default is unlimited; we prefer to put a cap on this

limit-rate-up=1048576     # 1048576 kB/s == 1GB/s; a raise from default 2048 kB/s; contribute more to p2p network
limit-rate-down=1048576   # 1048576 kB/s == 1GB/s; a raise from default 8192 kB/s; allow for faster initial sync

# Tor: broadcast transactions originating from connected wallets over Tor (does not concern relayed transactions)
tx-proxy=tor,127.0.0.1:9050,16

# Tor: add P2P seed nodes for the Tor network
# For an up-to-date list of working nodes see https://www.ditatompel.com/monero/node-peers
add-peer=4egylyolrzsk6rskorqvocipdo4tqqoyzxnplbjorns7issmgpoxvtyd.onion:18083
add-peer=fagmobguo6u4z4b2ghyg3jegcdpmd4qj4wxkhemph5d5q6dltllveqyd.onion:18083
add-peer=monerokdwzyuml7vfp73fjx5277lzesbrq4nvbl3r3t5ctgodsx34vid.onion:18089
add-peer=b75obarnhi42p7js7wgzo7v3wtiwcgf4bknrwv6ihatop77jivrtwpid.onion:15892
add-peer=5nvd6jbefgto3u74nzzdkcsbqgxyzrkk7bz5qupsdqg4gbuj5valiaqd.onion:18083
add-peer=ozeavjybjbxbvmfcpxzjcn4zklbgohjwwndzenjt44pypvx6jisy74id.onion:18083
add-peer=xcccrsxi2zknef6zl3sviasqg4xnlkh5k3xqu7ytwkpfli3huyfvsjid.onion:18083

# Make the seed nodes permanent to fix monerod issue of not maintaining enough connections,
# based on this reddit comment:
# https://www.reddit.com/r/monerosupport/comments/k3m3x2/comment/ge5ehcy/?utm_source=share&utm_medium=web2x&context=3
add-priority-node=4egylyolrzsk6rskorqvocipdo4tqqoyzxnplbjorns7issmgpoxvtyd.onion:18083
add-priority-node=fagmobguo6u4z4b2ghyg3jegcdpmd4qj4wxkhemph5d5q6dltllveqyd.onion:18083
add-priority-node=monerokdwzyuml7vfp73fjx5277lzesbrq4nvbl3r3t5ctgodsx34vid.onion:18089
add-priority-node=b75obarnhi42p7js7wgzo7v3wtiwcgf4bknrwv6ihatop77jivrtwpid.onion:15892
add-priority-node=5nvd6jbefgto3u74nzzdkcsbqgxyzrkk7bz5qupsdqg4gbuj5valiaqd.onion:18083
add-priority-node=ozeavjybjbxbvmfcpxzjcn4zklbgohjwwndzenjt44pypvx6jisy74id.onion:18083
add-priority-node=xcccrsxi2zknef6zl3sviasqg4xnlkh5k3xqu7ytwkpfli3huyfvsjid.onion:18083

# Tor: tell monerod your onion address so it can be advertised on P2P network
anonymous-inbound=PASTE_YOUR_ONION_HOSTNAME:18083,127.0.0.1:18083,64

# Tor: be forgiving to connecting wallets; suggested by http://xmrguide42y34onq.onion/remote_nodes
disable-rpc-ban=1
```

### /etc/.../monero.service

``` INI
# /etc/systemd/system/monero.service

[Unit]
Description=Monero Daemon
After=network.target
Wants=network.target

[Service]
ExecStart=/opt/monero/monerod --detach --config-file /etc/monero.conf --pidfile /run/monero/monerod.pid
ExecStartPost=/bin/sleep 0.1
Type=forking
PIDFile=/run/monero/monerod.pid

Restart=always
RestartSec=16

User=monero
Group=monero
RuntimeDirectory=monero

StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

## Open firewall ports

If you use a firewall (and you should), open `18080` and `18081` ports for incoming TCP connections. These are for the incoming **clearnet** connections, P2P and RPC respectively.

You **do not** need to open any ports for Tor. The onion services work with virtual ports. The `tor` daemon does not directly accept incoming connections and so it needs no open ports.

For example, for popular ufw firewall, that would be:

``` Bash
ufw allow 18080/tcp
ufw allow 18081/tcp
```

To verify, use `ufw status`. The output should be similar to the following (the `22` being default SSH port, unrelated to Monero):

```
To                         Action      From
--                         ------      ----
22/tcp                     LIMIT       Anywhere
18080/tcp                  ALLOW       Anywhere
18081/tcp                  ALLOW       Anywhere
22/tcp (v6)                LIMIT       Anywhere (v6)
18080/tcp (v6)             ALLOW       Anywhere (v6)
18081/tcp (v6)             ALLOW       Anywhere (v6)
```


## Testing

### On server

List all services listening on ports and make sure it is what you expect:

`sudo netstat -lntpu`

The output should include these (in any order); obviously the PID values will differ.

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
...
tcp        0      0 0.0.0.0:18080           0.0.0.0:*               LISTEN      259255/monerod
tcp        0      0 0.0.0.0:18081           0.0.0.0:*               LISTEN      259255/monerod
tcp        0      0 127.0.0.1:18083         0.0.0.0:*               LISTEN      259255/monerod
tcp        0      0 127.0.0.1:9050          0.0.0.0:*               LISTEN      258786/tor
```

### On client machine

Finally, we want to test connections from your client machine.

Install `tor` and `torsocks` on your laptop, you will want them anyway for Monero wallet.

Just for testing, you will also need `nmap` and `proxychains`.

Test **clearnet P2P** connection:

`nmap -Pn -p 18080 YOUR_IP_ADDRESS_HERE`

Test **clearnet RPC** connection:

`curl --digest -X POST http://YOUR_IP_ADDRESS_HERE:18081/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' -H 'Content-Type: application/json'`

Test **onion P2P** connection (skip if you don't have proxychains):

`proxychains nmap -Pn -p 18083 YOUR_ONION_ADDRESS_HERE.onion`

Test **onion RPC** connection:

`torsocks curl --digest -X POST http://YOUR_ONION_ADDRESS_HERE.onion:18081/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' -H 'Content-Type: application/json'`


## Debugging

Tor:

  * Status: `systemctl status tor@default`
  * Logs: `journalctl -xe --unit tor@default`

Monero:

  * Status: `systemctl status monero`
  * Logs: `tail -n100 /var/log/monero/monero.log`
  * Logs more info: change `log-level=0` to `log-level=1` in `monero.conf` (remember to revert once solved)


## Further improvements

### Periodic restarts

It's likely worthwhile to add peridic auto-restarting to both `tor` and `monerod` every couple hours. Neither daemon is perfect; they can get stuck or leak memory in edge case situations,
like the recent attacks on Tor v3 or DDoS attacks on the Monero network. One possible way would be to use systemd timers.
