# Reproducing a case

Reproduction is the whole verification mechanism here. Nothing is marked verified because
the author says so.

**A failed reproduction is as valuable as a successful one.** If you follow the steps and do
not see what the case describes, that is a finding about the case, and it is the finding we
most want. Report it the same way.

## What you are being asked to do

1. Pick a case from the [case index](README.md#cases).
2. Read its **How to reproduce** section and check you have the hardware and tools.
3. Run it.
4. Record what you saw, matching or not.
5. Open a [reproduction report](../../issues/new?template=reproduction-report.yml).

You are not being asked to endorse anything. You are being asked to try to break it.

## What to record

Whatever the outcome, capture these. Missing fields are fine; guessed fields are not.

- Board or device, including revision.
- Tool name and exact version.
- Operating system.
- The case commit hash you followed.
- The commands you ran, verbatim.
- The output you got, verbatim.
- Anything you had to do that the case did not tell you to do.

That last one matters most. Undocumented setup steps are the usual reason a case works for
its author and nobody else, and they are invisible to the person who wrote it.

## Outcomes

| You saw | Report it as | What happens |
|---|---|---|
| What the case describes | `reproduced` | Case moves to `REPRODUCED`, you are credited unless you decline. |
| Something different | `diverged` | Case stays where it is until the difference is understood. |
| Could not get far enough to tell | `blocked` | Usually a documentation gap. Tell us where you stopped. |

A case that diverges is not deleted. The divergence gets written into it, because "this does
not reproduce on revision B" is exactly the kind of thing this commons exists to record.

## Attribution

Your result and your name stay yours. Say in the report whether you want to be credited,
credited under a handle, or not credited. Silence is treated as no credit.

## Hardware barriers are real

Some cases need a specific board. Case 001 needs a ZCU104 and a Vivado installation, which
is a genuine barrier and the reason it may sit at `AWAITING INDEPENDENT REPRODUCTION` for a
while. That is not hidden. Cases reproducible on free tools with no hardware are marked as
such in the index and are the better entry point if you are new here.

If a case cannot be reproduced by anyone without buying hardware, that is a limitation of
the case, not a fault of the reader.
