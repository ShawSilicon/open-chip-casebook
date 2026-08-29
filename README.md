# Open Chip Casebook

The published record of the **Open Chip Clinic**.

One broken chip block. One reproducible test. One proven fix. Free for the world.

The Clinic is the programme and the front door: it looks at designs. The Casebook is what
gets written down afterwards and published for anyone to challenge. The two carry different
standards of proof on purpose. A scoped audit is clinical judgement delivered privately to
the owner. A case here is a public claim, and no public claim is marked verified until
someone who did not write it has rerun it.

Chip-design knowledge is expensive to create, hard to verify from outside, and usually
disappears behind company walls. So the next engineer pays to discover the same crack
again. This is a public record of specific failures, what actually caused them, and what
the fix cost.

Prove it once, and everyone keeps the result.

## Three ways in

- **Read Case 001.** [The clock was right and the hub was still dead](cases/001-zcu104-ps-pl-isolation/).
- **Reproduce it.** [How reproduction works](REPRODUCE.md), then
  [file a reproduction report](../../issues/new?template=reproduction-report.yml). A failed
  reproduction is as valuable as a successful one.
- **Submit a failure.** [Propose a case](../../issues/new?template=case-submission.yml), and
  see [CASE_TEMPLATE.md](CASE_TEMPLATE.md) for the shape it needs to take.

Claims here carry a result class. See [EVIDENCE_STANDARD.md](EVIDENCE_STANDARD.md).

## What a case must contain

Every case in this commons carries all ten of the following. A write-up missing any of
them is a draft, not a case.

1. A plain-language explanation of what failed, readable by someone outside the specialty.
2. A design that is either deliberately constructed, openly licensed, or published with
   the owner's written permission.
3. The smallest test that reproduces the failure.
4. Tool names, versions, seeds and commit hashes.
5. The engineering root cause, not just the symptom.
6. The fix.
7. What the fix cost, in latency, area, power or complexity.
8. Before-and-after results, with the measured values shown.
9. Independent reproduction by a second contributor.
10. A clear open-source licence and provenance record.

## Verification states

Nothing here is verified because we say so. Every case carries one of three states, and
the state is visible on the case itself.

| State | Meaning |
|---|---|
| `PUBLISHED` | The write-up is complete and meets all ten requirements except item 9. |
| `AWAITING INDEPENDENT REPRODUCTION` | Published, and nobody outside the original author has rerun it yet. |
| `REPRODUCED` | A second contributor has rerun it and reported what they saw. |

A case only becomes `REPRODUCED` when someone who did not write it runs it and says so in
an issue. That is the whole mechanism. Reproducing a case is the most valuable thing
anyone can do here.

## Cases

| No. | Case | Domain | State | Source |
|---|---|---|---|---|
| 001 | [The clock was right and the hub was still dead](cases/001-zcu104-ps-pl-isolation/) | FPGA bring-up, ZynqMP PS-PL isolation | `AWAITING INDEPENDENT REPRODUCTION` | [kv-page-lifecycle-guard](https://github.com/taitashaw/kv-page-lifecycle-guard/blob/master/docs/hil/ps_pl_isolation.md) |
| 002 | [The same RTL, three simulators, three answers](cases/002-cross-simulator-disagreement/) | RTL portability, cross-simulator verification | `AWAITING INDEPENDENT REPRODUCTION` | [cxl-type3-admission-control-plane](https://github.com/taitashaw/cxl-type3-admission-control-plane) |

### Proof artifacts

Published work that is evidence without being a case. A case requires a failure: a symptom,
a hypothesis that was wrong, a root cause and a fix. Passing proofs have none of those, so
they live here instead of in the table above.

| No. | Artifact | Reproduction cost |
|---|---|---|
| 001 | [Clock domain crossing, async FIFO, proven by k-induction](proofs/001-async-fifo-cdc/) | Free tools, no hardware |

## Where to start

Cases that need no hardware and no paid tools are the best entry point, because anyone can
rerun them. Cases needing a specific board are marked as such, and their reproduction
requests are aimed at people who already own one.

## What this commons will not publish

- Proprietary or employer-owned RTL without written permission.
- Designs reconstructed from memory of employer work. Cases must be clean-room or owned.
- Foundry-restricted PDK material.
- Export-controlled designs.
- Third-party IP outside its licence.
- Candidate identities, screening artifacts or assessment recordings.
- Security vulnerabilities that have not been through coordinated disclosure. See
  [SECURITY.md](SECURITY.md).
- Performance claims without reproducible evidence.

Open source is not automatically correct, secure or fabrication-ready. Public scrutiny
only builds trust when someone can rerun the evidence.

## Bringing us a design

The Clinic takes intake. If you have a design misbehaving and you want someone to look at
it, that is a scoped engagement, not a free case: it runs privately, the findings are
yours, and nothing about it is published without your written permission.

If a finding turns out to be safe to share and you agree to release it, it can become a
public case here, credited or anonymous as you prefer. That is how this commons is meant to
grow past the cases one person can write alone.

Scope and terms are at [shawsilicon.ai](https://shawsilicon.ai). Do not send design files
until scope is agreed in writing.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Reproducing an existing case is as valuable as
submitting a new one, and it is a much shorter path to being useful here.

## Licence

The write-ups in this repository are MIT licensed. Each linked source repository carries
its own licence, stated on the case page.

The Open Chip Clinic is run by [ShawSilicon](https://shawsilicon.ai).
