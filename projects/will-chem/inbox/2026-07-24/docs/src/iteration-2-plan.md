# Iteration 2 — plan

Builds on `ITERATION_1_REVIEW.md` (this branch) and the reviewer triage in
`uma-carbocation-study/iteration-1:NEXT_ROUND.md`. Execute on a fresh branch
`uma-carbocation-study/iteration-2` cut from `uma-carbocation-study/iteration-1`
(inherits the iteration-1 codebase, ledgers, and results).

## Context — why iteration 2

Iteration 1 established that UMA is a strong, fast engine for closed-shell
physical-organic chemistry but has real, narrow failures on reactive
intermediates. The domain reviewer's verdict reframed the project: **for a model
sold as general, the flaws are the contribution.** The interesting-marked flaws
cluster into one thesis — *UMA gets carbocation relative energies wrong: it
over-stabilizes small localized-empty-orbital cations (methyl T4, vinyl B5
"chase") and under-penalizes cations that cannot planarize/delocalize
(bridgehead B4/T6).*

But iteration 1's evidence for that thesis is **not yet rigorous enough to
publish**: the 100 hypotheses were UMA-only (no systematic second-method
cross-check), key magnitudes are estimates rather than measurements, and the
experiment code is a monolith that cannot cleanly host a *designed* series.
Iteration 2 turns the anecdote into a quantified, cross-checked, literature-
anchored failure map — and prescribes the data that would fix UMA.

## Goal

> **Build the rigorous, quantified failure taxonomy of UMA on carbocation
> stabilization, and prescribe the training data that would fix it — DFT-free
> (UMA + xTB + literature).**

Every candidate flaw must pass three **rigor gates** before it is reported:

1. **Multi-start / symmetry-break** — prove it is not a sampling artifact
   (broaden beyond iteration 1's 4 fixed seeds).
2. **xTB cross-check** — GFN2-xTB on the same isodesmic/isomeric comparison;
   agreement strengthens, disagreement is itself a finding.
3. **Literature / novelty check** (web search) — unreported → headline; known →
   cite. Log every check.

## Workstreams

### A. Infrastructure refactor (prerequisite, do first)
The monolith blocks a *designed* series. Refactor the experiment layer into a
declarative format, reusing the clean patterns already in the repo.

- Introduce a `Hypothesis` dataclass + registry mirroring the frozen `System`
  dataclass in `uma_core/systems.py` (which the 100 hypotheses currently
  bypass). Each hypothesis = data: id, SMILES set, comparison type
  (isomeric / isodesmic / geometric / spin-gap), expected value + source,
  tolerance, tags, rigor-gate flags.
- **Unify duplicated helpers.** Single `build()` and `relax()` with one
  consistent `fmax` (pick 0.03 eV/Å; iteration 1 diverged 0.03 vs 0.05 across
  `mechanisms.py` / `run_uma.py` / `explore.py`). Deduplicate `rotation_matrix`
  (`mechanisms.py` vs `stress_test.py`).
- Add a reusable **dual-method runner** that evaluates every comparison under
  both UMA and GFN2-xTB and emits one shared result schema (extend
  `run_xtb.py` / `report.py` rather than re-inventing). This is what makes the
  gate-2 cross-check uniform instead of per-hypothesis ad hoc.
- Keep iteration-1 `mechanisms.py` results intact for provenance; port the
  carbocation-relevant hypotheses into the new registry rather than deleting.

Critical files: `uma_core/systems.py` (pattern to reuse), new
`uma_core/hypotheses.py` (registry) + `uma_core/run_compare.py` (dual-method
runner), `uma_core/report.py`, `uma_core/explore.py` (multi-start).

### B. Primary science — the carbocation-stabilization error class
Design and run a **cation series** spanning the stabilization axis, all as
isodesmic/isomeric comparisons so references cancel:

    methyl → 1°/2°/3° → allyl/benzyl → vinyl → cyclopropylcarbinyl →
    non-classical/bridged (2-norbornyl) → bridgehead (1-norbornyl) →
    terpene C₁₀H₁₇⁺ (α-terpinyl and friends)

For each, quantify **where and by how much UMA over- vs under-stabilizes**
relative to xTB and to literature/experimental hydride-affinity data. Anchor to
a common reference (e.g. isodesmic hydride-transfer against a fixed reference
cation) so numbers are comparable across the whole series — this fixes
iteration 1's "estimate, not measurement" weakness for the methyl gap.

- **Chase B5 (vinyl):** is the 52.8 kcal/mol bridged-below-classical gap real or
  over-stabilization? Multi-start both isomers, xTB cross-check, literature the
  known ~4–5 kcal/mol gap.
- **Unify** T4 (methyl), B5 (vinyl), B4/T6 (bridgehead) into one coherent,
  quantified error class with a mechanism story (empty-p localization vs
  geometry-forced non-planarity).

Test the working hypothesis explicitly and report the quantified map (a table of
UMA vs xTB vs reference, with the signed error per cation).

### C. Secondary — open-shell / degenerate failures (lower priority)
Document tightly as a distinct class: Cp⁺ antiaromatic Jahn–Teller miss +
triplet anomaly, dichlorocarbene singlet–triplet sign error, loose CH₂ gap.
Same rigor gates. Keep separate from the carbocation thesis (it is off the
domain axis but a real secondary story).

### D. Demote off-domain (F₂ / charge-shift)
Quick generalization check only (per reviewer T5): do other homolytic
charge-shift bonds show the same non-monotonic turnover? Judge relevance to
carbocation/terpene chemistry; shelve if tangential. Do not expand.

### E. Constructive output — retraining prescription
Name the specific molecule classes and reference data whose addition would make
an MLIP transferable to carbocation / natural-product mechanism: non-classical /
bridged carbocations, bridgehead ions, terpene rearrangement intermediates,
small electron-deficient cations (CH₃⁺-like). Deliverable: a `RETRAIN_SPEC.md`
listing gap classes + candidate reference systems + what to compute for each.

### F. Park the DFT-dependent numbers
Keep B1 (α-terpinyl global-min conformer) and B2 (tert→sec gap) as clearly
scoped `DFT_REQUESTS.md` items, firmed up with multi-start + xTB + literature so
they are ready to hand off the moment DFT is available. Do not pretend-close
them.

## Loop discipline (unchanged from `CLAUDE.md`)
One hypothesis at a time, wrinkle-driven; validate every structure; multi-start
always; dated `DIARY.md` + `HYPOTHESES.md` ledger + `UMA_FLAWS.md`; commit each
batch. Keep two judgments running: the reviewer's novelty taste (what is a
headline) and the agent's own correctness judgment (what is true / an artifact) —
do the boring correctness work because it licenses the exciting claims.

## Defaults chosen (open questions from `NEXT_ROUND.md`)

- **DFT-free by design.** Assume no DFT this iteration; B1/B2/T4 stay parked +
  literature-anchored, ready to spec if DFT appears.
- **Priority:** carbocation-stabilization errors (primary) → open-shell/spin
  (secondary) → off-domain F₂ (demote). Matches the reviewer verdicts.
- **Audience:** write for the MLIP-methods × physical-organic overlap (the "sold
  as general, here is where it breaks on mechanism" framing); this is the
  reviewer's implied venue.

## Verification (how to check iteration 2 end-to-end)

1. `python -m uma_core.stress_test` — instrument still 6/6.
2. Run the new registry both methods:
   `python -m uma_core.run_compare <cation-series>` — confirm each comparison
   emits UMA + xTB numbers into the shared schema.
3. Reproduce each reported flaw via its hypothesis id
   (`python -m uma_core.mechanisms <id>` or the new runner) and confirm the
   multi-start + xTB + literature gate fields are populated.
4. Confirm the primary deliverable: a UMA-vs-xTB-vs-reference table for the
   cation series with signed per-cation errors, plus `RETRAIN_SPEC.md`.
5. Ledgers updated (`HYPOTHESES.md`, `UMA_FLAWS.md`, `DIARY.md`) and each batch
   committed.
