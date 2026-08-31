# Case 003: it passed C simulation and lost a beat in hardware

| | |
|---|---|
| **State** | `AWAITING INDEPENDENT REPRODUCTION` |
| **Fix status** | **Diagnosed, fix specified, NOT APPLIED.** See the evidence boundary. |
| **Domain** | HLS, AXI-Stream integration, reset-to-running boundary |
| **Reproduction cost** | Vitis and Vivado 2025.2. Not free tools, no board required. |
| **Source** | [rope-hls-vs-rtl](https://github.com/taitashaw/rope-hls-vs-rtl), finding F4 |
| **Licence** | Apache 2.0 |
| **Verified by author** | Phase 2, logs committed |

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

## Evidence boundary

This case is diagnosed but not closed, and that is the honest state of it.

What is `MEASURED`: the defect, its exact magnitude, its scope, and four competing
explanations eliminated.

What is `NOT_MEASURED`: everything after the fix. There is no after. Publishing the
diagnosis without the correction is deliberate, because a complete diagnosis with three
alternatives eliminated is useful to somebody hitting the same symptom today, and waiting
for a v1.1 that has not been scheduled would mean publishing nothing.

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
