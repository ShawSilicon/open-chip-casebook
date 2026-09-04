# Case 003: the test could not see it

| | |
|---|---|
| **State** | `AWAITING INDEPENDENT REPRODUCTION` |
| **Fix status** | **Diagnosed, fix specified, NOT APPLIED.** See the evidence boundary. |
| **Domain** | HLS, AXI-Stream integration, reset-to-running boundary |
| **Reproduction cost** | Vitis and Vivado 2025.2. Not free tools, no board required. |
| **Source** | [rope-hls-vs-rtl](https://github.com/taitashaw/rope-hls-vs-rtl), finding F4 |
| **Licence** | Apache 2.0 |
| **Verified by author** | Phase 2, logs committed |

## Why this case exists

Verification takes 60 to 70% of engineering effort on a chip project, and only 14% of
ASIC and SoC projects still reach first-silicon success, the lowest figure in more than
twenty years of tracking (2024 Siemens EDA / Wilson Research Group Functional Verification
Study). Teams are not failing because they skip verification. They are failing while doing
more of it than any other activity.

This case is one concrete reason. The test here did not fail to catch the bug because it was
weak or incomplete. It could not catch the bug, because the level it ran at has no concept
of the thing that broke. Effort was never the problem. Blindness was.

## What failed, in plain language

A kernel written in C++ and compiled to hardware by a high-level synthesis tool. In
software simulation it produces exactly the right numbers. Every check passes.

Run the same kernel as actual hardware and the very first block of output is one chunk
short. Not wrong values. A missing chunk. Everything after it is fine.

The software test could never have caught it, because the thing that went wrong does not
exist in software: the moment the hardware comes out of reset and starts running.

## The wrong hypothesis

That passing C simulation meant the kernel was correct.

C simulation drives the kernel as an ordinary function over pre-filled streams and checks
values. It has no notion of a reset-to-running boundary and no per-beat handshake timing, so
the failure is invisible at that level by construction.

Three further explanations were ruled out by experiment rather than argument, and ruling
them out is most of the work:

- **Not a wrapper port-mapping error.** The hand-written RTL is bit-exact through the
  identical wrapper.
- **Not an X or ROM-initialisation error.** `x_count` is 0.
- **Not a collector misframe.** The collector is TLAST-framed and asserts ready from before
  reset.

## Root cause

The naive streaming kernel is a free-running `II=1` loop, `while (!in.empty())`, emitting
`ap_axiu` beats with TLAST. At the reset-to-running transition the first head loses its
final output beat.

It is not correctable downstream. A beat that was never emitted cannot be synthesised back
by a wrapper.

## Before and after

Both implementations run through the **identical** wrapper, top, harness and collector. Only
the kernel differs. That is what makes this a controlled comparison rather than an
observation.

    RTL   beats_out=256  bit_exact=2048  mism=0   max_lsb=0  x_count=0  frame_fail=0
    HLS   beats_out=255  bit_exact=1947  mism=93  max_lsb=2  x_count=0  frame_fail=1
    HLS   [FAIL] head 0 emitted 15 beats (expected 16)

The arithmetic closes on the loss being exactly one whole beat rather than scattered
corruption. The golden has 2048 values. The HLS run accounts for 1947 exact plus 93
mismatched, which is 2040. The missing 8 are one 8-lane beat.

The 93 mismatches are a **separate** finding, not this one. They are live `cosf`/`sinf`
quantised to Q1.15 differing from a double-precision Q1.15 table by at most 2 LSB. Heads 1
through 15 are complete at 16 beats each.

## The fix, and what it cost

**Not applied.** The specified correction is to frame the loop by the known head length, a
counted beat loop, instead of `while (!in.empty())`, or to add an explicit startup flush.
That is a kernel change, not a wrapper patch.

Cost: `NOT_MEASURED`. No area, timing or power comparison exists between the two loop forms,
because the change was never made.

## The attempted correction, and what it did

**Attempted 4 Sep 2026. Reverted the same day.** The specified fix was applied and it made
things worse.

The candidate change reframed the loop by the known block length instead of terminating on
stream emptiness. Measured through the identical wrapper:

| metric | before | after the attempt |
|---|---|---|
| `beats_out` | 255 | 255, unchanged |
| `bit_exact` | 1947 | 343 |
| `mism` | 93 | 1697 |
| `max_lsb` | 2 | 65535, full scale |

The missing beat was not recovered, and the data went from 93 mismatches to 1697.

**C simulation passed the same change**, reporting `[PASS] functionally correct RoPE,
max_abs_err 6.104e-05 < 2.0e-03`.

Why it regressed: the original loop recovered its position from the stream's own
end-of-block marker on every beat. The candidate fix derived position from a loop counter
instead, assuming the stream was block-aligned on entry. With the first block still one beat
short, every later block sat one beat out of phase, so every angle was computed for the
wrong position. The change removed the design's only resynchronisation.

This is the case's own argument arriving a second time and larger. The blind test did not
merely fail to catch the original defect. It approved a regression eighteen times worse.

The kernel has been reverted. The attempt is recorded here rather than discarded, because a
correction that fails is evidence about the test, not just about the fix.

## Evidence boundary

This case is diagnosed but not closed, and that is the honest state of it.

What is `MEASURED`: the defect, its exact magnitude, its scope, and four competing
explanations eliminated.

What is also `MEASURED`: the failed candidate correction above, and the fact that C
simulation approved it.

What is `NOT_MEASURED`: any state in which this defect is fixed. One correction has been
attempted and rejected; no working correction exists yet, so there is no cost figure for one
either. Publishing the diagnosis without a working fix is deliberate. A complete diagnosis
with four competing explanations eliminated is useful to somebody hitting the same symptom
today, and a rejected correction is itself evidence about the test.

Verification required to close it: the first head emits 16 beats with TLAST on beat 15,
`beats_out` 256, and data matching the golden within the separate float-trig tolerance.

## How to reproduce

Requires Vitis and Vivado 2025.2. No board.

    export PATH=/tools/Xilinx/2025.2/Vivado/bin:/tools/Xilinx/2025.2/Vitis/bin:$PATH

Run the HLS flow, then the wrapper smoke test for both implementations, and compare against
`reports/wrapper_smoke_hls.log` and `reports/wrapper_smoke_rtl.log` in the source repository.
Report what you saw at the [reproduction form](../../../../issues/new?template=reproduction-report.yml),
matching or not.

## Provenance

Constructed by the author for an HLS versus hand-RTL bake-off. Apache 2.0, no employer-owned
material.
