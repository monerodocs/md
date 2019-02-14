---
title: Accepting Monero as a Payment for E-commerce and Services | Monero Documentation
---
# Accepting Monero as a Payment for Common E-commerce and Services 

## Applicability

Relevant if your business is unlikely to be cut off by regulated payment processors. This covers goods and services that are not only legal but also mostly unregulated in all major jurisdictions. Think all the usual services and goods like electronics, clothing, tickets and travel, food and groceries, books, sports and beauty products, etc.

Otherwise see [accepting Monero for regulated businesses](/accepting-monero/regulated-businesses) or [accepting Monero for anonymous businesses](/accepting-monero/regulated-businesses).

## Monero Payment Processors

The easiest way is to connect one of existing cryptocurrency payment processors supporting Monero.

Using external payment processor makes the whole process easy and conceptually similar to connecting PayPal.

Here are some pros:

* **Pricing goods in your currency** - nothing changes in your pricing, the amount of Monero due is dynamically calculated by payment processor

* **No need to handle Monero** - technical complexity is entirely outsourced to payment processor; you do not need to install Monero on the server; you also do not need a Monero wallet

* **No Monero volatility risk** - some payment processors will offer immediate conversion of your Monero to preferred national currency (through partner services) or to Bitcoin

* **USD/EUR withdrawals** - some payment processors will offer a wire, SEPA or PayPal withdrawals of your balance (through partner services)

* **Plugins** for all major e-commerce platforms for easy integration (WooCommerce, OpenCart, Shopify, PrestaShop, Magento, ...)

* **High-level invoice API** - if you don't want use a plugin then you can integrate against convenient "create invoice request" / "invoice paid callback" API offered by payment processor


### GloBee

[https://globee.com/](https://globee.com/)

Pros:

* Payment processor originally founded by a prolific Monero core team member
* Supports all major cryptocurrencies:
    * Bitcoin (BTC), Monero (XMR), Ethereum (ETH), Litecoin (LTC), Ripple (XRP), ...
* Supports Lightning Network
* Plugins for most e-commerce platforms
* First class Bitcoin and Monero support + no shitcoin distractions
* Instant registration w/o formal review process
* Clean and pleasant user interface

Cons:

* USD/EUR settlements and withdrawals are not directly supported. Instead there is an option to auto-forward to Luno, Uphold or BitPay. The business must first register in one of these services and go through their KYC and acceptance process
* Only established in late 2017
* Unclear development pace

Informational:

* Undisclosed legal entity and jurisdiction. This is perfectly fine in principle and inline with the cypherpunk spirit. However, it makes reasoning about compliance and accounting implications harder for the clients
* Run mostly out of Johannesburg / South Africa


### CoinPayments

[https://coinpayments.net/](https://coinpayments.net/) | Onion: [https://coinpaymtstgtibr.onion/](https://coinpaymtstgtibr.onion/)

Pros:

* Well established, long running and actively developed cryptocurrency payment processor
* Supports all major cryptocurrencies:
    * Bitcoin (BTC), Monero (XMR), Ethereum (ETH), Litecoin (LTC), Ripple (XRP), ...
* Plugins for all e-commerce platforms
* Cayman Islands + Estonian jurisdiction
* Official Tor Onion website

Cons:

* USD/EUR settlements and withdrawals are not possible for Monero. They are supported for Bitcoin and a few selected cryptocurrencies but Monero is not among them as of Jan 2019. USD/EUR settlements and withdrawals are implemented through Coinbase, Wyre, and Coinmotion. The business must first register in one of these services and go through their KYC and acceptance process
* Monero is just one of hundreds supported coins. The support could be potentially outdated or dropped in the future. The company does not seem to participate in Monero community
* The user interface and UX is somewhat messy and clunky
* Shitcoin junky. With over 1000 coins and tokens supported, the payment processing technology is very unlikely to be ultra reliable


### Other

Other payment processors with Monero support are [CDPay](https://www.cdpay.eu/) and [Bitpro](https://bitpro.cc/).

## Comparison Matrix

We prepared a comparison of available venues to accept Monero, as of 2019-01-28:

[<img src="/images/sheets-icon.png" width="20px" height="20px" style="margin-bottom: -4px;" /> Accepting Monero as a Business - Comparison Matrix](/r/accepting-monero-comparison-matrix)

## Verdict

We recommend to start with [GloBee](https://globee.com/) due to:
 
* best Bitcoin and Monero experience
* working venue to cash out USD/EUR after accepting Monero

## E-commerce

Payment processors will offer plugins to major e-commerce platforms including:

* WooCommerce
* OpenCart
* Shopify
* Magento
* PrestaShop
* WHMCS
