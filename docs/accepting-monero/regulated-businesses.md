---
title: Accepting Monero as a Payment for Regulated Businesses | Monero Documentation
---
# Accepting Monero as a Payment for Regulated Businesses 

## Applicability

Relevant if your business is official but not welcome by traditional payment processors.
This is typically the case for heavily regulated or controversial businesses
that may be legal in some jurisdictions and illegal in others.

## Non-custodial Monero Payment Processors

The easiest way is to connect one of existing **non-custodial**
cryptocurrency payment processors supporting Monero.

The non-custodial means Monero will go directly to your wallet.
The payment processor role is limited to observing the network
and notifying you on received payments. They also tend to support
the user interface piece.

Because money does not flow through them
they are **much more flexible**
with regard to what businesses they support.

They may also market themselves as "watch towers" or "payment notification" services,
instead of "payment processors".

### Value they provide

* **Pricing goods in your currency** - nothing changes in your pricing, the amount of Monero due is dynamically calculated by payment processor

* **No installation** - you don't need to install and host Monero on your server; this is all ran by the payment processor

* **Plugins** for all major e-commerce platforms for easy integration (WooCommerce, OpenCart, Shopify, PrestaShop, Magento, ...)

* **User interface** for requesting payments is provided by payment processor

* (Potentially) **high-level invoice API** - if you don't use a plugin then you can integrate against convenient "create invoice request" / "invoice paid callback" API instead of directly with Monero

### Assumptions and limitations

* **You need a Monero wallet** because you will receive Monero directly w/o intermediaries;
  you may start with very easy Monero wallet on your smartphone
  and gradually move towards safer options like hardware wallet
  when your holdings increase
* **You need to safeguard and sell received Monero** - you are responsible for handling received Monero.
  This is not hard but does require some basic learning, doing a one-time backup of your secret seed,
  and optionally learning how to sell Monero for your preferred national currency
* **You need to share your "secret view key" with payment processor** - this is easy and simply means the payment processor will know all your incoming payments
* **Volatility risk** - you do need to embrace volatility risk. The mitigation would be to regularly convert most of received moneros to less risky assets like Bitcoin and maybe even your government currency of choice.


## MyCryptoCheckout

Established multi-currency, non-custodial payment processor for Wordpress based e-commerce.

[https://mycryptocheckout.com/](https://mycryptocheckout.com/)

Pros:

* Pretty established with [10+ millions of USD in processed transactions](https://mycryptocheckout.com/stats/) 
* Supports all major cryptocurrencies:
    * Bitcoin (BTC), Monero (XMR), Ethereum (ETH), Litecoin (LTC), Ripple (XRP), ...
* Free tier of up to 3 transactions per month - excellent way to test the service and the real demand
* Very good [documentation](https://mycryptocheckout.com/doc/installation/) and [demo](https://wpdemo.mycryptocheckout.com/)
* Low, fixed yearly fee

Cons:

* Only for Wordpress / WooCommerce / EasyDigitalDownloads
  * No generic API for custom integration

## MoneroIntegrations

Open source e-commerce plugins to easily process Monero transactions.

[https://monerointegrations.com/](https://monerointegrations.com/)

Pros:

* Developed by Monero expert SirHack and contributors
* Supports WooCommerce, PrestaShop, WHMCS
* Open source
* Free of charge

Cons:

* Monero specific; you would need to separately add plugins for other cryptocurrencies
* Relies on external block explorer [https://xmrchain.net/](https://xmrchain.net/) to recognize incoming payments
    * This is necessary to avoid installing anything on merchant's server
    * However, privacy and reliability guarantees offered by xmrchain.net are unclear (it is a free community service)
    * Alternatively, merchant may install and connect his own Monero daemon (`monero-wallet-rpc`); this is not very hard but requires a root server access and a couple of hours of technical person

## Comparison Matrix

We prepared a comparison of available venues to accept Monero, as of 2019-01-28:

[<img src="/images/sheets-icon.png" width="20px" height="20px" style="margin-bottom: -4px;" /> Accepting Monero as a Business - Comparison Matrix](/r/accepting-monero-comparison-matrix)
