<img style="margin: 1em; float:right; width: 33%;" src="/merlin.png">

Merlin is a [STROBE][strobe]-based transcript construction for zero-knowledge
proofs.  It automates the Fiat-Shamir transform, so that by using Merlin,
non-interactive protocols can be implemented as if they were interactive.

This is significantly easier and less error-prone than performing the
transformation by hand, and in addition, it also provides natural support for:

* multi-round protocols with alternating commit and
challenge phases;

* natural domain separation, ensuring challenges are
bound to the statements to be proved;

* automatic message framing, preventing ambiguous encoding of commitment data;

* and protocol composition, by using a common transcript for multiple protocols.

Finally, Merlin also provides a transcript-based random number generator as
defense-in-depth against bad-entropy attacks (such as nonce reuse, or bias over
many proofs).  This RNG provides synthetic randomness derived from the entire
public transcript, as well as the prover's witness data, and an auxiliary input
from an external RNG.

## About

Merlin is authored by Henry de Valence, with design input from Isis
Lovecruft and Oleg Andreev.  The construction grew out of work with Oleg
Andreev and Cathie Yun on a [Bulletproofs implementation][bp].
Thanks also to Trevor Perrin and Mike
Hamburg for helpful discussions.  Merlin is named in reference to
[Arthur-Merlin protocols][am_wiki] which introduced the notion of
public coin arguments.

The header image was created by Oleg Andreev as a composite of Arthur Pyle's
[The Enchanter Merlin][merlin_pyle] and the Keccak Team's [θ-step
diagram][keccak_theta].

This project is licensed under the MIT license.

[bp]: https://doc.dalek.rs/bulletproofs/
[strobe]: https://strobe.sourceforge.io/
[am_wiki]: https://en.wikipedia.org/wiki/Arthur%E2%80%93Merlin_protocol
[merlin_pyle]: https://commons.wikimedia.org/wiki/File:Arthur-Pyle_The_Enchanter_Merlin.JPG
[keccak_theta]: https://keccak.team/figures.html


