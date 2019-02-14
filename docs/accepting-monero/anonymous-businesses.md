---
title: Accepting Monero as a Payment for Anonymous Businesses | Monero Documentation
---
# Accepting Monero as a Payment for Anonymous Businesses 

!!! Danger
    Monero is just one piece of the complex anonymity puzzle.
    Please be reminded general opsec is critical for maintaining your anonymity.

!!! Danger
    Monero does not hide your IP address. As with most services and applications,
    you are expected to hide your IP address via a network level solutions like [Tor](https://www.torproject.org/).

## Applicability

Relevant if you run your business anonymously - in a truly cypherpunk spirit - and want to accept Monero in an automated way.

If used correctly, Monero provides the highest payments privacy available.

## You cannot outsource payment processing

You cannot outsource payment processing for two reasons:

* by using an intermediary you would allow it to know all your transactions,
  which obviously isn't a good idea for anonymous business;
  this also concerns the non-custodial watch-only payment processors (they require your private view key to operate)

* payment processors are unlikely to allow for your anonymous business;
  even if there is no upfront verification you would be doing against their ToS
  which would allow them to cut you off w/o notice and freeze the in-transit funds
  
## Assumptions

* You will need a server with root access

* You will need to run Monero wallet daemon `monero-wallet-rpc` on the server; this provides API for your application

* As with all services you are running, you need to ensure Monero daemons only connect through Tor

* You will need to integrate your application with `monero-wallet-rpc` HTTP/JSON API

* You will need to develop a little frontend piece for the payment flow;
  show payment request with address and optionally a qr code;
  then show subsequent payment statuses
  
* Finally, you will probably need to sell some of your moneros in a way decoupled from your business

## Guide

### Prerequisites 

* You do have a root server with all outgoing network traffic routed via Tor.
Running anonymous business you most likely have this already in place.
If not, you may start with [anonymous root servers](https://www.privacytools.io/#host)
and then learn how to [route all outgoing network traffic via Tor](https://www.whonix.org/wiki/Onion_Services).
* You do have basic understanding of how [Bitcoin](https://en.bitcoin.it/) and [Monero](https://monero.how) work.
If you don't we recommend going first through the Bitcoin wiki to understand decentralized cryptocurrencies in general
and then going after Monero specific knowledge.
* You did create a Monero wallet. You played with it on [stagenet](/infrastructure/networks/#stagenet) to get comfortable.
We recommend [official Monero desktop GUI wallet](/interacting/download-monero-binaries) and connecting over Tor.
To quickly connect via Tor first make sure the tor daemon is running `systemctl status tor.service` and then run the wallet wrapped with torsocks `DNS_PUBLIC=tcp torsocks ./monero-wallet-gui`.
In wallet settings specify remote node `zdhkwneu7lfaum2p.onion:18099`.
For any serious work replace torsocks with [Whonix](https://www.whonix.org/)-like solution.

### Install `monero-wallet-rpc` on the server

There are no well established packages for installing Monero on the server.
The way to go is to [download official binaries](/interacting/download-monero-binaries),
[verify the signature](/interacting/verify-monero-binaries),
and move them to your preferred directory.

### Create Monero configuration file

Create configuration file `/etc/monero.conf` based on the [reference](/interacting/monero-config-file).

### Connect to open full node
                   
Pick a random community [Monero open full node](https://node.pwned.systems/).

Configure it by adding a line to `/etc/monero.conf`:

    daemon-address=host:port

### Create a view-only wallet

View only wallet can recognize incoming payments but cannot spend any funds.
This is recommended unless you also need automatic spending on your platform.

    monero-wallet-cli --config-file=/etc/monero.conf --generate-from-view-key MyViewOnlyWallet
    
    # when asked paste your main address and private view key
    # you can show them in your desktop/mobile wallet software
                   
### Test with CLI wallet

    monero-wallet-cli --config-file=/etc/monero.conf MyViewOnlyWallet
    
    # type
    help    
    # then play around, send some funds and ensure they come up here
    
### Accept programmatically

### Calculate order total in Monero

The goods or services you offer are probably priced in fiat currency like USD or EUR.
You will probably want to auto-convert order total amount into XMR.

Our suggestion is to use at least three independent USD/XMR price sources and then take the median value.

Of course, be careful to connect via Tor for price requests. 

### Generate a new subaddress

First, read about [subaddresses](/public-address/subaddress).

Use `get_address` API call to generate subsequent addresses.

### 

See this guide:

https://ww.getmonero.org/get-started/accepting/
    

### Churn and Sell Monero

Before doing anything with the funds is its probably a good idea to churn your wallet.
Churning is sending entire balance back to yourself. This increases privacy because it furthers
the distance of the coins from the source.

For selling cryptocurrency, we highly recommend anonymous (no-KYC) decentralized exchange [Bisq](https://bisq.network/).
Obviously, when selling for fiat (USD, EUR, etc), the fiat sender (and potentially Bisq arbiter) will both learn your bank account number and related transfer data.
