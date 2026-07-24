# Iteration 1 — independent review

Reviews branch `uma-carbocation-study/iteration-1` (commits through `ff0222c`).
Companion to the reviewer's triage in `NEXT_ROUND.md`; this is a second,
independent read focused on what is solid, what is shaky, and what iteration 2
must fix. Written on `claude/iter1-review-iter2-plan-4wkrqn`.

## What iteration 1 did

A single autonomous agent ran a **100-hypothesis, DFT-free campaign** (H1–H100)
using UMA (`uma-s-1p1`, `omol` task, CPU) as a mechanism-exploration engine for
carbocation / terpene physical-organic chemistry (the experimental group × the aromaticity group
framing). Self-reported tally (`HYPOTHESES.md`): **85 confirmed, ~7 genuine UMA
flaws, rest mixed/refuted-but-informative.** All comparisons are
isodesmic/isomeric or geometric so UMA's absolute-energy reference cancels.

**Verification meant, in the absence of DFT:** (a) isodesmic/isomeric energy
comparisons; (b) geometry dissection (connectivity, per-atom H counts, bond
lengths, angle sums) to confirm what actually relaxed and catch silent
rearrangements; (c) symmetry-breaking re-relaxation to tell minima from saddles
(H15); (d) multi-start seeding (4 fixed seeds) to escape single-start traps;
(e) spin-channel calibration on O₂/CH₂ before trusting any spin gap; (f) a 6/6
instrument stress test (`stress_test.py`).

## Genuine flaws found (the load-bearing output)

| Flaw | System | Verdict | Confidence |
|---|---|---|---|
| Antiaromatic Jahn–Teller missed | Cp⁺ (C₅H₅⁺) relaxes to symmetric pentagon, holds under symmetry-break (H14/H15) | real | high — tightly bounded by H26/H27/H30 |
| Spin-state energetics wrong | Cp⁺ triplet −200 kcal/mol; CCl₂ triplet GS (should be singlet); CH₂ gap 2× loose (H16/H77) | real, sign error | high on sign |
| Bredt bridgehead under-destabilized | 1-norbornyl **3.1 below** 2-norbornyl (H28) | sign wrong vs Bredt | medium — magnitude wants DFT |
| Methyl cation over-stabilized | CH₃⁺ ranks above tBu; ladder otherwise textbook (H43); methyl *radical* fine (H51) | real (existence) | high existence, **soft magnitude** |
| F₂ charge-shift dissociation | non-monotonic, plateau ~149 vs BDE ~38 (H55); C–C homolysis fine (H24) | real turnover | medium — partly restricted-singlet framing |
| Small non-classical cations over-stabilized | bridged vinyl 52.8 below classical (~4–5 real, H37); halonium 49–67 above open (H23) | **suspected/unverified** | low — no reference |

The reviewer's reframing (`NEXT_ROUND.md`): *the flaws are the paper, the
successes are the setup* — and the interesting-marked flaws cluster into **one
carbocation-stabilization error class**: UMA over-stabilizes small,
localized-empty-orbital cations (methyl, vinyl) and under-penalizes cations that
cannot planarize/delocalize (bridgehead).

## What is solid

- The **instrument layer** is sound: `stress_test.py` (determinism, translation/
  rotation invariance, charge sensitivity, basin stability), `run_uma.py` (gated-
  model handling), `run_xtb.py`, `systems.py` (clean frozen dataclass).
- The **skeptical discipline** is the best part: rearrangements verified by
  geometry, spin/charge channels calibrated before use, single-start artifacts
  caught (ethyl H6→H9, halonium H21→H23), and DFT-dependent claims explicitly
  *refused* rather than faked (`DFT_REQUESTS.md`).
- Flaw bounding is careful: the Cp⁺ blind spot is pinned to the open-shell/
  degenerate cation specifically (neutral cyclobutadiene H27 and cyclopropenyl
  anion H30 are handled correctly).

## What is shaky — the gaps iteration 2 must close

1. **No systematic second-method cross-check.** The 100 hypotheses are
   **UMA-only**; xTB was run only on the original 7 systems (`run_xtb.py`), not
   per hypothesis. Most "UMA is right/wrong" verdicts rest on UMA-internal
   consistency + textbook numbers. This is the single biggest rigor gap.
2. **Electronic ΔE compared directly to experimental ΔH** throughout (no ZPE/
   thermal). Acknowledged for a few cases (Diels–Alder H57, benzene ASE H84) but
   elsewhere treated as unqualified agreement — some "hits" may be fortuitous
   cancellation.
3. **Magnitudes stated as measured when they are estimates.** The methyl
   "~60–80 kcal/mol" over-stabilization is *inferred* from where the internal
   ladder would place CH₃⁺; the existence is well-argued, the number is not
   measured and should not be quoted as such.
4. **Vertical barriers framed inconsistently** — sometimes "upper bound"
   (correct, `mechanisms.py:905`), sometimes "matches experiment" (H25 allyl,
   H73 amide overestimates).
5. **Crude heuristic classifiers are load-bearing.** `classify_carbocation` /
   distance-only `fingerprint` (`explore.py`) drive labeling and clustering and
   already produced one wrong call (H3 labeled 2-norbornyl "secondary").
6. **Multi-start is only 4 fixed seeds** — too thin to claim global minima for
   floppy terpene cations.

## Code architecture

`mechanisms.py` is a **2,682-line append-only monolith** and the main technical
debt: 97 near-identical hardcoded `h4…h100(calc, model) -> dict` functions, each
embedding SMILES literals, bespoke geometry analysis, and hardcoded pass/fail
thresholds, with no shared `Hypothesis` type or result schema. `main()` sits at
line 2460 with H90–H100 defined *after* it; the registry is split across
`EXPERIMENTS = {...}` (2399) and `EXPERIMENTS.update({...})` (2676). Helpers are
duplicated across modules (`build`, `relax` with divergent fmax 0.03 vs 0.05,
`rotation_matrix`). Notably, `systems.py` *is* clean and data-driven — but none
of the 100 hypotheses use it. Refactoring the experiment layer into a
declarative format is a prerequisite for the "designed cation series" iteration 2
calls for and for applying the rigor gates uniformly.

## DFT-blocked items (parked, not closed)

From `DFT_REQUESTS.md`, all unrun: **P1** α-terpinyl cation relocated global
minimum (2.30 kcal/mol below one-shot; the monoterpene branch-point reference
conformer); **P2** tert→sec C₄H₉⁺ gap (UMA 13.2 vs xTB 15.7); **P2** limonene
protonation energy (no trustworthy number — spans formula + charge); **P2**
bridgehead ΔE; **P2** vinyl/halonium bridged-vs-open magnitudes; **P3**
2-norbornyl non-classical stabilization (needs CCSD(T)).
