# Merlin Transcripts

Merlin provides two objects: a transcript, which models the public
interactions between a prover and a verifier, and a transcript RNG,
which uses the transcript to provide defense-in-depth against bad
entropy attacks.

This chapter describes how these objects are constructed and the
operations they can perform:

* [Transcript Operations](./ops.md) describes the
  transcript object.

* [Generating Randomness](./rng.md) describes the transcript-based
  RNG.

The next chapter, [Using Merlin](../use/index.md), describes how to
use Merlin to implement a proof protocol.
