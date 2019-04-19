# Transcript Operations

Merlin provides a specified way to use [STROBE][strobe].  STROBE is a
protocol framework intended for constructing online transport
protocols.  Merlin is aimed at a different use-case, zero-knowledge
proofs, so it uses only a subset of STROBE.  This means that a Merlin
implementation can either use a STROBE implementation as a library, or
implement the required subset of STROBE operations directly.  See the
STROBE specification for more details on STROBE operations.

## Transcript Objects

A Merlin transcript is a typed wrapper around a STROBE object,
instantiated using Keccak-f/1600 at the 128-bit security level.

## Initialization

A Merlin transcript is initialized as follows.

## Appending a message

## Extracting a challenge

[strobe]: https://strobe.sourceforge.io/
