# Contributing to the Open Chip Casebook

Two ways to contribute, and the second one is more valuable than most people expect.

## 1. Reproduce an existing case

Pick a case, run it, and open an issue saying what you saw. Report it honestly whether it
matched or not. A case that fails to reproduce is a more useful finding than one that
quietly succeeds.

Include your tool versions, your platform, and the output you actually got. If it matched,
the case moves to `REPRODUCED` and your name goes on it. If it did not, we investigate and
the case stays where it is until the discrepancy is understood.

A case is never marked verified by its own author.

## 2. Submit a new case

Open an issue before writing it up, so we can check the boundaries below before you spend
the effort.

Your case must carry all ten items listed in the README. In particular it must include the
smallest design that reproduces the failure, exact tool versions, the root cause rather
than the symptom, and what the fix cost.

### The design must be yours to publish

One of the following must be true, and you must say which in the case:

- You constructed the design deliberately for this case.
- The design is already openly licensed.
- The owner gave written permission, and you can produce it if asked.

Designs reconstructed from memory of employer work do not qualify, even if you rewrite
them. If you cannot separate what you know from what your employer owns, do not submit it.

## Developer Certificate of Origin

All contributions require a DCO sign-off. Add a `Signed-off-by` line to each commit:

    git commit -s -m "your message"

This certifies you wrote the contribution or have the right to submit it under the
repository licence. See https://developercertificate.org for the full text. We use DCO
rather than a CLA because it asks for a statement of fact rather than a transfer of rights.

## Security findings

Do not open a public issue for an unpatched vulnerability. See [SECURITY.md](SECURITY.md).

## Tone

Criticise the design, the tool, or the documentation. Never the engineer. Most cases here
exist because something was genuinely misleading, and the person who hit it was paying
attention, not being careless.
