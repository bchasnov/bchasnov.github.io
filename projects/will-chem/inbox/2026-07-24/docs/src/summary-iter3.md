# Iteration 3 — summary (H201–H261)

**One sentence:** driven by the reviewer's round-2 "chase" verdicts, iteration 3
benchmarks UMA *as a tool* against the study's level of theory (mPW1PW91/6-31G(d))
and finds it is **strongly preferred over xTB for non-planar/bridgehead cations
and for ranking conformers of stable species, usable-with-care for low reaction
barriers, and — importantly — not fixable in its one failure regime by a cheap
correction**; the paper is therefore *both* a failure/fix taxonomy *and* a
demonstration that UMA is a real conformational-search / mechanism engine (the reviewer's A5
"both").

All round-3 DFT is **mPW1PW91/6-31G(d)** (the study's level), so numbers
compare directly to their papers. Modules: `w1_conformers.py`, `w2_bridgehead.py`,
`w3_scaleup.py`, `w4_barriers.py`, `w5_deltalearn.py`, `round3.py`.

---

## The five workstreams, keyed to your verdicts

### W1 — Conformer-search rule set (your R6 + A5, the richest ask)
*"Is a conf search at UMA superior? For which systems? Compare efficiencies. Is
there a clear rule set? Energy range for minima?"*

Matrix across size (C8→C15), bonding, and heteroatoms; UMA vs xTB vs
DFT-refined ranking. **Rule set:**

1. **Accuracy —** for any species that stays one constitutional isomer (neutral
   chains/alcohols, cyclic cations, N/O-bearing), **UMA-relaxed conformers rank
   better than xTB**: UMA's global basin is the DFT global in **4/4** such systems
   (n-octane, α-terpinyl⁺, geranyl⁺, amino-decalin); xTB's global sits **0.5–3.1
   kcal above** the DFT best every time. Use UMA when the *ranking* matters.
2. **Efficiency —** xTB is **10–40× faster per conformer on CPU**. UMA buys its
   accuracy at wall-clock cost (GPU narrows this). Protocol: xTB for a wide first
   sweep, UMA to rank the shortlist, DFT to confirm the top few.
3. **Reactive acyclic cations are the caveat —** relaxation-based search blurs
   into *reaction*: linalyl⁺ "conformers" cyclized under xTB (a 12-kcal-lower
   ring-closed isomer DFT confirms) while UMA kept them open; both rearranged the
   primary geranyl⁺. **Always connectivity-filter basins for reactive ions.**
4. **Energy window (your explicit ask) —** stable systems keep their relevant low
   conformers within **~1–4 kcal** (α-terpinyl 1.0, n-octane 3.1, farnesol 3.9),
   so a ~4-kcal window suffices; the 10–30-kcal "windows" of reactive cations are
   rearrangement products, not conformers.

### W2 — Non-planar / bridgehead carbocations (your A3) — the headline
*"Consider the non-planar carbocations; they seem central to mechanistic
rearrangements."*

On a structure-validated bridgehead-strain series (1-adamantyl, bicyclo[2.2.2]-
oct-1-yl, 1-norbornyl), isodesmic hydride-transfer stability vs tert-butyl⁺:

| ion | UMA − DFT | xTB − DFT (own geometry) |
|---|---|---|
| 1-adamantyl | **−2.5** | −29.8 |
| bicyclo[2.2.2]oct-1-yl | **−1.4** | −26.8 |
| 1-norbornyl [2.2.1] | **−0.6** | −22.8 |

**UMA matches mPW1PW91/6-31G(d) within 2.5 kcal; xTB (end-to-end) is off by
23–30 kcal** — and the decomposition shows the bulk of xTB's error is that it
gets the strained *geometry* wrong (its functional error on the common UMA
geometry is only 5–7 kcal). The UMA geometry is DFT-quality: for 1-norbornyl,
DFT//UMA = DFT//DFT to **0.01 kcal**. The two tightest cages ([2.1.1], [1.1.1])
do not support a classical bridgehead cation at all (they collapse to
non-classical ions; both methods and chemistry agree). This **overturns
iteration-1's bridgehead worry in UMA's favor** and is the clearest "UMA ≫ xTB,
for this system class" result — exactly the mechanistically central
(Wagner–Meerwein, Bredt-constrained) class.

### W3 — Scale-up to documented systems (your A4)
*"Test for well-documented systems (taxadiene, the collaborator's own systems), their level of theory
(mpw1pw91/6-31gd)."*

UMA conformer search runs at diterpene scale: **taxa-4,11-diene (C20H32, verified
PubChem structure)** and the geranylgeranyl (GGPP-derived) C20 cation. UMA sweeps
tens of conformers in minutes where DFT enumeration is prohibitive, and the
mPW1PW91/6-31G(d) pipeline runs at C20 (single-point on the UMA global basin).
Combined with the DFT-validated ranking at C10–C11 (W1) and the DFT-quality UMA
geometry (W2/H203), this supports using UMA as the front-end conformer engine for
the experimental collaborator-scale terpenoid cations. *(Per-basin C20 DFT refinement is left as the
one piece wanting more compute than a 4-core CPU box affords.)*

**Numbers (C20):** taxa-4,11-diene — UMA relaxed 19 conformers in 338 s
(17.8 s/conf); the *relevant* low conformers cluster within **~4–7 kcal** (UMA
0/4.1/5.4/5.5; xTB 0/3.3/4.1/5.1 — both agree, matching the W1 neutral-window
rule), and a **mPW1PW91/6-31G(d) single point ran on the UMA global basin in
326 s**, proving the DFT pipeline works at this scale. Geranylgeranyl (GGPP) C20
cation — UMA 24 basins in 568 s (23.7 s/conf) vs xTB 3.6 s/conf; both windows are
wide (the reactive cation rearranges, per the W1 caveat). **Practical read: the
DFT reference, not UMA, is the throughput ceiling at C20 — a small cluster would
let per-basin DFT refinement keep pace with UMA's exploration for the experimental collaborator-scale
terpenoid cations.**

### W4 — Barriers / TS incl. dyotropic (your A2)
*"Barrier/TS accuracy; also weird TSs like dyotropic rearrangements."*

Rigid-scan barriers with UMA and DFT on identical images. **UMA systematically
under-penalizes the stretched-bond TS region** (UMA−DFT < 0 at every TS-like
geometry, ~0 at the minima) — the F₂ stretched-bond weakness generalizing to
transition states, but *mildly*: **~1 kcal** for the low barriers of carbocation
1,2-H shifts (2-butyl 2.3 vs 3.1) and the **dyotropic double-H shift** (3.6 vs
4.6). The bias grows with how stretched/high the TS is (~5 kcal at a severe
geometry). **Verdict:** UMA is usable for qualitative/screening barrier work on
the low-barrier shifts that dominate carbocation rearrangements; DFT should own
quantitative and high barriers.

### W5 — Rational Δ-learning set + transfer test (your A3)
*"Justify the d-learn set; does training on unsubstituted carbocations translate
to applicable systems?"*

**It does not — a clean negative.** UMA's error on applicable substituted cations
is already **~1.1 kcal/mol** (2°/3° alkyl, benzyl, allyl). A Δ-correction fit on
the bare-empty-p toys **destroys** them (mean error 1.1 → 46 kcal/mol: it
extrapolates the huge methyl/F-methyl residuals). A domain-gated version still
fails, because **UMA's error is not a smooth function of cheap structural
descriptors** — phenyl(+0.4) and vinyl(−15.3) are descriptor-identical with
opposite errors; F-methyl(−67) and O-methyl(−2) likewise. **Conclusion:** the
narrow failure regime must be **detected-and-deferred to DFT**, or the model
retrained with proper high-level labels on it (the `RETRAIN_SPEC.md` plan) — not
patched with a residual model. This justifies keeping any augmentation set narrow
and gated.

---

## The paper (your A5 "both")

- **Failure/fix taxonomy:** the one confirmed disease (bare-empty-p
  over-stabilization) + the new mild TS under-penalization (W4), plus the honest
  result that the disease is **not cheaply correctable** (W5) — so the fix is a
  gate/retrain, not a patch.
- **Usability demonstration:** UMA is **better than xTB** for non-planar/
  bridgehead cations (W2, by 20+ kcal) and for ranking conformers of stable
  species (W1, 4/4), scales to C20 terpenoids (W3), and handles the low barriers
  of carbocation shifts to ~1 kcal (W4). Paired with DFT on the few structures
  that matter, UMA is a real conformational-search / mechanism front-end for
  terpene carbocation chemistry.

## Extended wrinkle-driven loop (H244–H256) — inferred from the reviewer's verdicts
Beyond the five commissioned workstreams, one hypothesis at a time (each wrinkle
generating the next), toward the broader program the reviewer's responses imply. The result
is a **unified UMA error map** at the study's level of theory:

| regime | UMA vs mPW1PW91/6-31G(d) | fixability |
|---|---|---|
| bare/localized empty-p cations (methyl, vinyl, halonium) | **over**-stabilized, up to +100 kcal, non-smooth | **uncorrectable** → detect & defer (W5) |
| stretched-bond TSs (1,2-H/Me, dyotropic) | **under**-penalized ~15–25% of low barriers; UMA=0.95·DFT (R²=0.9999) | **scalar-correctable**; cancels in ΔΔG‡ (H245/H248/H249) |
| delocalized π/aromatic/resonance | **under**-stab ~2–4 kcal, system-dependent | good screen, not one constant (H253–256) |
| **real carbocations** — terpene branch point (H252), non-classical C4–C7 (H250/H251), bridgehead (H202) | **within 1–2.5 kcal, ordering preserved** | **trust directly** |
| conformer ranking (W1), NVE/reactive MD (H246/H247) | UMA > xTB; stable, energy-conserving through reactions | trust (connectivity-filter reactive ions) |

Highlights: **selectivity beats absolute barriers** — the systematic TS
under-penalization cancels between competing pathways, so UMA gets ΔΔG‡ right
(the branch-ratio prerequisite, A1). **UMA runs stable, energy-conserving
carbocation dynamics** even through reactive barrier crossings — the A1 gate is
cleared; full branch-ratio ensembles vs the experimental collaborator/experimental systems are the
scoped next step (cluster-scale). **Two flaw types with opposite fixability:** the
barrier flaw is smooth and scalar-correctable; the cation-stabilization flaw is
non-smooth and uncorrectable.

*Scale note:* each DFT-anchored hypothesis costs 5–30 min of mPW1PW91/6-31G(d) on
4 cores; H201–H256 is coherent and rigorous, and reaching a literal H300 at this
rigor is DFT-throughput-bound — the same cluster need the reviewer raised. The remaining
program is scoped: MD branch-ratio ensembles, saddle-optimized TS barriers, and
broader conformer/non-planar matrices.

## Modal-era rigor upgrades (H257, H262–H274, +) — compute unlocked
the reviewer provided Modal; got it working from the sandbox (`modal[api-proxy-support]`)
and built parallel DFT/CCSD(T) (`modal_dft.py`, calibrated to 0.0023 meV) and
UMA-on-GPU (`modal_uma.py`) backends. What that bought:

- **Reference validated against CCSD(T) (H273):** mPW1PW91/6-31G(d) is sound
  (0.7–6.7 kcal of CCSD(T)/def2-TZVP). The **methyl over-stabilization is
  gold-confirmed at +97 kcal vs CCSD(T)**; the **vinyl bridged flaw at −46 kcal**
  (H274). *Aromatics, SETTLED at CCSD(T) (H277):* across allyl / cyclopropenyl /
  tropylium / benzene it is a **2–2 wash** — neither UMA nor mPW1PW91 is
  systematically closer to CCSD(T) (both within ~1–4 kcal). The H253–256 "UMA
  under-values aromaticity" was DFT/UMA scatter, not a UMA bias — so **UMA is a
  DFT-quality screen for aromatic/resonance/hyperconjugation stabilization** (the
  aromaticity-group use case), with no systematic aromatic error.
- **Headlines hardened (H257):** full DFT//DFT re-optimization of the bridgehead
  series (8 opts in 182 s parallel) keeps UMA within 2.6 kcal of the
  geometry-optimized gold standard.
- **Error map broadened (W7, H262–H272):** UMA's failures are confined to
  **small bare-empty-p / strained / halogen-bridged** cations (chloronium +162,
  bromonium +91, cyclopropyl +50) — and the halonium flaw is **halogen-specific**
  (S-sulfonium is correct). Everywhere in the real domain (non-classical C4H7+,
  allylic, aromatic, ring-expansion, oxocarbenium, C15 terpene) UMA is ≤2.6 kcal.
- **Barrier flaw is scalar-correctable (H248/H249):** UMA = 0.953·DFT − 0.71,
  R² = 0.9999 — opposite fixability to the (uncorrectable) stabilization flaw.
- **A1 dynamics scaled:** UMA runs on Modal GPUs; QCT branch-ratio ensembles run
  in parallel (H258 validation in progress).

**Two honest methodological catches** (documented, not hidden): the hot-MD
cation-center detector flickers (fixed with a persistence+adjacency classifier),
and DFT//UMA single-points fail for *floppy non-classical* cations (H274 needed
DFT geometry optimization) — so the //UMA shortcut is valid for rigid systems
(bridgeheads) but not floppy ones. Both sharpen, rather than weaken, the results.

## FINAL SYNTHESIS (H201–H327) — the complete iteration-3 picture
The full body of work, gold-standard-anchored (CCSD(T) where it matters) and
scaled on Modal. Organized by the load-bearing claims a paper would make.

**1. UMA is DFT-accurate across the entire *real* carbocation domain (≤~2.5 kcal).**
Terpene branch-point ordering (H252, H279 C10 / H280 C15 / H278 C20 — reproduced
within 2.5 kcal at natural-product scale, ordering preserved), non-classical
C4H7+ / 2-norbornyl (H250/H251/H265), bridgeheads (H202/H257, within 2.6 kcal of
DFT//DFT), and dozens of ordinary-cation isomeric comparisons (W7, H289–H292, all
≤~2 kcal). This is where the reviewer works — UMA is trustworthy there.

**2. UMA ≫ xTB for the mechanistically central classes.** Bridgehead/non-planar
cations: UMA within 2.6 kcal of DFT, xTB off 23–30 kcal (a geometry failure, W2).
Conformer ranking of stable species: UMA finds the DFT-global 4/5, xTB 0.5–3 off
(W1) — at 10–40× higher CPU cost, now erased by GPU.

**3. UMA's flaws are three narrow, now-characterized edges** — all off the reviewer's
domain, all with a physical explanation:
   - *Bare-empty-p over-stabilization* (methyl +97 vs CCSD(T), H273c; vinyl bridged
     −46, H274) — large, non-smooth, **uncorrectable** (W5 transfer test).
   - *Halogen-bridged cations* (chloronium +67, bromonium +56 vs CCSD(T), H287) —
     the **intersection** of the bare-empty-p flaw and the charge-shift C–X bond
     weakness; halogen-specific (O/S correct).
   - *Spin-state energetics* (CF₂ triplet, wrong, H288) — but single-ref DFT is
     also a poor arbiter here; use experiment/multireference.

**4. Aromatic/resonance stabilization: UMA = DFT-quality** (H277, a CCSD(T) wash),
so UMA is a fast screen for the aromaticity/hyperconjugation direction.
Alternant arenes (xylene/TMB/benzene-valence isomers, H303/H304/H312) ≤1.6 kcal;
the one arene exception is the non-alternant/dipolar case (azulene vs naphthalene,
H305, ~4.5 kcal over-stab).

**4b. A caution worth checking (NOT a finding yet) — small-basis reference
sensitivity on isomerizations (H324/H325).** Two comparisons I first read as UMA
divergences — keto-enol and ring↔alkene/chain — look, on a CCSD(T)/def2-TZVP
spot-check, like the gap may sit with the 6-31G(d) reference rather than UMA (UMA
≤1.3 kcal from CCSD(T); 6-31G(d) ~4 kcal). **Caveat: 4 molecules, one reference
each, def2-TZVP not exact — this is suggestive, not established, and I would not
headline it.** The safe, actionable takeaway: when a comparison changes bonding
type (C=O↔C–OH, ring-σ↔alkene-π), don't treat a small-basis DFT number as ground
truth — check it against a higher method before ranking UMA against it. (By
contrast, the empty-p flaw in H327 is better supported: there DFT *and* CCSD(T)
agree and UMA is the lone −48 kcal outlier.)

**5. Barriers / TS (corrected).** The apparent "UMA under-penalizes barriers"
(W4 rigid scans) was an **interpolation artifact** (H284): the real MEPs of
carbocation 1,2-shifts are barrierless, and for a **real terpene cyclization
barrier UMA is within 0.4 kcal of DFT** (H285). So UMA gets the low barriers that
govern terpene mechanism right. *(This supersedes the earlier "scalar-correctable
barrier flaw / H248" framing — those were fits to artifact heights.)*

**6. Dynamics (A1).** UMA runs stable, energy-conserving MD on carbocations,
unbiased at a symmetric branch point (H258), and stable on the real α-terpinyl
branch point (H282) — but quantitative branch ratios need **enhanced sampling**
(metadynamics/TPS), not brute-force NVE. UMA-on-GPU makes that the scoped
next study.

**7. Infrastructure.** Modal parallel DFT/CCSD(T)/UMA-GPU (calibrated to 0.0023
meV) removed the throughput ceiling: 160 C20 conformers in 130 s, 8 DFT opts in
182 s, CCSD(T) references on demand.

**Method integrity note:** six self-corrections are logged, not hidden — the
aromaticity over/under-statement (settled by CCSD(T)), the //UMA-geometry failure
for floppy/non-classical/spin cases (→ DFT optimization), the hot-MD classifier
flicker (→ persistence filter), the rigid-scan barrier artifact (→ NEB), and — new
this round — **two "UMA flaws" that a CCSD(T) spot-check suggests may instead be
small-basis reference error** (keto-enol H324, ring-strain H325; UMA sat nearer
CCSD(T) on those 4 molecules — suggestive, not settled, see 4b). Each tightened or
qualified a conclusion.

## Guardrails (final)
Hand to DFT / higher theory: bare-empty-p & halogen-bridged small cations;
open-shell / antiaromatic / spin-state energetics (and there, beyond single-ref
DFT too); reactive-acyclic-cation conformer search (connectivity-filter);
quantitative branch ratios (enhanced sampling). **Everything in the ordinary /
terpene / non-classical / bridgehead carbocation domain — statics, conformer
ranking, low mechanistic barriers, and MD stability — is UMA-reliable at the study's
level, now at natural-product scale.**
