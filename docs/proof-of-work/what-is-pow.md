---
title: Proof of Work
---
# Proof of Work

> Proof of work is a way to legitimize untrusted party

### What exactly is proof of work?

Proof of work is a cryptographic proof that untrusted party committed significant computational resources to solve artificial problem.

Technically, the "proof" is simply a solution to the problem at hand.

### It's all about legitimizing untrusted party

How an untrusted party on the Internet could earn any level of your trust?

It can prove its commitment by solving agreed computationally hard problem.

For example, by requiring untrusted party to perform a hard computation before you accept their connection, you limit connections only to "committed" parties.

In another example, you could require PoW to be attached to incoming e-mails to make spam prohibitively expensive.

### Work must be otherwise useless

The work on and solution to "computationally hard problem" cannot be useful in any other way than to prove the commitment.

If the work is useful elsewhere then it doesn't prove commitment to you.

The problem must be artificial. Otherwise incentives are skewed and the whole scheme breaks.

### Strong asymmetry

The requirement for proof of work scheme is strong asymmetry for work vs verification resources.

The work must be arbitrarily hard. At the same time proof verification must remain dirt cheap (in terms of computational resources).  

Cheap verification is critical because at this stage we are dealing with potentially huge number of untrusted parties,
who could DoS the verifier by submitting invalid proofs. Such proofs should be trivial to discard.
