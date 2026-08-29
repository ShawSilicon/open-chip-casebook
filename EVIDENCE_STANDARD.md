# Evidence standard

Every claim in a case carries a result class. A claim without one is not evidence, it is an
opinion with a register address in it.

This taxonomy is the one used in the source projects, so a case and the repository it came
from speak the same language.

## Result classes

| Class | Means | Typical proof |
|---|---|---|
| `MEASURED` | Observed on real hardware or a real tool run, with the raw output kept. | A log, a register read, a waveform, a tool transcript. |
| `SIMULATED` | Reproduced in simulation only. | Icarus, Verilator, XSim run with a seed recorded. |
| `FORMAL_PROVEN` | Proven for all reachable states within stated assumptions. | SymbiYosys induction or BMC log, with the assumptions written down. |
| `EMULATED` | Reproduced under emulation rather than the real device. | QEMU or equivalent, version pinned. |
| `INFERRED` | Follows from the above by reasoning, not by running anything. | Say so explicitly and show the reasoning. |
| `NOT_MEASURED` | Nobody ran it. | State it. Do not fill the gap with a plausible number. |
| `BLOCKED` | Could not be run, with the reason. | Missing tool, missing device, missing licence. |

`INFERRED` and `NOT_MEASURED` are not failures. Publishing them is the point. A case that
says "we never measured the area cost" is worth more than one that estimates it and hopes
nobody checks.

## The evidence boundary

Every case states where its evidence stops. That is the evidence boundary, and it is a
required section, not an apology.

Case 001 is the working example: the fix is two register writes and a read-back, the state
change is `MEASURED`, and the implementation cost is `NOT_MEASURED` because the fix does not
touch the design. The case says so in its own text.

## What does not count as evidence

- A screenshot with no command, version or timestamp.
- A claim about "every value" of something when a handful were tried. Write what was tried.
- A vendor statement about what a tool does. Run the tool.
- A result that only exists on the author's machine with undocumented setup.
- Anything reconstructed from memory after the fact.

## Precision rules

1. **Scope every "never" and "always."** `psu_init()` never calling a routine is a fact about
   one generated file in one build, not about every ZynqMP boot path. Write "in this build."
2. **Separate configuration from behaviour.** A clock enabled in a register is not a clock
   arriving at a load. Case 001 exists because those two were conflated.
3. **Cite to the page.** `UG1085 p.34` is checkable in a minute. `UG1085` is not.
4. **Pin versions.** Tool, OS, board revision, commit hash.
5. **Name the wrong hypothesis.** What you ruled out and how is often the most useful part.

## Reproduction and verification

A case is `REPRODUCED` only when someone other than the author runs it and reports what they
saw. The author cannot verify their own case. See [REPRODUCE.md](REPRODUCE.md).
