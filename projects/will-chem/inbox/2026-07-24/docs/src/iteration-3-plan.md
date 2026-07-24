# Iteration 3 — launch brief (H201–H300)

**You are the iteration-3 agent.** Iterations 1–2 built and DFT/CCSD(T)-confirmed
a failure map of UMA on carbocation chemistry; the domain reviewer (the reviewer the experimental collaborator,
the experimental collaborator group → the aromaticity group) has now triaged round 2 and said *chase*. Your job:
run ~100 hypotheses (H201–H300) driven by the reviewer's verdicts, keeping the same rigor.

Branch: `uma-carbocation-study/iteration-3` (you are on it). Commit each batch.

## Read first
- `feedback/verdicts_2026-07-22.json` — the reviewer's round-2 reactions + comments (the mandate).
- `NEXT_ROUND_3.md` — the reviewer's verdicts decoded into workstreams.
- `SUMMARY_ITER2.md`, `UMA_FLAWS.md`, `RETRAIN_SPEC.md` — what's already established.
- `CLAUDE.md` — the standing loop discipline.

## Environment
Fresh container? Run `bash setup_env.sh` (installs ase/rdkit/tblite + torch/
fairchem for UMA + pyscf/geometric for DFT; needs `HF_TOKEN` with gate access to
facebook/UMA, or a checkpoint at `uma_core/weights/uma-s-1p1.pt`).

## Tooling already in place (reuse it — do not rebuild)
- `uma_core/iter2.py` — dual-method harness: `compare()` (isomeric UMA+xTB),
  `stab_ladder()` (isodesmic hydride transfer), `relax_multistart`, conformer
  fingerprints, durable logging to `results/iter2_results.json`.
- `uma_core/mechanisms2.py` — H101–H200 + the declarative registry (`REG*`);
  add new hypotheses as functions or registry rows.
- `uma_core/run_dft.py` — PySCF DFT/CCSD(T). **Round-3 DFT uses the reviewer's level of
  theory: mPW1PW91/6-31G(d)** → `xc="mpw1pw"`, `basis="6-31g*"` (see `round3.py`).
- `uma_core/synergy.py` — dense UMA conformer sweep → cluster basins →
  DFT-refine. This is the engine for W1 (conformer rule set) and W3 (scale-up).
- `uma_core/round3.py` — H201 non-planar pyramidalization scan (already runs).

## The mandate (the reviewer's verdicts → workstreams), in priority order
Every hypothesis passes the rigor gates: multi-start / validate the structure,
cross-check (xTB and/or DFT), anchor to literature or **mPW1PW91/6-31G(d)**.

**W1 — Conformer-search rule set** *(the reviewer's richest ask: R6 "interesting" + A5)*.
Is UMA *significantly* better than xTB for conformer search, and **for which
systems**? Across a matrix spanning size (C₅→C₂₀), bonding (saturated / allylic /
non-classical), and heteroatoms: measure whether the relevant low/reactive
conformer is found, the window width needed, wall-clock efficiency, and
minimum quality after DFT single-points. **Deliverable: a rule set keyed on
size/bonding/atoms** + the energy range of minima (the reviewer's "standard conformational
search protocols, determine energy range for minima"). Engine: `synergy.py`.

**W2 — Non-planar carbocations** *(A3: "relevant for mechanistic rearrangements")*.
Does UMA get the pyramidalization penalty right? `round3.py` H201 scans it for
tert-butyl/isopropyl vs mPW1PW91/6-31G(d); extend to a bridgehead-strain series
(adamantyl, bicyclo[2.2.2]/[2.2.1]/[1.1.1] bridgeheads) and forced-non-planar
ring cations. Where does the penalty error appear?

**W3 — Scale-up on documented systems** *(A4)*. Taxadiene and the experimental collaborator cascade
cations (C₂₀); UMA conformer search + energetics vs **mPW1PW91/6-31G(d)** and
their published numbers. Validates UMA at natural-product scale.

**W4 — Barriers / transition states** *(A2, chase)*. UMA vs DFT for 1,2-H /
1,2-Me shift and cyclization barriers via NEB; **include a dyotropic
rearrangement TS** (the reviewer's explicit ask); attempt constrained TS searches (A5).
This is the untested regime — UMA failed at stretched bonds (F₂), and a TS is
stretched bonding, so expect trouble and measure it.

**W5 — Rational Δ-learning set + transfer test** *(A3, chase)*. Justify the
augmentation set (bare-empty-p **and non-planar** carbocations); fit a residual
correction on top of UMA; **test whether it improves *applicable* substituted /
mechanistic systems**, not just the training toys (the reviewer's exact concern).

## Framing (the reviewer's A5 "both")
The paper is **both** an MLIP-methods failure/fix taxonomy **and** a demonstration
that UMA is usable for conformational searching / MD despite the failures. Keep
both threads: document where it breaks, and prove where it works.

## Loop discipline (from CLAUDE.md)
One hypothesis at a time, wrinkle-driven; multi-start always; validate every
structure; keep a dated `DIARY_ITER3.md`, the `HYPOTHESES_ITER3.md` ledger, and
the `UMA_FLAWS.md` log; commit each batch; report failures plainly. Two judgments
running: the reviewer's taste (novel / domain-relevant → the paper) and yours (true /
artifact → the science). Do the boring correctness work — it licenses the
exciting claims.

## Start here
1. `bash setup_env.sh`
2. `python3 -m uma_core.round3` (H201, non-planar — confirms the pipeline).
3. Build the W1 conformer-search matrix (highest-value, answers the reviewer's richest
   comment), then proceed W2→W5.
