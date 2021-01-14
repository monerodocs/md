---
title: Tor Onion Seed Nodes for Monero P2P Network
---
# Tor onion seed nodes for Monero network


## When this is necessary?

This is only necessary if you run a full node and you want to propagate locally-originating transactions over Tor (using `tx-proxy` option, see [monerod reference](/interacting/monerod-reference/#tori2p)). This allows you to mask your IP (as transaction originator) against P2P network surveillence, on top of Monero's built-in Dandelion++.

## What are P2P seed nodes?

Your monero daemon will discover other P2P nodes but it needs to start somewhere. These starting nodes are known as "seed nodes". For clearnet the seed nodes are hardcoded in the software so no configuration is needed.

For the Tor network there are no hardcoded seed nodes. You must specify them manually by using the `add-peer` option in `monero.conf`.

## Config snippet

These were tested working as of 2020-01-11. They are ran by volunteers and are **not guaranteed** to work or be maintained. See below how you can quickly test them.

```
# monero.conf snippet
# ...
add-peer=moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion:18080
add-peer=monerozf6koypqrt.onion:18080
add-peer=zbjkbsxc5munw3qusl7j2hpcmikhqocdf4pqhnhtpzw5nt5jrmofptid.onion:18083        # https://github.com/monero-project/monero/blob/master/src/p2p/net_node.inl
add-peer=rno75kjcw3ein6i446sqby2xkyqjarb75oq36ah6c2mribyklzhurpyd.onion:28083        # it is mainnet despite the weird port
add-peer=sqzrokz36lgkng2i2nlzgzns2ugcxqosflygsxbkybb4xn6gq3ouugqd.onion:18083        # very flaky, works 1 in 3 times
add-peer=blzchctiibfjfvtukctsydhquucz2oajnxnfc5hh4ix35gyqjhdnaqqd.onion:18083        # full node by author of monerodocs.org
# ...
```

## How to test onion seed nodes?

You need Tor daemon installed and running on your laptop. You will also need `torsocks` command line tool that either comes with Tor or needs to be installed separately.

### Test using torsocks and telnet

You need tor, torsocks and telnet installed.

To test speficic onion:

    torsocks telnet moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion 18080

The expected output (domain resolution errors are fine):

```
1610372702 ERROR torsocks[11332]: Unable to resolve. Status reply: 4 (in socks5_recv_resolve_reply() at socks5.c:677)
Trying 127.42.42.0...
Connected to moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion.
Escape character is '^]'.
```

### Test using torsocks and proxychains

You need tor, torsocks and proxychains installed.

To test specific onion:

    proxychains nmap -Pn -p 18080 moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion

The expected output (example):

````
[proxychains] Strict chain  ...  127.0.0.1:9050  ...  moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion:18080  ...  OK
Nmap scan report for moneroxmrxw44lku6qniyarpwgznpcwml4drq7vb24ppatlcg4kmxpqd.onion (224.0.0.1)
Host is up (0.74s latency).
rDNS record for 224.0.0.1: all-systems.mcast.net

PORT      STATE SERVICE
18080/tcp open  unknown
````

## Not enough onion seed nodes

The onion seed nodes are a scarce resource. Most nodes are only maintained temporarily by their authors.

You can greatly contribute to Monero P2P network performance and resillience by running onion-enabled monero full node.
