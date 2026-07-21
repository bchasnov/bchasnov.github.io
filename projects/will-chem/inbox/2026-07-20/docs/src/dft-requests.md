# DFT escalation requests

Places where UMA + GFN2-xTB are **not sufficient** to settle a scientific claim,
and a DFT (or higher) reference is warranted. Per your instruction, I'm raising
these here rather than running DFT unilaterally. Each item states the question,
why the fast methods can't answer it, and exactly what I'd compute.

Priority: **P1** = blocks a claim we'd want to publish/report; **P2** = would
materially strengthen a number; **P3** = nice-to-have / known hard problem.

---

## P1 — Confirm the relocated α-terpinyl cation global minimum

**Question.** The UMA conformer search moved the α-terpinyl cation global
minimum **2.30 kcal/mol below** the single-guess geometry the one-shot pipeline
reported (3 basins within 3.2 kcal/mol). Is UMA's ordering of these conformers
correct?

**Why fast methods can't settle it.** The basins are within ~2–3 kcal/mol — the
regime where UMA and GFN2-xTB routinely reorder conformers. This cation is the
central branch-point of monoterpene cyclization (the experimental collaborator's territory), so
its lowest conformer sets the reference for every downstream partitioning
energy. A wrong reference conformer biases everything.

**Proposed DFT.** Single-point (or loose reopt) on the top ~3 UMA conformers
(`uma_core/results/alpha_terpinyl_cation.gmin.xyz` + the two runner-up basins)
at, e.g., **ωB97X-D/def2-TZVP** with an implicit solvent or gas phase to match.
Deliverable: DFT relative energies of the 3 basins → confirm/relabel the global
minimum. ~3 single points; cheap.

## P2 — Tertiary vs secondary C4H9+ gap to chemical accuracy

**Question.** What is the tert-butyl → sec-butyl cation energy gap? UMA says
**13.2**, GFN2-xTB says **15.7** kcal/mol (Δ = 2.5).

**Why fast methods can't settle it.** The *ordering* is robust (both put
tertiary lower — no DFT needed for that). The *magnitude* is not: 2.5 kcal/mol
is larger than chemical accuracy, and this gap is the thermodynamic driving
force we cite for hydride/methyl shifts. Stating a number with ±1 kcal/mol
confidence needs a wavefunction/DFT reference.

**Proposed DFT.** These are 13-atom isomers — trivial. Optimize tert-butyl and
sec-butyl cations at **ωB97X-D/def2-TZVP** (ideally a **DLPNO-CCSD(T)/def2-TZVP**
single-point on top) and report the gap. Isomeric, so the reference cancels —
directly comparable to the UMA/xTB numbers.

## P2 — Monoterpene protonation energy (limonene → α-terpinyl)

**Question.** How favorable is protonating limonene's ring alkene to the
α-terpinyl cation?

**Why fast methods can't settle it.** As documented in the report, this ΔE spans
different formula *and* charge, so UMA vs xTB is not directly comparable (their
absolute energy zeros differ — the ~160 kcal/mol apparent gap is a reference
artifact, not chemistry). We currently have **no trustworthy number** for this.

**Proposed DFT.** A consistent scheme at one level of theory: either an explicit
proton-affinity calculation, or an **isodesmic** reaction that cancels the
proton reference (e.g. hydride/proton transfer against a reference pair).
Deliverable: one defensible protonation/relative energy at DFT.

## P3 — 2-norbornyl cation: non-classical vs classical energy

**Question.** By how much does UMA/xTB favor the σ-bridged non-classical
2-norbornyl cation over a (constrained) classical secondary form?

**Why fast methods can't settle it.** Both UMA and xTB give a *symmetric bridged*
geometry (good — see diary H3), but the **energetics** of non-classical vs
classical norbornyl is a textbook-hard case that historically needed
**CCSD(T)** — DFT alone is borderline and functional-dependent. So this is
really a "high-level theory" request, not just DFT.

**Proposed calc.** If we want to make a quantitative non-classical-stabilization
claim: CCSD(T)/CBS-ish single points on the bridged minimum and a constrained
classical structure. Otherwise, keep the claim qualitative ("both methods
localize a symmetric bridge"), which is already well-supported and needs no
escalation.

## P2 — β-halonium: bridged vs open energetics (from H21/H23)

**Question.** UMA finds symmetric bridged halonium ions (F/Cl/Br) as genuine
local minima, but places them **49–67 kcal/mol above** open/rearranged forms. For
β-F that is chemically reasonable (F bridges poorly; the α-fluoro cation sink is
lower, consistent with H20). For **Cl and Br the ordering is surprising** —
anchimeric assistance normally makes the bridged bromonium/chloronium the
minimum. UMA's neutral dissociation asymptote is fine (H24), so this is specific
to the charged bridged-vs-open comparison.

**Why fast methods can't settle it.** It is a bridged-vs-open energy ordering for
a charged species where UMA's behavior is anomalous and xTB is not a reliable
arbiter for halonium bridging. Needs a DFT reference.

**Proposed DFT.** Optimize the bridged and open 2-chloroethyl / 2-bromoethyl
cations at ωB97X-D/def2-TZVP; report which is lower. Tiny systems.

---

*None of the above has been run. Tell me which (if any) to pursue and on what
compute, and I'll wire it into the pipeline as a `run_dft.py` reference stage
alongside `run_xtb.py`.*

## P2 — bridgehead (1-norbornyl) vs 2-norbornyl cation (from H28)

**Question.** UMA places the 1-norbornyl **bridgehead** cation 3.1 kcal/mol
*below* the 2-norbornyl cation, contradicting Bredt's rule (bridgehead cations
are strongly destabilized). By how much is UMA off?

**Why fast methods can't settle it.** UMA's sign is qualitatively wrong; xTB is
not a trustworthy arbiter for non-classical/bridgehead cation energetics.

**Proposed DFT.** Optimize both C7H11⁺ isomers at ωB97X-D/def2-TZVP (bridgehead
constrained to its cage minimum); report ΔE. Small, cheap. Expectation: bridgehead
well above 2-norbornyl.

## P2 — over-stabilization of non-classical small cations (from H23, H37)

**Question.** UMA places the bridged vinyl cation 52.8 kcal/mol below classical
(known gap ~4–5) and bridged halonium 49–67 kcal/mol from open forms. Are these
gaps real or is UMA over-stabilizing multi-center-bonded small cations?

**Proposed DFT.** ωB97X-D/def2-TZVP on classical vs bridged C2H3⁺ (and the
halonium pairs). Tiny. Expectation: vinyl gap ~4–5 kcal/mol, not ~50.
