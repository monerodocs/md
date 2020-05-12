
https://www.reddit.com/r/Bitcoin/comments/8dr4fw/benedikt_b%C3%BCnz_bulletproofs/dxpv4q6/

Pieter Wuille on Bulletproofs:

Bulletproofs is a general technology to implement zero-knowledge proofs.

Bitcoin does not use zero-knowledge proofs. There is no way you can just "plug in" Bulletproofs anywhere in Bitcoin.

Perhaps you are talking about confidential transactions (CT). This is a technique to permit a public ledger that makes the amounts hidden while still permitting everyone to verify the balances adds up. If that sounds like magic, that's because it is.

CT rely on zero-knowledge proofs. They could use Bulletproofs. They could also use other types of zero-knowledge proofs (there are several, zk-SNARKS is the well known, each with their own trade-offs). Certainly the invention of Bulletproofs made CT more accessible.

However... CT is still very different from how Bitcoin works right now. There is no obvious way to integrate them. My best hope would be using something like an extension block, where you can have coins on the "legacy" side or coins on the confidential side - with explicit operations to move them between the two.

However, that is far from simple, and AFAIK nobody is really working on it. Much as I love this technique and would be very excited to see it in Bitcoin, it's unrealistic to think that it will be usable anytime soon there.

Every system must lack at least one of unconditional soundness and unconditional blindness. It's perfectly possible to build a system that lacks both.


Bulletproofs are a general zero-knowledge proof technique. That means that they can be used to prove any statement over secret data without revealing that data.

You could prove you know 2 numbers that add up to 7.

You could prove that you know a string whose SHA256 hash is 16c28109a2719ebd4a123db11ff966f02d814354e3dc932449484f1c5a804af4, without revealing anything else about that string.

They could be used instead of zk-SNARKs in Zero-Knowledge Contingent Payments (swapping money for solutions to a problem). These can be done without any changes to Bitcoin.

You could (in theory, read on) use them to build a blockchain similar to ZCash (which heavily relies on zk-SNARKs for proving that their ledger makes sense), but without the trusted setup procedure or novel cryptography (Bulletproofs use very conservative security assumptions). Unfortunately, Bulletproof validation is much slower than zk-SNARK validation for complex problems, so it's not really comparable.

I expect that the most interesting applications are things we haven't considered yet. They suddenly bring zero-knowledge proofs into scope for a lot of problems and protocols where it wasn't really reasonable to go through the effort of using one of the existing proof systems.

++++

I'm pretty sure that Monero is not unconditionally sound. Even if it is, after switching to Bulletproofs they won't be (Bulletproofs cannot be made unconditionally sound).

Yes, with the old rangeproof construction there was an alternative that was unconditionally sound, but not unconditionally private.

This is not possible with Bulletproofs (and theoretically impossible with anything with similar compactness; unconditionally sound proofs cannot be small).

++++

Bulletproofs are a general zero-knowledge proof technique. That means that they can be used to prove any statement over secret data without revealing that data.

You could prove you know 2 numbers that add up to 7.

You could prove that you know a string whose SHA256 hash is 16c28109a2719ebd4a123db11ff966f02d814354e3dc932449484f1c5a804af4, without revealing anything else about that string.

They could be used instead of zk-SNARKs in Zero-Knowledge Contingent Payments (swapping money for solutions to a problem). These can be done without any changes to Bitcoin.

You could (in theory, read on) use them to build a blockchain similar to ZCash (which heavily relies on zk-SNARKs for proving that their ledger makes sense), but without the trusted setup procedure or novel cryptography (Bulletproofs use very conservative security assumptions). Unfortunately, Bulletproof validation is much slower than zk-SNARK validation for complex problems, so it's not really comparable.

I expect that the most interesting applications are things we haven't considered yet. They suddenly bring zero-knowledge proofs into scope for a lot of problems and protocols where it wasn't really reasonable to go through the effort of using one of the existing proof systems.


++++

Practical zero-knowledge proofs have existed for a few years now, and ZKCPs are indeed an application of them.

Bulletproofs are just a new type of zero-knowledge proof. Compared to zk-SNARKS, they're much more conservative in their security assumptions, and don't need a complicated setup procedure before proofs can be created. On the other hand, they're also larger and slower for complicated problsms. And they don't do anything that couldn't be done before - they're just more practical depending on your requirements.
