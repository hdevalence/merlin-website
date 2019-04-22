# Transcript Protocols

In general, a proof system operates on mathematical objects: finite
field elements, elliptic curve points, etc., while Merlin provides a
byte-oriented message API.  This means that while Merlin handles the
details of transcript hashing, message framing, and so on, it is the
proof system implementation's responsibility to define its *transcript
protocol*: how its mathematical objects (at the proof system layer)
correspond to byte messages (at the Merlin layer).

## Defining a Transcript Protocol

This consists of two parts:

1.  Defining a proof-specific domain separator and a set of labels for
the messages specific to the protocol.  The labels should be fixed,
not defined at runtime, as runtime data should be in the message body,
while the labels are part of the protocol definition.  A sufficient
condition for the transcript to be parseable is that the labels should
be distinct and none should be a prefix of any other.

2.  Defining how mathematical objects are encoded as bytes: for
instance, how to encode a curve point as a byte string, or how to
convert uniformly distributed challenge bytes to a
uniformly-distributed scalar.

## Example

In Rust, a transcript protocol can be defined using an extension
trait, to add the proof-specific behaviour to the library-provided
`Transcript` type, as follows.  Other languagues can accomplish the
same task by other means:

```rust
trait TranscriptProtocol {
    fn domain_sep(&mut self);
    fn append_point(&mut self, label: &'static [u8], point: &CompressedRistretto);
    fn challenge_scalar(&mut self, label: &'static [u8]) -> Scalar;
}

impl TranscriptProtocol for Transcript {
    fn domain_sep(&mut self) {
        // A proof-specific domain separation label that should
	// uniquely identify the proof statement.
        self.append_bytes(b"dom-sep", b"TranscriptProtocol Example");
    }

    fn append_point(&mut self, label: &'static [u8], point: &CompressedRistretto) {
        self.append_bytes(label, point.as_bytes());
    }

    fn challenge_scalar(&mut self, label: &'static [u8]) -> Scalar {
        // Reduce a double-width scalar to ensure a uniform distribution
        let mut buf = [0; 64];
        self.challenge_bytes(label, &mut buf);
        Scalar::from_bytes_mod_order_wide(&buf)
    }
}

```

This provides a proof-specific domain separator function to mark the
beginning of a proof statement, which should uniquely identify the
proof statement, including any parameters. The example's functions
leave the labels to the call sites in the rest of the implementation,
allowing code like

```rust
transcript.commit_point(b"A", A.compress());

```

but it may be better to maintain a fixed set of labels internally to
the definition of the transcript protocol.

## Validation Logic

The transcript protocol is also a convenient location to place
validation logic for prover-supplied parameters, because if this
validation logic is baked into the transcript protocol, it can't be
accidentally omitted.  For instance, a Schnorr proof may require that
the prover-supplied points are not the identity element, and this
logic can be included into a validate-and-append function.
