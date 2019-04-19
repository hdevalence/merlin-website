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

Merlin transcripts are initialized with a single parameter, `app_label`, a
byte string which represents an application-specific domain separator.
See the [Passing Transcripts](../use/passing.md) section for more
details on usage.

To construct a Merlin transcript, first, construct a STROBE-128 object
with the 11-byte label `b"Merlin v1.0"`.  Next, append the message
body `app_label` with label `b"dom-sep"`, as described in the
following section.  Finally, return the transcript.

## Appending Messages

Merlin messages are byte strings up to 4GB (`u32::max_value()` bytes)
long.  Very long messages are allowed but discouraged; consider
prehashing long messages and appending the hash as a message.
Messages are labeled by a byte string. See [Transcript
Protocols](../use/protocol.md) for more information about message
labels.

To append a message `message` with label `label`, perform the STROBE
operation
```
AD[label || LE32(message.len())](message);
```
where `LE32(x)` is the 4-byte, little-endian encoding of the 32-bit
number `x`.

The notation `OP[meta](data)` is defined on page 9 of the [STROBE
paper][strobe_paper]. Because Merlin does not have a transport
concept, the metadata is encoded using `meta-AD`.

## Extracting Challenges

To extract a sequence of challenge bytes labeled by `label` into the
buffer `dest`, perform the STROBE operation
```
dest <- PRF[label || LE32(dest.len())]();
```
where `LE32` is defined as before.

[strobe]: https://strobe.sourceforge.io/
[strobe_paper]: https://strobe.sourceforge.io/papers/strobe-latest.pdf
