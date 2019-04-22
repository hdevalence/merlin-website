# Passing Transcripts

Merlin models a proof proof protocol as operating on a transcript
context.  This means that implementations of proof protocols should
*not* create a transcript internally, but accept an existing transcript
as a parameter.

## Domain Separation

Because Merlin transcripts are passed as parameters to the proving and
verification functions provided by a proof library, applications which
use those library functions must create transcript objects to use
those functions.  But because the transcript creation function takes a
domain separation label (see [Transcript
Operations](../transcript/ops.md)), this means that every application
using a Merlin-based proof system performs domain separation by default,
as the application must supply a domain separation label.

Moreover, this also allows an application to bind a Merlin-based proof
not just to a single label, but to arbitrary structured application
data, by appending structured messages to a transcript before passing
it to a proof function.  For instance, an application can commit a
message to a transcript to turn a Schnorr proof into a signature
scheme.

## Sequential Composition

Passing a transcript as a parameter also allows automatic sequential
composition without requiring any changes to the implementations of
the composed proofs.  To compose two proof statements, a library or
application can append a domain separator identifying the composition,
pass the transcript to the first proof function, then pass the
transcript to the second proof function.  The second proof's messages
and challenges are then bound to the first proof's, while the first
proof's messages are bound to the composition declaration.

This can be used both at the application level, combining proofs from
different libraries (for instance, combining a Bulletproof with a
Schnorr proof), or at the library level, allowing a proof to be
implemented internally as a composition of two proofs (for instance,
implementing the inner-product proof in a Bulletproof independently
from the range-to-inner-product reduction proof).