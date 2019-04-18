# Fiat-Shamir Problems

The [Fiat-Shamir heuristic][fs_wiki] provides a way to transform a
(public-coin) interactive argument into a non-interactive argument.
Intuitively, the idea is to replace a verifier's random challenges
with a hash of the prover's prior messages, but the exact details
are usually unspecified.

Specifying these details requires deciding, among other things, what
exactly the prover's messages are, how they are formatted, how to
ensure the encoding is unambiguous, how to handle domain separators,
how to link challenges between rounds (in the case of multi-round
protocols), how to expand challenges (in the case that a protocol
requires more challenge bytes than the digest size), etc.

More importantly, there's a conceptual gap: the protocols that
people *specify and analyze* are interactive, while the protocols
that people *implement and use* are non-interactive, and these
related but distinct concepts are linked by an ad-hoc transformation
left up to each proof system's implementor.  This gap opens space for
security vulnerabilities, such as in the [Helios protocol][helios]
used for IACR elections, and in the [SwissPost/Scytl e-voting
system][scytl].

In contrast, Merlin aims to fill this conceptual gap by providing an
automatic linkage, handling the details of message framing and
encoding, and allowing an implementation to be specified as if it
were interactive, matching the description in a paper.

[fs_wiki]: https://en.wikipedia.org/wiki/Fiat%E2%80%93Shamir_heuristic
[helios]: https://eprint.iacr.org/2016/771
[scytl]: https://people.eng.unimelb.edu.au/vjteague/HowNotToProveElectionOutcome.pdf
