<img style="margin: 1em; float:right; width: 33%;" src="/merlin.png">

Merlin is a [STROBE][strobe]-based construction of a proof transcript which
applies the Fiat-Shamir transform to an interactive public-coin
argument of knowledge.  This allows implementing protocols as if they
were interactive, committing messages to the proof transcript and
obtaining challenges bound to all previous messages.

In comparison to using a hash function directly, this design provides
support for:

* multi-round protocols with alternating commit and
challenge phases;

* natural domain separation, ensuring challenges are
bound to the statements to be proved;

* automatic message framing, preventing ambiguous encoding of commitment data;

* and protocol composition, by using a common transcript for multiple protocols.

In addition, Merlin provides a transcript-based random number generator.
This provides synthetic randomness derived from
the entire public transcript, as well as the prover's witness data,
and an auxiliary input from an external RNG.

## About

Merlin is authored by Henry de Valence, with design input from Isis
Lovecruft and Oleg Andreev.  The construction grew out of work with Oleg
Andreev and Cathie Yun on a [Bulletproofs implementation][bp].
Thanks also to Trevor Perrin and Mike
Hamburg for helpful discussions.  Merlin is named in reference to
[Arthur-Merlin protocols][am_wiki] which introduced the notion of
public coin arguments.

This project is licensed under the MIT license.

[bp]: https://doc.dalek.rs/bulletproofs/
[strobe]: https://strobe.sourceforge.io/
[am_wiki]: https://en.wikipedia.org/wiki/Arthur%E2%80%93Merlin_protocol


