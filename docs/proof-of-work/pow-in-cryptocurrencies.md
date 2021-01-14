---
title: Proof of Work in Cryptocurrencies
---
# Proof of Work in Cryptocurrencies

> Proof of work is a Sybil protection mechanism

## PoW protects against Sybil attack

In decentralized cryptocurrencies **untrusted** actors confirm (blocks of) transactions.

If threshold voting was employed then the scheme would break immediately.
This is because nothing prevents a single actor from creating arbitrary number of pseudonyms and take over the voting.
In distributed systems this is known as Sybil attack.

Instead, cryptocurrencies employ proof of work. In the proof of work scheme,
it is not the number of actors that counts. It is the amount of committed
computational resources.

This, of course, is much harder to game.
To endanger the scheme, an attacker would have to actually control majority (>50%) of computational resources.
In practice, attacker would need this control over significant period of time.  

## PoW is a leader election mechanism

In distributed systems "leader election" is a process of establishing which node is responsible for (temporarily) coordinating the system.

In cryptocurrencies PoW is used to elect the node that "wins" the next block.

Using PoW for leader election was one of the key inventions introduced by Bitcoin. 

Competing nodes (known as "miners") work on a solution to artificial problem.
Every now and then, someone randomly finds the solution.
Chances are linearly proportional to committed computing power.

The winner uses its solution to "underwrite" the block it assembled. Only blocks with valid solutions are accepted by the network.

The winner also gets a reward for its work. The reward is a specific amount of cryptocurrency created "out of thin air" and assigned to self. The winner also gets all fees coming from transactions included in the block.

The difficulty of the PoW problem is dynamically adjusted by the network, with the goal of finding blocks with a roughly constant rate (typically, every couple of minutes).
