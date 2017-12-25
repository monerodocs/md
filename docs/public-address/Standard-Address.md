# Address

Monero public address is what you publish to get paid.

An address can be generated offline and for free. It boils down to generating a large random number representing your private spending key.

Publishing your Monero address does __not__ endanger your privacy. That's because in Monero transactions go to stealth addresses which are decoupled from your public address.

There are a few types of public addresses in Monero:

* Standard address - the basic type of the address, also refered to as raw address
* Integrated address - embeds payment ID so you can learn for what you are being paid
* Subaddress - allows for organizing your funds in subaccounts within a single wallet

## Standard address

The raw address is useful for things like donations (where you don't care who donated)
or exchange withdrawals (where you know the payer out of band). Its strength is simplicity.

Monero standard address is composed of two public keys:

* public spend key
* public view key

It also contains a checksum and a "network byte" which actually identifies both the network and the address type.

Data structure ([src](https://github.com/monero-project/monero/blob/f7b9f44c1b0d53170fd7f53d37fc67648f3247a2/src/cryptonote_basic/cryptonote_basic_impl.cpp#L159)):

Index       | Size in bytes    | Description
------------|------------------|-------------------------------------------------------------
0           | 1                | identifies the network and address type; [18](https://github.com/monero-project/monero/blob/793bc973746a10883adb2f89827e223f562b9651/src/cryptonote_config.h#L149) - main chain; [53](https://github.com/monero-project/monero/blob/793bc973746a10883adb2f89827e223f562b9651/src/cryptonote_config.h#L161) - test chain
1           | 32               | public spend key
33          | 32               | public view key
65          | 4                | checksum ([Keccak-f[1600] hash](https://github.com/monero-project/monero/blob/8f1f43163a221153403a46902d026e3b72f1b3e3/src/common/base58.cpp#L261) of the previous 65 bytes, trimmed to first [4](https://github.com/monero-project/monero/blob/8f1f43163a221153403a46902d026e3b72f1b3e3/src/common/base58.cpp#L53) bytes)

It totals to 69 bytes. The bytes are then encoded ([src](https://github.com/monero-project/monero/blob/8f1f43163a221153403a46902d026e3b72f1b3e3/src/common/base58.cpp#L240)) in [Monero specific Base58](/primitives/Base58) format, resulting in a 95 chars long string. Example standard address:

`4AdUndXHHZ6cfufTMvppY6JwXNouMBzSkbLYfpAV5Usx3skxNgYeYTRj5UzqtReoS44qo9mtmXCqY45DJ852K5Jv2684Rge`

## Reference

* [https://xmr.llcoins.net/addresstests.html](https://xmr.llcoins.net/addresstests.html)
