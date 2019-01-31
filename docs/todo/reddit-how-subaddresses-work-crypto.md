https://www.reddit.com/r/Monero/comments/akz211/help_with_subaddresses/


I can't speak about the code level, but the best places to look for clarification on how subaddresses work may be:

    https://github.com/monero-project/monero/pull/2056
    https://www.getmonero.org/library/Zero-to-Monero-1-0-0.pdf (section 5.3)

This may be a bit more than you were looking for, but I am hoping the explanation will answer some of your questions.

In a regular traditional Monero address the Bob gives out a pair (A, B) := (a*G, b*G), where A is the public viewkey, B is the public spend key, and a and b are the corresponding private keys. Then Alice samples random r, computes 
the one-time address P := H(r*A)*G + B, and sends money to it. In the transaction, Alice includes P and R := r*G, known as the transaction key, so that Bob can recover the private key to the P via checking whether P - H(a*R)*G == 
B and, if that is the case, subsequently setting p := H(a*R) + b.

Now, naively, Bob could give out entirely different pairs (A,B) for each new payment requested, but this incurs rescanning the blockchain for each one of them, which is very costly.

Subaddresses solves this problem; i.e. with one main viewkey, Bob can give out a virtually unlimited number of unlinkable addresses (or subaddresses), and check whether any of them has receiver a payment with a single scan of the 
blockchain. Here is how it works:

As before, Bob picks two private keys a and b, but now instead of giving out the pair (A, B) := (a*G, b*G), Bob gives out the subaddress (a*B, B) = (a*b*G, b*G). The key here is that Bob will fix a and let b vary to produce 
different subaddresses, so that we may denote the b's by b_i := h(b || i), for i in some big range [1 .. N].

The payment works this way: Alice has the subaddress (A_i, B_i) := (a*B_i, B_i) and wants to produce a one-time address from it. So she picks r at random, and computes R := r*B_i, and sets P := H(r*A_i)*G + B_i. P and R go along 
in the transaction as before so that Bob can identify that P belongs to him and derive its private key. (Notice that the definitions of viewkey and transaction keys changed.)

The one-time address identification is fast because the set of B_i's is loaded in a hash table before the scanning so that when Bob's wallet sees a one-time address P, all it has to do is look up whether P - H(a*R)*G == B_i, for 
some i; i.e., whether P - H(a*R)*G belongs to the hash table. Since a is fixed, that is a single lookup per one-time address, as opposed to many in the naive approach with traditional addresses.

My notation is a bit different and a bit simplified from the given references, but I hope it makes it easier to understand.

    permalinkembedsavereportgive awardreply

