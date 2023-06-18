---
title: Multisignature
---
# Multisignature

In cryptocurrencies, multisig feature allows to sign a transaction with more than one private key. Funds protected with multisig can only be spent by signing with M-of-N keys.

Example use cases:

* shared account (1-of-2; both husband and wife individually have full access to their funds)
* consensus account (2-of-2; both husband and wife must agree to spend their funds)
* threshold account (2-of-3; an escrow service is involved as an independent 3rd party, to co-sign with either the seller, or with the buyer, if seller and buyer do not agree)
* secure account (2-of-3; a single owner controls all 3 keys but secures them via a different means to diversify risks)
* arbitrary threshold account (M-of-N; some cryptocurrencies provide full flexibility on the number of signers)

## Monero's multisig design

Monero doesn't directly implement multisignatures (at least not in a classical sense). Monero emulates the feature by secret splitting.

Transactions are still signed with a single spend key. The spend key is a sum of all N private keys. The rationale for such design is to decouple multisig from ring signatures.

Let's consider the 2-of-3 scheme. We have 3 participants. Each participant is granted exactly 2 private keys in a way that pairs do not repeat between participants. This way any 2 participants together have all 3 private keys required to create the private spend key.

Multi-signing is a wallet-level feature, and there is no separate multisig address type. This means there is no way to learn from the blockchain which transactions were created using multiple signatures.

After multisig wallet setup every participant ends up knowing the public address and private view key. This is necessary for participants to recognize and decipher transactions they are supposed to co-sign.

## Multisig wallet setup

Multisig is currently only available via the Command Line Interface (CLI). This tutorial will assume you have some familiarity with the CLI before beginning.

In this example we will use a 2-of-3 multisig scheme, as it generalizes well.

Initially, while you're becoming familiar with multisig, it's suggested you begin by using **stagenet**, such that no valuable Monero are lost.

If you're not already familiar with stagenet, it is a separate, but functionally identical instance of the Monero network, created for testing purposes. To use it you simply add the --stagenet flag when creating and running your stagenet wallet. 

### 1: Create a new wallet

To begin you will need to create a new wallet. Multisig cannot be applied to a wallet that has previously received funds.

First you create a new wallet. The below the code assumes you're using a remote node, but using a local node is ideal:

```
./monero-wallet-cli --stagenet --daemon-address address-URL  # Create your wallet
```

In the above, replace address-URL with the actual URL that you want to connect to. At the time of writing, a list of remote nodes can be found at: [monero.fail](https://monero.fail). The default view shows mainnet servers, so make sure to filter by stagenet servers first.

Next, **enable** multisig via:

```
set enable-multisig-experimental 1
```

If you don't set this flag, then try to issue the first command, you will see:

```
Error: Multisig is disabled.
Error: Multisig is an experimental feature and may have bugs. Things that could go wrong include: funds sent to a multisig wallet can't be spent at all, can only be spent with the participation of a malicious group member, or can be stolen by a malicious group member.
Error: You can enable it with:
Error:   set enable-multisig-experimental 1
```

This warning message is there to let people know that Multisig is still an experimental feature and may have bugs. You can [read more](#warning) about this message below.

**Recommendation:** By default the CLI applies a screen timeout of 90 seconds. After which, you will be asked to input your password to continue using the wallet. Unfortunately, once the wallet times out, it interrupts the multisig creation process.

To extend the timeout to 10 minutes, use the command:

```
set inactivity-lock-timeout 600
```

To disable the timeout entirely (for this session only), use the command:

```
set inactivity-lock-timeout 0
```

### 2: prepare_multisig

To begin, every participant independently generates **initialization data**, which is **not** an address.

Participants then send their initialization data manually to all other participants over a secure channel. 

However, if you're creating the multisig wallet without external participants, then you simply transfer the data between terminal windows.

Begin by using the command:

```
prepare_multisig
```

**Note:** if you try to use this command on a wallet that has already been used, you will see the error message:

```
Error: This wallet has been used before, please use a new wallet to create a multisig wallet
```

After prepare_multisig you will see a message that looks similar to the below:

```
MultisigxV2R1C9Bd2LNS9oDXLwDWbVbWc53nfUJpFnQqPDDtHksVVrY33DADgnhKetL5Swgk477uP1AENAy2pz11zW73NGqZojTai2TSDyARK3QR8uVt1t26oW21mFdZtd8iuNqTPBjuCc2q9jaRzqUG75rXtnn8eD5DwJX6NaMP63o2n2fta7dXZcpM
Send this multisig info to all other participants, then use make_multisig <threshold> <info1> [<info2>...] with others' multisig info
This includes the PRIVATE view key, so needs to be disclosed only to that multisig wallet's participants 
```

The long string that begins Multisig is important, and will be shared with the other wallets in the next step.

Before you move to the next step, you'll want to run the **prepare_multisig** command on the rest of the wallets you want to use in your multisig setup. For example, 2 more if you're doing a 2/3 multisig.

### 3: make_multisig

This command is where you set the threshold for your multisig wallet and then pass the initialization data from the other participants. The initialization data is the long string beginning **Multisig** mentioned above.

For example, if you're doing a 2 of 2 multisig, then your threshold is 2 and you'll pass 1 piece of data. If you're doing a 3 of 5 multisig, then your threshold is 3 and you'll pass 4 pieces of data.

Continuing with our 2/3 multisig example, you would then type into your CLI:

```
make_multisig 2 <data1> <data2>
```

Which in practice would look similar to:

```
make_multisig 2 MultisigxV2R1MyhE1hgED7AFwytsfW44s7G4abNHFKhwfJH9kvB2Q7xNVBdJAyY9gm7eJkHVRo1T3Hb6PeYsyzUrqQsmpBByDq4iRywanpRLxLN2JKuvKPBDayAywAHBzGxdnGiyoXhLdnZiU6Azy3VNocwH1jgfFvYDUUCo7H8mFacnLUFVLC8LjEfz MultisigxV2R1CzwgGBTPxb51nWfvLg7mYPRBnqDgppZq85E745qR1NvGNCLBaHSCmUQ4JRb41tW9PUerAgz9pKHJ5NpKgE6vsZnpLJkCP3u4zwcXJW3UHjABc446jdQegP1hyHnGgJpah8RmdeLcLCAqa6WXgt3xJoz6QF5o66tnCiyJkYyebjWeXV2y
```

If you were doing a 3/5 multisig, you'd instead run:

```
make_multisig 3 <data1> <data2> <data3> <data4>
```

Once this command is run on each wallet, you will then receive a second round of initialization data, that looks similar to:

```
Another step is needed
MultisigxV2Rn1LVYohry597ZfzPhWnuL6qcueBNdq4ivrP7zqDm4W5eKBhxgdcERcSvFs8F5EkLSuYFyKfBEeh4Fui6xHTeRqb7cWshXY96WruxMaSxMafTdPn48ko52e8UHvA4kWwpuPidBYg5dyVWoQLWgqCMDANxWnjhenw6HTwpT96yB8n1a16oQEYyQWg66r2sZHi9RMmivTsihnMq66rTHKPKKau1SHButDwQ
```

**Side note:** If you make a mistake, such as inputting the wrong threshold or missing out some initialization data, there isn't an undo function. That individual wallet will get created incorrectly, and you will need to re-do it.

### 4: exchange_multisig_keys

With the threshold established in the prior command, the exchange_multisig_keys command simply takes the init data from the other participants, no threshold parameter needed.

For example:

```
exchange_multisig_keys <data1> <data2>
```

It is either run once or twice in total.

Once if your wallet has the same threshold as the total number of participants, e.g. 2 of 2.

Twice if you have a different threshold, e.g. 2 of 3.

Continuing our 2/3 multisig example, after inputting the above exchange_multisig_keys command, we would then see:

```
Another step is needed
MultisigxV2Rn1WCSNqbsjuTXaPVfFsk3ekFF444yFN5PMCXcQHv1Pv794ZdkDZRnfVGgeP5JwpysR3ingQtQMMnmQDEXnP4qgdnh3SU2NXvfe7kMaSxMafTdPn48ko52e8UHvA4kWwpuPidBYg5JdJwdEAh8Ud7kBFX34zP33ZBbrYXcQbQKTcM3XQ8AEP8bVXHVqQSGzkAkjZRp3H63k6ZSXSYdH9WaC9pdr9FV3tx
Send this multisig info to all other participants, then use exchange_multisig_keys <info1> [<info2>...] with others' multisig info
```

Then for a second, and last time, we input:

```
exchange_multisig_keys <data1> <data2>
```

And we receive back:

```
Multisig wallet has been successfully created. Current wallet type: 2/3
Multisig address: 56MD1L4zky3bFXDQb9qvSx7PDbg8F4x1HgPrFNrDnGnYDqFZcWGswWc1p2moFa1F44ccJniY9Wkzk6urkJbEDvubHqYtkcs

```

This results in a wallet **public address** and **private view key** to be known for all participants.

So if you're the sole participant in the multisig setup, you'll know it has worked when you see the same multisig address across all the wallets.

## Receiving funds

### 1: Funding the Multisig Account

Addresses created by a multisig wallet operate the same as normal, non-multisig addresses. This means:

- Each wallet can create subaddresses independently, no collaboration needed.

- All participants can see incoming funds as they share the private view key.

The main difference comes when trying to spend funds from a multisig wallet. See the **Spending Funds** section below for how to do this.

### 2: Check Account Balance

To check the account balance, open one of the multisig wallets and type the **refresh** command.

This will refresh the wallet and display your balance. The output will look similar to the below, but with a different amount:

```
Starting refresh...
Refresh done, blocks received: 0                                
Currently selected account: [0] Primary account
Tag: (No tag assigned)
Balance: 10.000000000000, unlocked balance: 10.000000000000 (Some owned outputs have partial key images - import_multisig_info needed)
```

If you see that last sentence:
```
(Some owned outputs have partial key images - import_multisig_info needed)
```
This means that you haven't synchronized your wallet with the threshold amount of wallets needed (1 other in the case of 2/3 multisig) for the outputs to become spendable.

You can also use the command **show_transfers** to display a list of funds received and the transfer date, with the output looking similar to:

```
 1263592     in unlocked       2023-01-09 21:13:59      10.000000000000 c5a3eec347401b1e263f45577b840c036568aa841eb2ebc6eb1332c1bc281f28 0000000000000000 0.000000000000 76Matb:10.000000000000 1 - 
```

## Spending funds

Prior to explaining the process for spending multisig funds, it may help to have a high level overview of the process. There are two core steps:

1) **First, the sharing of partial key-images** - At minimum, the spender needs to get a partial key image from the people (1 or more) who will sign the transaction with him later. They need to export a file and share it with the future spender, who then imports the file to their wallet.

2) **Second, creating, signing & submitting the transaction** - A transfer is created, written to file, and then this file needs to be signed by the co-signers, before it can lastly be submitted to the network.


### Preparation for spending

**Preparation Step 1: Export partial key image**

Prior to constructing a transaction, the spender will need to get a partial key image from the wallet or wallet**s** which will later co-sign the transaction.

In our 2/3 multisig example, the spender needs to get 1 partial key image, because the threshold is 2.

The wallet that will provide the partial key image needs to enter the command:

```
export_multisig_info key1
```

Where **key1** can be any filename. The output will then be:

```
Multisig info exported to key1
```

The file will be saved to the present working directory in the terminal.

It then needs to be shared with the wallet which will create the spending transaction.

**Preparation Step 2: Import partial key image**

Now that the spending wallet has the partial key image it can import it. Assuming the file is in the present working directory, the command would be:

```
import_multisig_info key1
```

If two or more key images were being imported, you would specify them side by side, such as:

```
import_multisig_info key1 key2 key3
```

After issuing that command, the wallet will display how many new inputs it has verified, for example if 1 output is verified:

```
Height 1263592, txid <c5a3eec347401b1e263f45577b840c036568aa841eb2ebc6eb1332c1bc281f28>, 10.000000000000, idx 0/1
Multisig info imported. Number of outputs updated: 1
```

### Spending

**Spending Step 1 - Create Unsigned Transaction**

Creating a new transaction can be done by **any** of the multisig wallets. However, to avoid weird things from happening, only do it for 1 transaction at a time. If anything weird happens, re-do steps 1 & 2 again to fix.

The wallet initiating the transfer should create a transfer, as per the normal CLI transfer process:

```
transfer <address> <amount>
```

So for example:

```
[wallet 56MD1L]: transfer 72Qv1pqug5rX1qS77Bj9C4XBbrvdYRJLM6769bseDytqVZWV2iQxGDnZ85KmubdiCQgtZjeb4fPdUNGq8Foae5b1Bo77T64 5
Wallet password: 

Transaction 1/1:
Spending from address index 1
Sending 5.000000000000.  The transaction fee is 0.000167640000

Is this okay?  (Y/Yes/N/No): Yes
```

The output will look like:

```
Unsigned transaction(s) successfully written to file: multisig_monero_tx
```

The present working directory will now contain a file named **multisig_monero_tx**, which should then be shared with the co-signer.


**Spending Step 2 - Sign Transaction**

The wallet that has been chosen to co-sign the transaction now needs to run the command:

```
sign_multisig multisig_monero_tx
```

Which will result in an output similar to:

```
Loaded 1 transactions, for 10.000000000000, fee 0.000167640000, sending 5.000000000000 to 72Qv1pqug5rX1qS77Bj9C4XBbrvdYRJLM6769bseDytqVZWV2iQxGDnZ85KmubdiCQgtZjeb4fPdUNGq8Foae5b1Bo77T64, 4.999832360000 change to 56MD1L4zky3bFXDQb9qvSx7PDbg8F4x1HgPrFNrDnGnYDqFZcWGswWc1p2moFa1F44ccJniY9Wkzk6urkJbEDvubHqYtkcs, with min ring size 16, dummy encrypted payment ID. Is this okay?  (Y/Yes/N/No): y
Transaction successfully signed to file multisig_monero_tx, txid 82132a4302188b15c87916c05df79755dafb9ada78c39164937c246a4c2dee0a
It may be relayed to the network with submit_multisig
```

**Note:**
Once you synchronize the partial key images, there is a certain time window by which you can create and send your transaction. I'm unsure exactly how long the time window is. If you leave it too long you will see the output:

```
Error: Multisig error: This signature was made with stale data: export fresh multisig data, which other participants must then use
```

The solution is to re-do steps 1 and 2 of the preparation for sending. Once that's completed, you can return to the sending steps.

**Spending Step 3 - Submit Transaction**

Now that your transaction has been co-signed, it is possible to submit it. You can do this from any of the wallets, as long as they have the co-signed multisig_monero_tx file. Using the command:

```
 submit_multisig multisig_monero_tx
```

You will then see an output similar to:

```
[wallet 56MD1L]: submit_multisig multisig_monero_tx
Wallet password: 
Loaded 1 transactions, for 10.000000000000, fee 0.000167640000, sending 5.000000000000 to 72Qv1pqug5rX1qS77Bj9C4XBbrvdYRJLM6769bseDytqVZWV2iQxGDnZ85KmubdiCQgtZjeb4fPdUNGq8Foae5b1Bo77T64, 4.999832360000 change to 56MD1L4zky3bFXDQb9qvSx7PDbg8F4x1HgPrFNrDnGnYDqFZcWGswWc1p2moFa1F44ccJniY9Wkzk6urkJbEDvubHqYtkcs, with min ring size 16, dummy encrypted payment ID. Is this okay?  (Y/Yes/N/No): y
Transaction successfully submitted, transaction <82132a4302188b15c87916c05df79755dafb9ada78c39164937c246a4c2dee0a>
You can check its status by using the `show_transfers` command.
```

The transaction has now been broadcast to the network. If you want to create another one, you will need to go back to the preparation stage and re-sync the partial key images.

## Mnemonic Seeds

With a regular wallet is it possible to create a mnemonic seed that you can backup, and later use to recreate the wallet.

Fortunately, multisig wallets have the same feature. The only difference is that the seed is a **long string** of letters and numbers, rather than a set of dictionary words. Unfortunately, it needs to encode too much data to fit neatly into the regular mnemonic seed dictionary output.

To access your wallet seed, open the wallet within the CLI and type **seed**. You will see an output similar to this:

```
NOTE: the following string can be used to recover access to your wallet. Write them down and store them somewhere safe and secure. Please do not store them in your email or on file storage services outside of your immediate control.

020000000300000051512b513c603a023df44e2400ad58b3f4751e2dcba44b1d4316be5adffd330b773b3818a07ab6ccc4ffcee1feed5526296b52f5ea0844eb8562cb3e63e8154c5c91f0cfd102a830d8692cec64e662f7ffac4cb82dd950c891a92a22dc80930ab78dd01a1ec7ed04d90eff530d2af9d4e40670576863492357727c6615620e95d2e962632518d958040b710474a4c944cb3a286e3fe9ad0b1d651ff6d8b4c50c81a74423650210bbcb7b427435e90b3eb494cb108bd148cab56e5352303d55070d35e703217b7b051a96af50c1c912bc372f65a4ffa93631ae009a544106bd5dfcf477573387f159ce6cae10fb2ffe63f55e75ed94d0893ece9b67b0a1cf0fc2034ca04ef7c862494a13237bfaa8e822f824ea59de561353f2ac01fbc279280c
```

**Note:** This seed will only recreate the individual wallet it is created from. Each wallet would need to be backed up separately.

## <a id="warning"></a>About the experimental feature warning message

Prior to a pull request in mid 2022 ([PR #8149](https://github.com/monero-project/monero/pull/8149)) Monero's multisig feature had some known bugs.

PR #8149 fixed these issues, including findings identified by an [independent audit](https://github.com/monero-project/monero/pull/8149#issuecomment-1167912258) of multisig.

However, there is still a possibility of a yet **unknown** bug that would allow a malicious group member, in a worst-case scenario, to acquire all funds within the multisig wallet.

Two potential steps to get Monero's multisig implementation further tested and more secure would be the completion of a formal specification and a third-party audit. However, there is currently no timeline for this.

It's worth noting that the risks implied by this unknown bug scenario depend upon the individual use-case. For example:

a) If one planned to use multisig in collaboration with other people, then this risk is there.

b) If one planned to use multisig solely to shard a cold wallet, and store it in multiple locations, then this risk may be lessened. On the basis that it requires two coincidences to come together: 

i) A malicious actor who has the capability to exploit an unknown bug in Monero's multisig.

ii) This malicious actor is then able to access one of the, presumably secured, multisig wallets.

However, if an exploit was made public knowledge, then the risk increases, because the attacker no longer needs to figure out the exploit, they simply need to locate your wallet and then implement the public exploit.

Thus, if one took the risk to use multisig in method b), they would be prudent to stay up to date on multisig development. Such that they would learn quickly if such an exploit was discovered.

## References

The below guides are very detailed, and were formative in the creation of this document. Note that they are both a little out of date, as a few of the CLI commands have been updated in the interim.

* [https://monero.stackexchange.com/questions/5646/how-to-use-monero-multisignature-wallets-2-2-2-3](https://monero.stackexchange.com/questions/5646/how-to-use-monero-multisignature-wallets-2-2-2-3)
* [https://taiga.getmonero.org/project/rbrunner7-really-simple-multisig-transactions/wiki/23-multisig-in-cli-wallet](https://taiga.getmonero.org/project/rbrunner7-really-simple-multisig-transactions/wiki/23-multisig-in-cli-wallet)