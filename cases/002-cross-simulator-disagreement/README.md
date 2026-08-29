# Case 002: the same RTL, three simulators, no agreement

| | |
|---|---|
| **State** | `AWAITING INDEPENDENT REPRODUCTION` |
| **Domain** | RTL portability, cross-simulator verification |
| **Reproduction cost** | Free tools, no hardware. Icarus and Verilator, both open source. |
| **Source** | [cxl-type3-admission-control-plane](https://github.com/taitashaw/cxl-type3-admission-control-plane) |
| **Licence** | MIT |
| **Verified by author** | 16 Jul 2026 |

## What failed, in plain language

You write one piece of logic and run it through two different simulators. They disagree
about what it does.

Only one of them can be right, and there is no rule that says the popular one wins. The
instinct at this point is that the simulator is broken. That instinct is usually wrong, and
acting on it means shipping RTL whose behaviour depends on which tool you happened to use.

## The wrong hypothesis

The simulator has a bug.

It is a reasonable first thought, because the design looks correct and the tools are the
only thing that changed. It is also the most expensive place to start looking, because it
sends you to someone else's issue tracker instead of your own source.

Two of the three issues in this bring-up turned out to be in the design and the testbench.
The third genuinely is a tool behaviour difference, and it is still not claimed as a tool
defect. See the evidence boundary below.

## Root cause

The bring-up surfaced three distinct issues, and separating them was most of the work.

**1. A design bug.** The translator emitted stale error flags on a decoder miss. Fixed by
gating every translation output on a match. `MEASURED` by differential simulation.

**2. An RTL portability hazard, and the cause of the cross-simulator disagreement.** The
original overlap logic used a runtime-indexed `always_comb` over unpacked arrays. That
construct evaluated inconsistently across simulators. It is not a wrong algorithm and it is
not a tool defect. It is a coding style whose scheduling semantics different simulators
resolve differently. Reworked into constant-index generate logic, after which Icarus 12.0,
Verilator 5.020 and AMD XSim 2025.2 all agree.

**3. A testbench stimulus incompatibility, observed on Verilator 5.020 only.** A procedural
blocking write to an individual element of a packed multidimensional array, read through a
module port by combinational logic, was not re-propagated on subsequent `#delay` steps.
Icarus propagated it. Replacing asynchronous combinational stimulus with synchronous
registered stimulus eliminated the discrepancy across all three simulators.

## Before and after

Before: the same overlap logic produced different results depending on the simulator, so no
result could be trusted without naming which tool produced it.

After: three independent simulators agree, and the decode behaviour is proven rather than
sampled. From `formal/decode.sby`, run under SymbiYosys with bounded model checking and
unbounded induction:

    DONE (PASS, rc=0)      decode_prove
    DONE (PASS, rc=0)      decode_cover

The proved properties include: accept implies exactly one match; overlap implies not accept;
accept implies aligned; accept implies the whole 64 byte line lies inside the window; and
`dpa = dpa_base + (hpa - base)`.

That last set matters because the fail-closed rule is the one a priority encoder would
quietly break. Two enabled windows overlapping in HPA must reject, never silently
priority-select. `win_id` is diagnostic only and never authorises a transaction.

## The fix and what it cost

Constant-index generate logic in place of runtime-indexed `always_comb`, and synchronous
registered testbench stimulus in place of asynchronous combinational stimulus.

Cost: `NOT_MEASURED`. No area, timing or power comparison was run between the two coding
styles. The change was made for determinism across simulators, not for area or speed, and
no number is claimed in either direction.

## Evidence boundary

This is the part worth reading carefully, because the honest answer here is smaller than
the story invites.

**No simulator defect is claimed.** Synchronous driving demonstrated a portable workaround.
It did not prove Verilator 5.020 is defect-free, and it did not establish that the observed
behaviour is incorrect rather than merely different. Confirming or excluding an upstream
defect would require a current Verilator, 5.036 or newer, and if the behaviour persisted, an
upstream issue filing. Neither has been done.

So issue 3 is characterised, not diagnosed. It is recorded here because "we saw a difference
and did not chase it to the bottom" is a true statement about the state of the evidence, and
suppressing it would make the case look tidier than the work was.

Issues 1 and 2 are diagnosed and fixed.

## How to reproduce

Free tools, no hardware, no licence.

1. Clone [the source repository](https://github.com/taitashaw/cxl-type3-admission-control-plane).
2. Run `scripts/bootstrap_formal.sh` to fetch the OSS CAD Suite. The toolchain is not
   vendored into the repository, so this step downloads it.
3. Run the differential simulation lane across Icarus and Verilator, and `make formal` for
   the SymbiYosys proofs.
4. Compare against `evidence/raw/formal_decode.log`.

To reproduce the original disagreement rather than the fix, revert the overlap logic to a
runtime-indexed `always_comb` over unpacked arrays and run the same lane across both
simulators.

A successful reproduction shows both engines agreeing after the fix. A divergence, on any
simulator version, is a more interesting result than a match. Report either at the
[reproduction form](../../../../issues/new?template=reproduction-report.yml).

## Provenance

The design was constructed by the author for this validation platform. It is MIT licensed
and contains no employer-owned material. It is a vendor-neutral CXL Type-3 validation
model, not a silicon, PHY or protocol-compliance claim.
