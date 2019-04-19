# Generating Randomness

Merlin provides a STROBE-based RNG for use in proof implementations,
which aims to provide a general defense-in-depth against bad-entropy
attacks.  It's intended to generalize from

1.  the deterministic nonce generation in Ed25519 & RFC 6979;
2.  Trevor Perrin's ["synthetic" nonce generation for Generalised
EdDSA][trevp_synth];
3.  and Mike Hamburg's nonce generation mechanism sketched in the
[STROBE paper][strobe_paper];

towards a design that's flexible enough for arbitrarily complex
public-coin arguments.

## Deterministic and synthetic nonce generation

In Schnorr signatures (the context for the above designs), the
"nonce" is really a blinding factor used for a single
sigma-protocol (a proof of knowledge of the secret key, with the
message in the context); in a more complex protocol like
Bulletproofs, the prover runs a bunch of sigma protocols in
sequence and needs a bunch of blinding factors for each of them.

As noted in Trevor's mail, bad randomness in the blinding factor
can screw up Schnorr signatures in lots of ways:

* guessing the blinding reveals the secret;
* using the same blinding for two proofs reveals the secret;
* leaking a few bits of each blinding factor over many signatures
can allow recovery of the secret.

For more complex ZK arguments there's probably lots of even more
horrible ways that everything can go wrong.

In (1), the blinding factor is generated as the hash of both the
message data and a secret key unique to each signer, so that the
blinding factors are generated in a deterministic but secret way,
avoiding problems with bad randomness.  However, the choice to
make the blinding factors fully deterministic makes fault
injection attacks much easier, which has been used with some
success on Ed25519.

In (2), the blinding factor is generated as the hash of all of the
message data, some secret key, and some randomness from an
external RNG. This retains the benefits of (1), but without the
disadvantages of being fully deterministic.  Trevor terms this
"*synthetic nonce generation*".

The STROBE paper (3) describes a variant of (1) for performing
STROBE-based Schnorr signatures, where the blinding factor is
generated in the following way: first, the STROBE context is
copied; then, the signer uses a private key `k` to perform the
STROBE operations
```text,no_run
KEY[sym-key](k);
r <- PRF[sig-determ]()
```

The STROBE design is nice because forking the transcript exactly
when randomness is required ensures that, if the transcripts are
different, the blinding factor will be different -- no matter how
much extra data was fed into the transcript.  This means that even
though it's deterministic, it's automatically protected against an
issue Trevor mentioned:

> Without randomization, the algorithm is fragile to
> modifications and misuse.  In particular, modifying it to add an
> extra input to h=... without also adding the input to r=... would
> leak the private scalar if the same message is signed with a
> different extra input.  So would signing a message twice, once
> passing in an incorrect public key K (though the synthetic-nonce
> algorithm fixes this separately by hashing K into r).

## Transcript-based synthetic randomness

Merlin provides a transcript-based RNG that combines the ideas from
(2) and (3) above.  To generate randomness, a prover:

1. creates a secret clone of the public transcript state up to that
   point, so that the RNG output is bound to the entire public
   transcript;

2. rekeys their clone with their secret witness data, so that the RNG
   output is bound to their secrets;

3. rekeys their clone with 32 bytes of entropy from an external RNG,
   avoiding fully deterministic proofs.

Binding the output to the transcript state ensures that two
different proof contexts always generate different outputs.  This
prevents repeating blinding factors between proofs.  Binding the
output to the prover's witness data ensures that the PRF output
has at least as much entropy as the witness does.  Finally,
binding the output to the output of an external RNG provides a
backstop and avoids the downsides of fully deterministic generation.

In Merlin's setting, the only secrets available to the prover are the
witness variables for the proof statement, so in the presence of a
weak or failing RNG, the RNG's entropy is limited to the entropy of
the witness variables.

A verifier can also use the transcript RNG to perform randomized
verification checks, although defense-in-depth is less essential
there.  They can skip step 2, and perform only steps 1 and 3.

## Constructing an RNG

To rekey with secret witness bytes `witness` labeled by `label`, the
prover performs the STROBE operations
```
KEY[label || LE32(witness.len())](witness);
```

To finalize the transcript RNG constructor into an RNG, the prover
obtains `rng`, 32 bytes of output from an external rng, then performs
```
KEY[b"rng"](rng);
```

Ideally, the transcript, transcript RNG constructor, and transcript
RNG should all have different types, so that it is impossible to
accidentally rekey the public transcript, or use an RNG before it has
been finalized.

[trevp_synth]: https://moderncrypto.org/mail-archive/curves/2017/000925.html
[strobe_paper]: https://strobe.sourceforge.io/papers/strobe-20170130.pdf
