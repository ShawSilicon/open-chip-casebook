# Proof 001: clock domain crossing, async FIFO, proven by k-induction

**This is a proof artifact, not a case.** Nothing failed here. It is published because it is
reproducible on free tools with no hardware, and because its assumption boundary is the part
most people get wrong.

| | |
|---|---|
| **Type** | Proof artifact |
| **Reproduction cost** | Free tools, no hardware. SymbiYosys, Yosys, Yices. |
| **Source** | [cxl-type3-admission-control-plane](https://github.com/taitashaw/cxl-type3-admission-control-plane) |
| **Licence** | MIT |

## What is proven

`async_fifo`, `sync_bits` and `reset_sync`, bridging a link clock domain and a memory clock
domain. Safety only. From `evidence/raw/formal_cdc.log`:

    summary: engine_0 (smtbmc yices) returned pass for induction
    summary: successful proof by k-induction.
    DONE (PASS, rc=0)

| Property | Class |
|---|---|
| No FIFO overflow or underflow | `FORMAL_PROVEN`, unbounded k-induction |
| Gray-code invariant, single-bit change | `FORMAL_PROVEN`, induction |
| Synchronised pointer never leads the true pointer | `FORMAL_PROVEN`, induction |
| End to end data integrity, dequeue[k] equals enqueue[k] | `FORMAL_PROVEN`, induction |
| Synchroniser depth, STAGES at least 2 | `FORMAL_PROVEN`, induction |

No bounded model checking is used as a fallback for any safety property above. Non-vacuity
is shown by covers and by simulation and formal mutation.

## What is NOT proven, and this is the point

| Property | Class |
|---|---|
| Metastable-bit resolution within one destination cycle | **ASSUMPTION.** Modelled by the free-running two-clock harness. Not proven. |
| Liveness, eventual drain, eventual flag update | **NOT CLAIMED.** A stalled domain clock blocks progress. |

A passing CDC formal run does not prove your crossing is safe against metastability. The
MTBF behaviour of each synchroniser is an assumption the harness encodes, not a property the
solver establishes. Treating a green formal result as coverage of metastability is a common
and expensive misreading, and it is why this page states the boundary before the results.

Liveness is not claimed anywhere in this platform. Safety only.

## How to reproduce

Clone the source repository, run `scripts/bootstrap_formal.sh` to fetch the toolchain, then
run the CDC sweep. Compare against `evidence/raw/formal_cdc.log`. No board, no licence, no
vendor tools.
