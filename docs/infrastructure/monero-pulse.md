---
title: MoneroPulse
---
# MoneroPulse

## What is MoneroPulse?

MoneroPulse is infrastructure for emergency checkpointing the blockchain.

It aims to mitigate chain-splits resulting from consensus bugs (like [this one from 2014](https://monero.stackexchange.com/questions/421/what-happened-at-block-202612/424#424)).

Effectively, MoneroPulse operators can publish which fork they consider the valid one. Technically, the "checkpoint" they publish is a block hash and the block height.

By default, Monero full node will simply warn users when MoneroPulse checkpoint does not match the fork it is on. The error will be present in the log and on the console in red. Users are free to discard it. Ideally though, users should consult community on what is going on, and make educated decission on whether to follow the checkpoint-compatible fork or the default fork.

Users can also set auto-enforcing the checkpoints via `--enforce-dns-checkpointing` option to `monerod`. In case of mismatch, `monerod` will rollback the local blockchain by a few blocks. Eventually, the alternative ("fixed") fork will get heavier and the node will follow it, leaving the "invalid" fork behind. This option is recommended for unattended full nodes.

Summing up, MoneroPulse is emergency checkpointing mechanism. It is opt-in for the users.

## MoneroPulse is DNS based

The ckeckpoints are stored as DNS TXT records for domains owned by MoneroPulse operators.

To get the idea you can access the checkpoints manually with any DNS client:

Try:

    dig -t txt checkpoints.moneropulse.net +dnssec

Result:

    (cut)

    ;; ANSWER SECTION:
    checkpoints.moneropulse.net. 299 IN  TXT  "1288616:875ac1bc7aa6c5eedc5410abb9c694034f9e7f79dce4c60698baf37009cb6365"
    checkpoints.moneropulse.net. 299 IN  TXT  "375000:c80c23e387585e12ffb6649d678e9ba328181797b9583a6d8911b77e25375737"
    checkpoints.moneropulse.net. 299 IN  TXT  "325000:4260d56368267bc2a70dd58d73c5ecf23b4e4d96e63c29a868e4a679b0741c7f"
    checkpoints.moneropulse.net. 299 IN  TXT  "233000:4f69bec2af6c0852412bdd10c19e6af10c8d738fe2618b5511a98efd03ab477e"
    checkpoints.moneropulse.net. 299 IN  TXT  "450000:4d098b511ca97723e81737c448343cfd4e6dadb3d8a0e757c6e4d595e6e48357"
    checkpoints.moneropulse.net. 299 IN  TXT  "250000:f59d31839bd909ec8830b4f7f66ff213f0bd006334c8523daee452725e5c7a79"
    checkpoints.moneropulse.net. 299 IN  TXT  "550000:c2e80a636438bd9f7a7ab432a6ad297e35540d80ff5b868bca098124cad2ff8c"
    checkpoints.moneropulse.net. 299 IN  TXT  "650000:1d567f2b491324375a825895c5e7b52857b38e4fed0e42c40909c2d52240b4e0"
    checkpoints.moneropulse.net. 299 IN  TXT  "800000:2ced10aa85357ab6c14bb12b6b56d1dde28940820dda30911b73a5cc9a301760"
    checkpoints.moneropulse.net. 299 IN  TXT  "850000:00e2b557dde9fd4a9e2e3dd7ddac962f5ca475eb1095bc50aa757fd1218ab0a5"
    checkpoints.moneropulse.net. 299 IN  TXT  "900000:d9958d0e7dcf91a5a7b11de225927bf7efc6eb26240315ce12372be902cc1337"
    checkpoints.moneropulse.net. 299 IN  TXT  "913193:5292d5d56f6ba4de33a58d9a34d263e2cb3c6fee0aed2286fd4ac7f36d53c85f"
    checkpoints.moneropulse.net. 299 IN  TXT  "913269:f8302e6b8ba1c49aad9a854b8d6c79d8272c6239dcbba5a75ed0784c1d4f56a1"
    checkpoints.moneropulse.net. 299 IN  TXT  "350000:74da79f6a136969abd6364bd3d37af273c408d6471e8ab598e80569b42415f86"
    checkpoints.moneropulse.net. 299 IN  TXT  "400000:1b2b0e7a30e59691491529a3d506d1ba3d6052d0f6b52198b7330b28a6f1b6ac"
    checkpoints.moneropulse.net. 299 IN  TXT  "500000:2428f0dbe49796be05ed81b347f53e1f7f44aed0abf641446ec2b94cae066b02"
    checkpoints.moneropulse.net. 299 IN  TXT  "600000:f5828ebf7d7d1cb61762c4dfe3ccf4ecab2e1aad23e8113668d981713b7a54c5"
    checkpoints.moneropulse.net. 299 IN  TXT  "700000:12be9b3d210b93f574d2526abb9c1ab2a881b479131fd0d4f7dac93875f503cd"
    checkpoints.moneropulse.net. 299 IN  TXT  "300000:0c1cd46df6ccff90ec4ab493281f2583c344cd62216c427628990fe9db1bb8b6"
    checkpoints.moneropulse.net. 299 IN  RRSIG  TXT 13 3 300 20180922151845 20180920131845 35273 moneropulse.net. 8CyqtsM2f9o6OHZYqtGPVf+8gcFM+eUyoMi29LlkcLtK1AXbZlKqCcdN NvdvB+4OzepmpTanSc+TbLWbz/sIzA==

Please note the DNSSEC signature entry at the end.

The checkpoints are mirrored on several DNS servers:

Mainnet:

    checkpoints.moneropulse.se
    checkpoints.moneropulse.org
    checkpoints.moneropulse.net
    checkpoints.moneropulse.co

Stagenet:

    stagenetpoints.moneropulse.se
    stagenetpoints.moneropulse.org
    stagenetpoints.moneropulse.net
    stagenetpoints.moneropulse.co

Testnet:

    testpoints.moneropulse.se
    testpoints.moneropulse.org
    testpoints.moneropulse.net
    testpoints.moneropulse.co

## MoneroPulse as attack vector

It is worth noting that MoneroPulse does not produce blocks and cannot split the chain on its own. It only suggests the valid fork.

Should MoneroPulse got entirely compromised, attacker could stop all auto-enforcing nodes from advancing, by feeding them with the fake checkpoint. This is partially mitigated by DNSSEC and by operating multiple domains. Monero expects checkpoints are consistent across domains. Thus, compromising a single domain or registrar should not lead to any disruption.

MoneroPulse also increases the say of its operators in case of possible contentious hard forks. While well intended, this effectively centralizes more power in hands of core developers, or whomever is at the time running MoneroPulse infrastructure.

### Who are MoneroPulse operators?

MoneroPulse is operated by selected core developers.

## Fixing "WARNING: no two valid MoneroPulse DNS checkpoint records were received"

This means DNS server you are using does not ackonwledge the +dnssec flag necessary for securely query for checkpoints.

By default, your operating system will use DNS server provided by your Internet Service Provider.

To fix this warning, change your DNS server either system-wide in your network configuration, or specifically for `monerod`.

Many people find Google's or Cloudflare's DNS servers superior to those offered by their ISPs.

Using Google DNS:

    DNS_PUBLIC=tcp://8.8.8.8 ./monerod

Using Cloudflare DNS:

    DNS_PUBLIC=tcp://1.1.1.1 ./monerod


## Reference

* [StackExchange answer](https://monero.stackexchange.com/questions/679/what-is-moneropulse?noredirect=1&lq=1)
* [Reddit answer](https://www.reddit.com/r/Monero/comments/419qdd/p2p4warning_no_two_valid_moneropulse_dns/)
* [Monero source code](https://github.com/monero-project/monero/blob/ff7dc087ae5f7de162131cea9dbcf8eac7c126a1/src/checkpoints/checkpoints.cpp)
