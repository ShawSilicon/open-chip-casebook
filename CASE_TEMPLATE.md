# Case NNN: <short title stating the finding, not the symptom>

| | |
|---|---|
| **State** | `PUBLISHED` / `AWAITING INDEPENDENT REPRODUCTION` / `REPRODUCED` |
| **Domain** | <e.g. FPGA bring-up, CDC, timing closure, protocol interop> |
| **Reproduction cost** | <free tools and no hardware, or name the board and licensed tools> |
| **Source** | <link to the repository holding the design, logs and scripts> |
| **Licence** | <licence of the source repository> |
| **Verified by author** | <date> |

## What failed, in plain language

Explain it so somebody outside the specialty follows it. No jargon that is not immediately
unpacked. If a ten year old could not follow the shape of the story, rewrite it.

## The wrong hypothesis

What the evidence pointed at first, and why that was reasonable. If the tool's own error text
or documentation led you there, say so, because the next person will be led there too.

This section is usually the most valuable part of the case. Do not skip it because it is
unflattering.

## Root cause

The actual mechanism. Cite documentation to the page. Scope any "never" or "always" to the
build, revision or configuration it was observed in.

## Before and after

The measured evidence, with raw values.

    before  <value>    <decoded fields>
    after   <value>    <decoded fields>

State the result class of each claim. See [EVIDENCE_STANDARD.md](EVIDENCE_STANDARD.md).

## The fix and what it cost

The change itself, then its cost in latency, area, power or complexity.

If a cost was not measured, write that it was not measured and why. Do not estimate. A
stated absence is a valid entry here and is preferred over a plausible number.

## Evidence boundary

Where this case stops. What it does not claim. What would falsify it.

## How to reproduce

Exact hardware, exact tool versions, exact commands. Assume no shared context with you.
Then say what a successful run looks like and what a failed one looks like.

See [REPRODUCE.md](REPRODUCE.md) for how to report the result.

## Provenance

State which of these applies, and be specific:

- Deliberately constructed for this case.
- Already openly licensed, with a link.
- Published with the owner's written permission, which you can produce if asked.

Designs reconstructed from memory of employer work do not qualify.
