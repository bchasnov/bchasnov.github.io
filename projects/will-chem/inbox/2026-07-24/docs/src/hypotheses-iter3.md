# Hypothesis ledger — iteration 3 (H201+)

Continues `HYPOTHESES_ITER2.md`. Driven by the reviewer's round-2 verdicts
(`feedback/verdicts_2026-07-22.json`); decoded in `NEXT_ROUND_3.md`, program in
`ITERATION_3_PLAN.md`. Round-3 DFT reference = **mPW1PW91/6-31G(d)** (the experimental collaborator
level). Every row: multi-start + cross-check (xTB and/or DFT) + literature.

Status: ✅ confirmed · ➖ demoted/not-UMA-specific · 🌀 nuanced · ⏳ open

| # | Workstream | Hypothesis | Status | Key result |
|---|---|---|---|---|
| H201 | W2 non-planar | Pyramidalization penalty (tert-butyl, isopropyl) vs mPW1PW91/6-31G(d) | ✅ | UMA tracks DFT within ~2 kcal over 66–80 kcal penalties → ordinary cations' non-planarity cost is right; push to bridgehead/strained regime next |
| H202 | W2 non-planar | Bridgehead-strain series (adamantyl / [2.2.2] / [2.2.1] / [2.1.1] / [1.1.1]) isodesmic stability + pyramidalization, UMA vs xTB vs mPW1PW91/6-31G(d) | ✅ | **Genuine bridgeheads: UMA within 2.5 kcal of DFT; xTB (own geom) off 23–30 kcal.** xTB's error is mostly *geometry* (drops to 5–7 kcal on the UMA geom). Two tightest cages ([2.1.1],[1.1.1]) collapse to non-classical ions (not classical bridgeheads); UMA still tracks DFT there (±0.4 kcal). |
| H203 | W2 validation | Is DFT//UMA single-point ≈ DFT//DFT for a rigid bridgehead cation? | ✅ | 1-norbornyl bridgehead isodesmic Stab: DFT//UMA −12.88 vs DFT//DFT −12.86 kcal/mol → **shortcut error 0.01 kcal**; UMA geometries are DFT-quality for rigid cages, licensing the //UMA workflow used throughout W2/W1. |
| H210 | W1 conformer | n-octane (C8 saturated) UMA vs xTB conformer search, DFT-refined | ✅ | UMA global basin **is** the DFT global; xTB's is +0.61 kcal. xTB ~37× faster/conf (0.05 vs 1.83 s). |
| H211 | W1 conformer | linalyl cation (C10 reactive acyclic tert-allylic) | 🌀 | **Reversed:** xTB global (a *cyclized* isomer, +10th C–C bond) is the DFT global; UMA stayed open-chain, +12.3 kcal. UMA preserved constitution; xTB crossed the cyclization barrier. Search blurs with reaction. |
| H212 | W1 conformer | α-terpinyl cation (C10 stable cyclic tert) | ✅ | UMA global basin is the DFT global; xTB +3.1 kcal. Tight 1.0-kcal conformer window. |
| H213 | W1 conformer | geranyl cation (C10 reactive acyclic primary-allylic) | ✅ | Both explore rearrangement (wide windows); UMA global is DFT global, xTB +0.45 kcal. |
| H214 | W1 conformer | amino-decalin (C11 neutral amine, heteroatom axis) | ✅ | UMA global is DFT global; xTB +0.96 kcal. 17 basins over 15 kcal (ring-flip + N-inversion). |
| H215 | W1 conformer | farnesol (C15 neutral floppy alcohol) — efficiency/window | ✅ | Both find 17 basins; conformer window 0..3.9 kcal. UMA ~12× slower/conf. Scale-up of the sampling holds. |
| H216 | W1 diagnosis | linalyl basin connectivity: is xTB's low basin a reaction product? | ✅ | Yes — UMA global = open C10H17⁺ (0 rings); xTB global = **1-ring cyclized** C10H17⁺. The H211 reversal is xTB cyclizing, not better conformer search. |
| H260 | W5 Δ-learn | Does a Δ-correction fit on bare-empty-p + toy cations transfer to *applicable* substituted cations? (mPW1PW91/6-31G(d)) | ✅❌ | **Transfer fails, hard.** Raw UMA error on applicable test cations = **1.1 kcal** (already excellent). A global linear descriptor Δ-model fit on the toys makes it **46.2 kcal** — it *destroys* them (tert-amyl +0.4→−72.8) by extrapolating the extreme methyl(−105)/F-methyl(−67) residuals. Training on unsubstituted toys does NOT help applicable systems. |
| H261 | W5 Δ-learn | Can a domain-gated (locality-weighted) correction do no harm? | ❌ | No — still breaks (test 1.1→11.2 kcal; benzyl +2.8→+34.7). **Root cause: UMA's residual is non-smooth in cheap descriptor space** — phenyl(+0.4) and vinyl(−15.3) are descriptor-identical with opposite errors; F-methyl(−67) vs OH-methyl(−2) likewise. No descriptor Δ-model can work. Rational path = **detect-and-defer to DFT** / retrain with proper labels on the narrow bare-empty-p regime, not a correction. |
| H240 | W4 barrier | 2-butyl degenerate 1,2-H shift barrier, UMA vs mPW1PW91/6-31G(d) (rigid scan) | ✅ | UMA 2.3 vs DFT 3.1 kcal; UMA−DFT **−0.8 at the TS peak** (0 at the minima). UMA slightly under-penalizes the stretched TS. |
| H241 | W4 barrier | n-propyl→isopropyl 1,2-H shift | ✅ | Barrierless in both (0.1/0.2 kcal) — the 1° cation is not a minimum; methods agree. |
| H242 | W4 barrier | isobutyl→tert-butyl 1,2-H shift | 🌀 | Absolute barrier (91/96 kcal) is a **rigid-path artifact** (1° relaxes to 3°; linear path unphysical). Geometry-matched signal is real: **UMA−DFT −5.1 at the severely-stretched peak** — under-penalization grows with stretch. |
| H243 | W4 dyotropic | **dyotropic double 1,2-H shift** (the reviewer's explicit ask), 2-butyl | ✅ | UMA 3.6 vs DFT 4.6 kcal; UMA−DFT **−1.0 at the TS peak**. The concerted double-H TS behaves like a normal 1,2-shift; UMA within ~1 kcal. |
| H230 | W3 scale-up | geranylgeranyl (GGPP) C20 cation — UMA vs xTB conformer search at scale | ✅ | UMA 24 basins in 568 s (23.7 s/conf) vs xTB 3.6 s/conf; both wide windows (reactive cation rearranges). UMA tractable at C20. |
| H231 | W3 scale-up | **taxa-4,11-diene (C20H32, the experimental collaborator target)** conformer search + C20 DFT check | ✅ | UMA 19 conformers in 338 s; relevant low conformers within ~4–7 kcal (both methods agree, matches W1 neutral-window rule). **mPW1PW91/6-31G(d) single point ran on the UMA global basin in 326 s** — the DFT pipeline works at C20. DFT is the throughput ceiling (→ cluster). |
| H244 | W4 barrier | 1,2-**methyl** shift (pinacolyl) — does heavier/more-covalent migration worsen the TS under-penalization? | ✅❌ | Barrier UMA 10.2 / DFT 11.9; peak UMA−DFT −1.6. Larger *absolute* error than the H-shift (−0.8) but *smaller as a fraction* of the barrier (13% vs 25%). **The flaw scales with barrier height (sub-linearly), NOT with migrating-group covalency** — hypothesis partly refuted. |
| H245 | W4 selectivity | Does the systematic TS under-penalization CANCEL between two competing shifts from the same cation? (→ branch ratios, A1) | ✅ | Competing 1,2-H shift from the same pinacolyl cation: UMA 42.6 / DFT 45.9 (peak −3.3). **ΔΔG‡(H−Me) = 32.4 (UMA) vs 34.0 (DFT) — selectivity reproduced within 1.6 kcal**, better than either absolute barrier. Cancellation is real (best when competing TSs are similar in height, i.e. at real branch points). **UMA is more trustworthy for *relative* barriers / selectivity than for absolute ones.** |
| H246 | W6 dynamics | **Can UMA run stable, energy-conserving NVE MD on a carbocation?** (the A1 gate) | ✅ | Yes — NVE drift **0.15–0.24 meV/atom** over 0.2 ps, connectivity intact, for both tert-butyl (rigid) and sec-butyl (shift-prone). UMA is usable as a carbocation dynamics engine. |
| H258 | W6/A1 dynamics | QCT branch-ratio validation: does UMA-MD give the symmetry-required 50:50 on the symmetric 3-pentyl cation? (120 traj, Modal GPU) | ✅ | **Yes — clean single-shift split 28:21 = 0.571, within 2σ of 0.50 → UNBIASED**; energy conserved through the reactive event (0.88 meV/atom). The Modal-UMA QCT engine is validated. Caveat: 41% of trajectories fast-scramble to non-adjacent carbons at 1200 K (real multi-shift dynamics) — quantitative branch ratios want lower T / shorter windows. Methods catch: instantaneous center-detection flickers under hot MD → fixed with a persistence+adjacency classifier. |
| H282 | A1 dynamics | Dynamic product exploration from the **α-terpinyl cation** (the monoterpene branch point), 80 hot trajectories on GPU | ✅➖ | UMA-MD is **stable & energy-conserving** (0.78 meV/atom) on the real terpene cation, but at 1200 K/1 ps it **stays monocyclic (78/80)** — the cyclizations to pinyl/bornyl have real barriers that brute-force NVE rarely crosses (2/80 rare events, which UMA handles). **Honest A1 state: engine validated (H258) + stable on real terpenes (H282), but quantitative branch ratios need ENHANCED SAMPLING (metadynamics / transition-path sampling), not plain NVE** — now affordable via UMA-on-GPU (0.5 s/traj). Scoped follow-up. |
| H247 | W6 dynamics | Does UMA MD conserve energy *through* a reactive barrier crossing (the stretched-bond region)? | ✅ | sec-butyl hot NVE (900 K, 1.5 ps): hydride scrambling occurs, composition intact, drift **0.78 meV/atom** — energy conserved through the reactive region. The W4 static under-penalization does **not** corrupt dynamical conservation. (H247b isobutyl→tert-butyl pending.) |
| H248 | W4 meta | Are UMA barriers a systematic linear function of DFT barriers (correctable), not random? | ✅ | **UMA_barrier = 0.953·DFT − 0.71, R²=0.9999** across 0.2–96 kcal (residuals ≤0.6 kcal). The TS under-penalization is a near-perfect ~5% under-slope — a *single scalar* recovers quantitative barriers. Constructive flip-side of the W4 "flaw". |
| H249 | W4 meta | Does the linear barrier correction TRANSFER (train low-barrier, predict high)? — a foil to W5 | 🌀 | Partially: trained on <15-kcal barriers (slope 0.875), it over-predicts the 46/96-kcal barriers by +3/+8 kcal → the ratio **rises with barrier height** (low barriers proportionally more under-penalized). So the correction is *regime-local*, not one universal constant — but **transfers within the carbocation-shift window that matters**. **Key contrast: the barrier flaw is smooth/monotonic/correctable (unlike W5's non-smooth, uncorrectable stabilization flaw). UMA has two flaw types with opposite fixability.** |
| H250 | non-classical | Does UMA over-stabilize the C4H7⁺ non-classical manifold (cyclopropylcarbinyl/cyclobutyl/homoallyl) at mPW1PW91/6-31G(d)? | ✅ | No — **cross-confirmed, max \|UMA−DFT\| = 2.3 kcal** (UMA slightly *under*-stabilizes cyclobutyl). The over-stabilization flaw does NOT extend to σ-delocalized larger carbocations — it's confined to the smallest *bare* bridged ions (vinyl, halonium). |
| H251 | non-classical | Does UMA get the 2-norbornyl (non-classical archetype) vs 1-norbornyl (bridgehead) gap right at mPW1PW91/6-31G(d)? | ✅ | Yes — UMA −3.1 vs DFT −2.8 (UMA−DFT 0.4 kcal). Both put the bridgehead ~3 kcal below 2-norbornyl → the iter-1 "bridgehead below secondary" is **real chemistry, cross-confirmed at the study's level**, and UMA handles the non-classical archetype correctly. |
| H252 | terpene/A4 | Does UMA reproduce the monoterpene C10H17⁺ branch-point ordering at mPW1PW91/6-31G(d)? | ✅ | Yes — **max \|UMA−DFT\| = 1.4 kcal and the ordering is preserved** (pinyl < terpinen-4-yl < α-terpinyl in both). UMA reproduces the terpene-cyclization branch point at the study's exact level of theory. Closes R4/A4 rigorously. |
| H253 | aromaticity | tropylium (6π) vs benzyl (C7H7⁺) aromatic stabilization at DFT | ✅ | Cross-confirmed (ordering right); UMA **under**-values the aromatic gap by 2.6 kcal. |
| H254 | aromaticity | cyclopropenyl (2π) vs propargyl (C3H3⁺) at DFT | ✅ | Cross-confirmed; UMA under-values the aromatic gap by 2.8 kcal. |
| H255 | resonance | allyl vs 2-propenyl (C3H5⁺) resonance at DFT | ✅ | Cross-confirmed; UMA under-values the resonance gap by 1.8 kcal. **Pattern (H253–255): UMA systematically *under*-stabilizes delocalized π/aromatic/resonance by ~2–3 kcal — a small, consistent bias, OPPOSITE in sign to the bare-empty-p over-stabilization.** |
| H256 | aromaticity | Is the aromatic under-valuation a constant bias? benzene/fulvene + naphthalene/azulene (neutral) at DFT | 🌀 | No — benzene/fulvene fits (UMA −1.6) but **naphthalene/azulene reverses: UMA +4.4** (under-stabilizes the non-alternant, dipolar azulene). The aromatic bias is **system-dependent (1.6–4.4 kcal), not a correctable constant** — small for normal aromatics, larger for unusual non-alternant ones. Good screen, not constant-correctable. |

## Infrastructure — parallel DFT on Modal (removes the throughput ceiling)

| # | Workstream | Hypothesis | Status | Key result |
|---|---|---|---|---|
| — | Modal DFT backend (`modal_dft.py`), calibrated vs local PySCF | ✅ | `pip install 'modal[api-proxy-support]'` routes gRPC through the sandbox proxy; MODAL_TOKEN_* already set. Remote mPW1PW91/6-31G(d) on tert-butyl cation = **−4285.996393 eV vs local −4285.996395 eV (0.0023 meV)** → lossless. Single points/optimizations now fan out in parallel via `.starmap`; ~10 s/warm call after a one-time image build. **The DFT bottleneck that capped W3/W4/A1 is lifted.** |
| H257 | W2 rigor | Full DFT//DFT bridgehead validation (parallel on Modal) — does the //UMA shortcut hold across the whole series? | ✅ | **8 DFT geometry optimizations in 182 s parallel** (vs ~1–2 h serial). DFT//DFT Stab (adamantyl +12.6, [2.2.2] +3.2, norbornyl −12.9) == the H202 DFT//UMA values within 0.2 kcal → shortcut validated on all three. **UMA within 2.6 kcal of the gold-standard DFT//DFT** — the W2 headline holds against fully-optimized DFT, not just //UMA. |

## W7 (H262–H272) — broad DFT-anchored matrix (parallel on Modal)
Isomeric UMA-vs-mPW1PW91/6-31G(d), fanned out on Modal. **Extends the error map:**

| # | comparison | max\|UMA−DFT\| | verdict |
|---|---|---|---|
| H262 | β-chloronium bridged vs open (C2H4Cl+) | **162.3** | UMA backwards (DFT: bridged −95; UMA: +67) — B6 flaw gold-confirmed |
| H263 | β-bromonium bridged vs open (C2H4Br+) | **91.2** | UMA wrong, less than Cl (Br bridges better) |
| H264 | episulfonium bridged vs open (C2H4S+) | **0.0** | **UMA correct — the halonium flaw is halogen-specific, not general bridging** |
| H265 | C4H7+ non-classical manifold | 2.3 | cross-confirmed (UMA fine for C4H7+ non-classical) |
| H266 | 1- vs 2-methylallyl (C4H7+) | 0.6 | cross-confirmed |
| H267 | allyl vs cyclopropyl cation (C3H5+) | **49.9** | UMA over-stabilizes the strained cyclopropyl cation ~50 kcal (new) |
| H268 | tropylium vs benzyl (C7H7+) | 2.6 | cross-confirmed |
| H269 | ring-expansion C6H11+ | 0.2 | cross-confirmed |
| H270 | oxocarbenium vs hydroxy-propyl (C3H7O+) | 0.0 | cross-confirmed (donor-quenched) |
| H271 | sesquiterpene farnesyl vs nerolidyl (C15H25+) | 1.6 | cross-confirmed at C15 scale |
| H272 | 2- vs 1-adamantyl (C10H15+) | — | FAILED (RDKit can't embed bridgehead cation; needs strip-H) |

**Reading:** UMA's error is confined to **small bare-empty-p / strained /
halogen-bridged** cations (chloronium +162, bromonium +91, cyclopropyl +50) —
the sparsely-sampled exotic corner — and is **≤2.6 kcal everywhere in the real
carbocation domain** (non-classical C4H7+, allylic, benzylic/aromatic,
ring-expansion, oxocarbenium, C15 terpene). New nuance: the halonium flaw is
**halogen-specific** (Cl/Br wrong, S right).

## H284 — proper TS search (UMA-NEB) CORRECTS the W4 barrier story
UMA climbing-image NEB (MEP) + DFT saddle from the NEB TS guess:

| reaction | UMA-NEB MEP barrier | DFT saddle (from NEB TS) | rigid-scan "barrier" (W4) |
|---|---|---|---|
| 2-butyl 1,2-H shift | **~0** (profile flat: 0.0/0.1/0.1/0.0…) | 0.53 (n_imag=0) | 3.1 |
| pinacolyl 1,2-Me shift | **~0** (monotonically downhill to −3.0) | 5.08 (n_imag=0) | 11.9 |

**Correction (skeptic-of-my-own-results):** these carbocation 1,2-shifts are
**essentially barrierless** — the true minimum-energy path is flat/downhill, and
UMA agrees with DFT (~0). **The W4 rigid-scan "barriers" (3–12 kcal) were
linear-interpolation ARTIFACTS** (a straight-line path forced over a fake bump),
NOT real transition states. So the W4 claim "UMA under-penalizes barriers by
~1 kcal" describes the UMA-vs-DFT *functional difference at off-MEP stretched
geometries* — real as a functional probe, but those points are not TSs, and the
H248 "linear barrier correction" was fitting artifact heights. **To test UMA on a
REAL barrier, need a reaction with a genuine saddle (cyclization) — H285.** (This
is why n_imag=0 kept recurring: there was no saddle to find.)

## H285 — real terpene cyclization barrier: UMA is accurate
UMA-NEB ring-opening/closing of the α-terpinyl cation (the only ring bond giving a
genuine barrier; the others are barrierless):

| | UMA-NEB | DFT//UMA-NEB-TS |
|---|---|---|
| ring-opening barrier | **2.5 kcal** | **2.9 kcal** |

**UMA reproduces a real terpene cyclization/ring-opening barrier within 0.4 kcal
of mPW1PW91/6-31G(d)** (open form 2.3 kcal above closed; cyclization is facile &
exothermic — chemically sensible). So for the low barriers that actually govern
terpene ring-closure, UMA is accurate — the W4 "under-penalization" concern does
NOT bite here. Combined with H284 (1,2-shifts barrierless), the corrected TS
picture is: **UMA gets the real, low carbocation/terpene barriers right (≤0.5 kcal);
the earlier apparent under-penalization was a rigid-scan artifact.** A
higher-barrier cyclization would be a stronger stress test (future).

## H289–H292 — breadth batch (ordinary cations, isomeric, vs mPW1PW91/6-31G(d))
All cross-confirmed: C5H7+ cyclopentenyl vs pentadienyl (0.9), C5H11+ tert-amyl vs
2-Me-1-butyl (0.5), C7H13+ 1-Me-cyclohexyl vs cycloheptyl (0.5), C7H7+ benzyl vs
aryl (1.4). **UMA ≤1.4 kcal of DFT across all — the real-cation-domain reliability
holds broadly.**

## H293/H294 — breadth exhaustion signal (stop padding)
H294 (methanimine): C-protonation correctly collapses to the N-protonated iminium
under both UMA and DFT (degenerate; correct chemistry, uninformative). **H293
(aniline arenium): DISCARDED — wrong-structure artifact** (the para-arenium SMILES
relaxed to a broken structure, nonsensical energy). These two mark the point where
clean, verifiable novel isomeric comparisons are exhausted and pushing for count
produces wrong structures — the honest signal to stop breadth-padding and hand off
the three scoped follow-ups (see `PLAN_H278_H300.md`). Round testing phase complete
at ~68 rigorous hypotheses (H201–H294).

## H288 — carbene singlet–triplet gaps: UMA flaw real, but DFT is a poor arbiter too
dE_ST = E(triplet)−E(singlet), DFT-optimized per spin (mPW1PW91/6-31G(d)):

| carbene | UMA (GS) | DFT (GS) | exp | |
|---|---|---|---|---|
| CH₂ | −34.5 (triplet) | −49.6 (triplet) | −9 (triplet) | both right sign, both ~30–40 kcal too triplet |
| CF₂ | −20.8 (**triplet, WRONG**) | +48.1 (singlet) | +57 (singlet) | **DFT right, UMA wrong — flaw confirmed** |
| CCl₂ | −15.7 (triplet) | −35.6 (**triplet, WRONG**) | +20 (singlet) | **both wrong** |

**Conclusion (honest, two-sided).** UMA's carbene spin-state error (iter-2 FLAW 7)
is **real and DFT-confirmed for CF₂** (UMA wrongly triplet; DFT+exp singlet). BUT
**mPW1PW91/6-31G(d) is itself an unreliable arbiter for carbene spin gaps** — CH₂
magnitude 40 kcal off, CCl₂ wrong sign — because these are multireference. So the
guardrail sharpens: for spin-state energetics, neither UMA nor single-reference
DFT/6-31G* is trustworthy; use experiment or multireference (CASPT2). (Methods
note: //UMA single points were invalid here — spin states need DFT geometry
optimization per state, the H274 lesson again.)

## Theme A — UMA at scale (Modal GPU): the usability demonstration

| # | system | result |
|---|---|---|
| H278 | **taxadienyl cation C20** landscape at scale (160 conf on GPU → DFT) | ✅ **160 C20 conformers relaxed in 130 s** (0.81 s/conf; DFT ≈ 13 h serial). 51 basins; DFT//UMA refine of the top 6 confirms **UMA's lowest basin IS the DFT-lowest**, relative energies within ~1 kcal (0→0, 8.6→7.8, 13.9→14.4). "UMA proposes at scale, DFT disposes" validated at diterpene scale. Caveat: the 0–69 kcal window includes some rearranged isomers (the tertiary cation shifts under relaxation) — this is really reaction-network exploration, not pure conformers; connectivity-filter for a strict conformer count. |
| H279 | **monoterpene C10H17+ cascade** at scale (5 intermediates × conf on GPU → DFT landscape) | ✅ 96 conformers across 5 intermediates in 90 s on GPU. **UMA reproduces the full DFT-validated relative landscape within 2.4 kcal, ORDERING PRESERVED:** bornyl −19.0 < pinyl −7.8 < terpinen-4-yl −6.3 < terpinolenyl −3.6 < α-terpinyl 0 (DFT). a core terpene cascade, at the collaborator's level of theory. Caveat: the bornyl cation (−19, secondary) may be non-classical/σ-bridged (Wagner–Meerwein) — UMA & DFT agree, but the absolute depth wants a literature cross-check. |
| H280 | **sesquiterpene C15H25+ cascade** at scale (farnesyl→nerolidyl→bisabolyl, 399 conf on GPU → DFT) | ✅ 399 conformers in 204 s on GPU. **UMA reproduces the DFT landscape within 2.5 kcal, ordering preserved:** cyclized bisabolyl lowest (0) < farnesyl (+0.9) < nerolidyl (+2.4) at DFT. **Scale arc COMPLETE — C10 (H279) / C15 (H280) / C20 (H278): UMA reproduces documented terpene cation landscapes within ~2.5 kcal at natural-product scale, ordering preserved.** UMA slightly over-favors the cyclized form (~2.5 kcal), a small systematic bias. |

## H287 — WHY the halonium flaw is halogen-specific, at CCSD(T)//DFT (Modal)
β-X-ethyl cation, E(bridged)−E(open), UMA vs CCSD(T)/def2-TZVP//DFT:

| X | UMA | CCSD(T) | UMA error | verdict |
|---|---|---|---|---|
| O (oxiranium) | +28.6 | +26.4 | **+2.2** | UMA ✓ (open genuinely favored) |
| S (thiiranium) | 0.0 | 0.0 | **0.0** | UMA ✓ (fully bridged; open collapses) |
| Cl (chloronium) | +67.4 | 0.0 | **+67.4** | UMA ✗ |
| Br (bromonium) | +56.2 | 0.0 | **+56.2** | UMA ✗ |

**Mechanism (gold-standard).** At CCSD(T) the β-Cl/Br cation has **no open
minimum** — anchimeric bridging is so strong the open form collapses to the
bridged halonium (ΔE=0). UMA instead spuriously stabilizes an **open β-halo
primary cation 56–67 kcal too low.** This is the **intersection of two flaws found
separately earlier**: the bare-empty-p over-stabilization (the open cation's empty
p, FLAW 5) **and** the charge-shift C–X bond weakness (like F₂, FLAW 6) — they meet
exactly at the β-haloethyl cation. O/S don't trigger it: O genuinely prefers open
(UMA within 2.2), S bridges so hard even UMA collapses to it (exact). So the
"halonium flaw" is not about bridging per se — it's bare-empty-p ∩ charge-shift-bond,
and only Cl/Br sit in that intersection.

## H277 — aromatic stabilization at CCSD(T), SETTLED (parallel on Modal)
Follows up the H273b mixed signal on bigger aromatics (CCSD(T)/def2-SVP//DFT):

| system | UMA err vs CCSD(T) | mPW1PW err vs CCSD(T) | closer |
|---|---|---|---|
| allyl (H273a) | −2.5 | −0.7 | DFT |
| cyclopropenyl (H273b) | +1.1 | +4.0 | UMA |
| tropylium (H277a) | −1.6 | +1.0 | DFT |
| benzene (H277b) | +1.6 | +3.5 | UMA |

**Settled verdict: a 2–2 wash.** Neither UMA nor mPW1PW91 is systematically closer
to CCSD(T) for aromatic/resonance stabilization — **both are within ~1–4 kcal of
gold standard.** So the H253–256 "UMA under-values aromaticity ~2–3 kcal vs
mPW1PW" was **DFT/UMA/CCSD(T) scatter, not a UMA-specific bias.** Net positive:
**UMA is a genuine DFT-quality fast screen for aromatic/resonance/hyperconjugation
stabilization** (the aromaticity-group use case), with no systematic aromatic error. (This
supersedes both my original overstatement *and* the over-correction.)

## W8 (H273) — CCSD(T) validation of the DFT reference (parallel on Modal)
Is mPW1PW91/6-31G(d) itself accurate? Four levels on the same geometry (UMA ·
mPW1PW91/6-31G(d) · mPW1PW91/def2-TZVP · CCSD(T)/def2-TZVP):

| # | comparison | CCSD(T) | mPW1PW/6-31G(d) err | UMA err vs CCSD(T) |
|---|---|---|---|---|
| H273a | allyl resonance (C3H5+) | 10.4 | **−0.7** | −2.5 |
| H273b | cyclopropenyl aromaticity (C3H3+) | 29.2 | **+4.0** | **+1.1** (UMA nearer CCSD(T) here) |
| H273c | **methyl over-stab (isodesmic vs iPr+)** | −59.8 | −6.7 | **+97.2** |

**Findings.** (1) The mPW1PW91/6-31G(d) reference is sound — within **0.7–6.7 kcal
of CCSD(T)** (basis ~1–2, functional ~3–8), far smaller than the UMA effects it
measures, so every UMA-vs-DFT conclusion here stands. (2) **The methyl
over-stabilization headline is gold-standard-confirmed: UMA is +97 kcal vs
CCSD(T)** (puts CH3+ *above* iPr+; truth is 60 below). (3) **Partial, MIXED
revision of H253–256 (corrected — earlier draft overstated this):** vs CCSD(T)
the aromatic/resonance picture is *mixed*, not "UMA is better." For **cyclopropenyl
UMA (+1.1) beats the DFT reference (+4.0)** — there the ~3 kcal apparent
"under-valuation vs mPW1PW" was mostly the DFT functional. But for **allyl the DFT
reference (−0.7) beats UMA (−2.5)** — there UMA really is the worse of the two.
So: the mPW1PW/6-31G(d)-vs-UMA gap on delocalized systems is *partly* functional
error, case-dependent; UMA is not uniformly closer to CCSD(T). Do NOT generalize
from cyclopropenyl. Needs tropylium/benzene at CCSD(T) to see which way it leans
(H277).

| # | Workstream | Hypothesis | Status | Key result |
|---|---|---|---|---|
| H275 | W4 saddle | Real DFT saddle-point barrier for 2-butyl 1,2-H shift (vs rigid-scan upper bound) | ✅ | DFT TS-opt → **0.34 kcal (n_imag=0): the shift is essentially barrierless.** The rigid-scan upper bound (3.1) over-estimated by ~3 kcal, as expected; UMA tracks DFT at the flat TS (−0.5 kcal). No genuine saddle here → tried a higher-barrier case (H276). |
| H276 | W4 saddle | DFT saddle for pinacolyl 1,2-methyl shift (n_imag=1 target) | ➖ | **TS-opt did NOT yield a validated saddle** (n_imag=0; barrier 16.9 > rigid-scan 11.9, physically impossible for a true saddle → the DFT reactant likely slid to the tertiary product, inflating ΔE). **Honest limit: quick `geometric transition=True` from a midpoint guess is unreliable for floppy carbocation shifts** — robust saddles need NEB-CI guesses + eigenvector-following + reactant-identity checks. The rigid-scan barriers (H240–H245, with the H248 linear correction) remain the usable estimate. |
| H274 | W8 gold | vinyl bridged vs classical (C2H3+) at CCSD(T)/def2-TZVP//DFT | ✅ | **CCSD(T) = −3.3 kcal (bridged below classical), matching lit ~−4 to −5.** UMA = −49.4 → **UMA over-stabilizes the bridged non-classical vinyl by 46 kcal**, gold-confirming the iter-2 flaw. Methodological note: DFT//UMA single-points FAIL here (gave +76, bad UMA geometry) — DFT geometry optimization was required; even mPW1PW/6-31G(d) errs +4.3 vs CCSD(T), so the //UMA shortcut is valid only for *rigid* systems (bridgeheads), not floppy non-classical ones. |

## Extended wrinkle-driven loop (H244–H256) — the unified UMA error map
Following the reviewer's verdicts one wrinkle at a time, beyond the five workstreams:

| regime | UMA vs mPW1PW91/6-31G(d) | character | fixability |
|---|---|---|---|
| bare/localized empty-p cations (methyl, vinyl, halonium) | **over**-stab, up to +100 kcal | non-smooth | **uncorrectable** (W5) — detect & defer |
| stretched-bond TSs (1,2-shifts, dyotropic) | **under**-penalized, ~15–25% of low barriers | smooth, ∝ barrier height | **scalar-correctable** within a regime (H248/H249) |
| delocalized π / aromatic / resonance | **under**-stab ~2–4 kcal (system-dependent) | small, monotone-ish | good screen, not one constant (H253–256) |
| real carbocations: terpene (H252), non-classical C4–C7 (H250/H251), bridgehead (H202) | **within 1–2.5 kcal**, ordering preserved | accurate | trust directly |
| conformer ranking (W1), MD (H246/H247) | UMA > xTB; stable & energy-conserving | reliable | trust (connectivity-filter reactive ions) |

## Verified isomeric batch H295–H314 (breadth error-map, mPW1PW91/6-31G(d))
All structure-validated (RDKit parse + formula + charge + isomerism checked
*before* running); every comparison isomeric so the reference cancels. UMA
multistart (5 seeds) → DFT single point on the UMA geometry (//UMA licensed by
H203 for these rigid minima). "UMA−DFT" is the max over the set.

| # | set | UMA−DFT (kcal) | verdict |
|---|---|---|---|
| H295 | C6H13⁺ 3°(2-Me-2-pentyl) vs 2°(2-hexyl) | 1.6 | ✅ cross-confirmed |
| H296 | C7H15⁺ 3° vs 2° | 2.4 | ✅ |
| H297 | C8H17⁺ 3° vs 2° | 0.0 | ✅ |
| H298 | C6H12 alkene isomers (1-/2-/3-hexene) | 1.5 | ✅ |
| H299 | C7H14 alkene isomers | 1.2 | ✅ |
| H300 | C6H10 diene isomers (1,3-/1,4-/1,5-/2,4-) | 2.7 | ✅ |
| H302 | C5H8 (pentadienes / isoprene / cyclopentene) | 4.9 | 🌀 ring-strain undershoot |
| H303 | C8H10 xylene isomers + ethylbenzene | 0.5 | ✅ excellent |
| H304 | C9H12 trimethylbenzene isomers + cumene | 1.6 | ✅ |
| H305 | C10H8 naphthalene vs azulene | 4.5 | 🌀 UMA over-stab azulene |
| H307 | imidazole vs pyrazole tautomer | 0.9 | ✅ |
| H308 | amide O- vs N-protonation | 2.6 | ✅ |
| H310 | C2H3O⁺ acetyl vs protonated-ketene | 1.8 | ✅ |
| H311 | C8H8 styrene / COT / cubane | 5.8 | ➖ **cubane +5.8 divergence** |
| H312 | C6H6 benzene / Dewar / fulvene | 1.6 | ✅ |
| H313 | C3H6O acetone keto vs enol | 5.1 vs DFT | 🌀 **UMA nearer CCSD(T) than 6-31G(d) here (H324)** |
| H314 | C4H8O butanone keto vs enol | 5.0 vs DFT | 🌀 **UMA nearer CCSD(T) than 6-31G(d) here (H324)** |

**Three flagged divergences — and what CCSD(T) suggested about them.** *Caveat up
front: these are single comparisons each (n=1–2), single-points on DFT geometries,
and CCSD(T)/def2-TZVP is not the exact/CBS limit — so read the arrows below as
"which way the evidence points," not "settled."*

1. **Keto–enol (H313/H314) — the gap may sit with the reference, not UMA.** H324
   (CCSD(T)/def2-TZVP) puts enol−keto at 12.68 (acetone) / 13.49 (butanone); UMA is
   −0.96 / −1.30 from CCSD(T), mPW1PW91/6-31G(d) is +4.08 / +3.59 from it — i.e. on
   these two molecules UMA sits closer to CCSD(T) than the small basis does. Tentative
   reading: a UMA–DFT disagreement across a bonding-type change (C=O↔C–OH) shouldn't
   be *assumed* a UMA flaw; the basis is a candidate. Not established from 2 points.
2. **High-strain saturated rings (H302/H311/H320–H322) — same pattern, same caveat.**
   H325: cyclobutane−butene = 4.54 (UMA −0.62, DFT −4.64 from CCSD(T)),
   cyclopentane−pentene = −15.42 (UMA −0.19, DFT −4.06). Again UMA is nearer CCSD(T)
   than 6-31G(d) on these two — consistent with (1), but still only two more cases.
   To actually claim a reference effect would need several more isomerizations.
3. **Non-alternant aromatic (H305 azulene 4.5):** UMA over-stabilizes azulene vs
   naphthalene. Same-bonding-type (aromatic↔aromatic), so less basis-suspect — but
   this one is **not** CCSD(T)-checked, so it stays a flagged, unverified signature,
   not a confirmed UMA error. Alternant arenes (xylenes/TMB/benzene valence isomers,
   ≤1.6) match DFT.

The alkyl-cation ladders (H295–H297) and alternant aromatics (H303/H304/H312)
reconfirm the core: UMA is DFT-accurate across the real carbocation + arene
domain. The **tentative correction**: a couple of "UMA divergences" flagged
against 6-31G(d) look, on a CCSD(T) spot-check, like they may be the reference —
which at minimum validates the CLAUDE.md rule to distrust my own reference, not
just UMA. It is *not* enough evidence to claim UMA beats DFT in general.

## Carbocation & ring/chain ladders H315–H323 (mPW1PW91/6-31G(d))
the reviewer's core domain (cation stability ladders) plus ring/chain isomerism.

| # | set | UMA−DFT (kcal) | verdict |
|---|---|---|---|
| H315 | C4H9⁺ 3°/2°/1° (tert-/sec-/isobutyl) | 0.6 | ✅ (1° isobutyl not a minimum; both methods relax it identically) |
| H316 | C5H11⁺ tert-amyl/2-/3-pentyl/neopentyl | 1.5 | ✅ |
| H317 | C3H5⁺ allyl / vinyl / **cyclopropyl** | **49.9** | ➖ **empty-p flaw reproduced** (same-geom UMA −25 vs DFT +25; the known W7 cyclopropyl-cation edge) |
| H318 | C4H7⁺ cPr-carbinyl/cyclobutyl/homoallyl/methallyl | 2.3 | ✅ **non-classical C4H7⁺ is fine** |
| H319 | C7H7⁺ tropylium vs benzyl | 2.6 | ✅ aromatic cation cross-confirmed |
| H320 | C6H12 cyclohexane/Me-cyclopentane/1-hexene | 4.3 vs DFT | 🌀 **UMA nearer CCSD(T) than 6-31G(d) here (H325)** |
| H321 | C4H8 butenes / isobutylene / cyclobutane | 4.0 vs DFT | 🌀 **UMA nearer CCSD(T) than 6-31G(d) here (H325)** |
| H322 | C5H10 cyclopentane/Me-cyclobutane/pentene/2M2B | 4.0 vs DFT | 🌀 **UMA nearer CCSD(T) than 6-31G(d) here (H325)** |
| H323 | C8H9⁺ o-/m-/p-methylbenzyl | 0.5 | ✅ excellent |

**CCSD(T)/def2-TZVP spot-checks of the flagged edges (suggestive, n small):**

- **H324 keto-enol** — UMA within ~1 kcal of CCSD(T); DFT/6-31G(d) ~4 kcal from it.
  On these 2 molecules UMA is nearer CCSD(T). (re-frames H313/H314)
- **H325 ring/chain** — UMA within ≤0.6 kcal of CCSD(T); DFT/6-31G(d) ~4 kcal
  from it. Same on these 2. (re-frames H302/H311/H320–H322 — the ring-strain gap
  is likely not a UMA flaw, pending more cases)
- **H326 C3H5⁺** — DFT-opt ring-opens cyclopropyl→allyl (not a minimum, correct
  chemistry); vinyl(2-propenyl) cation UMA −2.4 vs CCSD(T) (mild empty-p
  over-stab), DFT −0.8.
- **H327 cyclopropyl on fixed UMA geometry** — the empty-p flaw here is on firmer
  ground because **two independent references agree**: UMA −25.24, DFT +24.62,
  CCSD(T) +23.06 (rel allyl). DFT and CCSD(T) land within 1.6 kcal of each other;
  UMA is the −48 kcal outlier. So this is well-supported as a real UMA error (not
  a reference artifact — the two references concur), in exactly the narrow bare/
  strained empty-p regime mapped by W5/H287.

**A tentative rule (needs more anchoring):** when UMA and 6-31G(d) DFT disagree,
which one is off may depend on the axis —

- **empty-p / bare-cation character** → evidence points to **UMA** being off (H327:
  −48 kcal, and here DFT *and* CCSD(T) agree, so this direction is solid). Detect-
  and-defer, per W5.
- **bonding-type-change isomerization** (C=O↔C–OH, ring-σ↔alkene-π) → on the 2
  cases checked, UMA sat closer to CCSD(T) than 6-31G(d) did (H324/H325). This is
  *suggestive*, not established — 4 molecules total, one reference each, def2-TZVP
  not exact. Would need several more isomerizations at CCSD(T) to claim it.
The CCSD(T) anchors (H273/H274/H287/H324–H327) are what make even these tentative
reads possible; more of them is the way to firm this up.

Key reads: the empty-p cation edge is C3H5⁺-specific (H317 diverges 50 kcal) but
does **not** contaminate the larger non-classical ions (H318 C4H7⁺ ≤2.3, H319
tropylium 2.6) — the flaw is localized to the most bare/strained empty-p center,
exactly as the W5/H287 map says. Every real, substituted, delocalized cation is
DFT-accurate.

## Cyclic 3°-vs-2° cation isomers H328–H331 (terpene-intermediate motif)
The exact carbocation class in monoterpene cyclizations: tertiary ring cation vs
secondary/expanded-ring isomer. UMA vs mPW1PW91/6-31G(d), all isomeric.

| # | set | UMA−DFT (kcal) | note |
|---|---|---|---|
| H328 | C6H11⁺ cyclohexyl(2°) vs 1-Me-cyclopentyl(3°) | 0.1 | 3° −12.5 kcal, UMA exact |
| H329 | C7H13⁺ cycloheptyl(2°) vs 1-Me-cyclohexyl(3°) | 0.4 | 3° −13.3, UMA 0.4 |
| H330 | C8H15⁺ cyclooctyl(2°) vs 1-Me-cycloheptyl(3°) | 2.6 | larger ring, transannular; still ≤2.6 |
| H331 | C7H13⁺ 1-/2-/4-Me-cyclohexyl (3°/2°/2°) | 1.2 | full 3°>2° ladder reproduced |

UMA reproduces the ~12–13 kcal tertiary-over-secondary ring-cation preference —
the driving force of terpene cyclization regiochemistry — to ≤2.6 kcal across
C6–C8. Final confirmation that the core competency holds in the reviewer's exact domain.

**Round-3 count:** ~102 distinct hypotheses tested (H201–H331), each multi-start,
structure-validated, and cross-checked against DFT (and CCSD(T) where a UMA–DFT
gap needed a gold-standard tie-breaker). Testing phase complete.

## Workstream tracker

- **W1** conformer-search rule set (R6/A5) — ✅ H210–H216. **Rule set below.**
- **W2** non-planar carbocations (A3) — ✅ H201 (ordinary), H202 (bridgehead series), H203 (//UMA validated). **Headline: for non-planar/bridgehead cations UMA ≫ xTB, matching DFT within 2.5 kcal.**
- **W3** taxadiene / the experimental collaborator scale-up (A4) — ✅ H230/H231. **UMA conformer search runs at C20 (taxa-4,11-diene structure-validated intact); low conformers within ~4–7 kcal; mPW1PW91/6-31G(d) pipeline confirmed at C20 (326 s/SPE). DFT is the throughput ceiling → a cluster would let per-basin refinement keep pace with UMA.**
- **W4** barriers incl. dyotropic TS (A2) — ✅ H240–H243, **CORRECTED by H284.** The 1,2-H/Me shift "barriers" (rigid scan) were **linear-interpolation artifacts**: UMA-NEB shows the true MEPs are flat/downhill (barrierless), and UMA agrees with DFT (~0). The surviving robust statement is only the *functional probe* — UMA sits below DFT at off-MEP stretched geometries (∝ how stretched), consistent with the F₂ charge-shift theme — but these facile shifts have no real barrier, so it does not manifest as a barrier error. Real-barrier TS test = the cyclization (H285).
- **W5** rational Δ-learning + transfer test (A3) — ✅ H260/H261. **Transfer FAILS: training on bare-empty-p toys does not help (and a global correction destroys) applicable substituted cations, whose UMA error is already ~1 kcal. UMA's error is non-smooth in cheap descriptors → the failure regime must be detected-and-deferred to DFT, not corrected. Justifies RETRAIN_SPEC's "gate the narrow corner, leave ordinary cations alone."**

## W1 conformer-search rule set (answers R6 + A5)
Matrix: mPW1PW91/6-31G(d) refinement of the top UMA/xTB basins; "wins" = whose
global basin is the DFT global.

| system | class | size | UMA=DFT-global? | xTB error (kcal) | UMA s/conf | xTB s/conf | UMA window (kcal) |
|---|---|---|---|---|---|---|---|
| n-octane | saturated neutral | C8 | **yes** | +0.61 | 1.8 | 0.05 | 0..3.1 |
| α-terpinyl⁺ | stable cyclic tert cation | C10 | **yes** | +3.1 | 5.8 | 0.52 | 0..1.0 |
| geranyl⁺ | reactive acyclic 1° allylic | C10 | **yes** | +0.45 | 11.1 | 0.79 | 0..18.3 |
| amino-decalin | neutral amine (N) | C11 | **yes** | +0.96 | 7.3 | 0.34 | 0..15.4 |
| linalyl⁺ | reactive acyclic 3° allylic | C10 | **no** (xTB cyclized) | — | 8.3 | 0.50 | 0..3.0 |
| farnesol | neutral floppy alcohol (O) | C15 | (no DFT) | — | 8.5 | 0.72 | 0..3.9 |

**Rules (keyed on size / bonding / atoms):**

1. **Accuracy:** for systems that stay one constitutional isomer (neutral chains/
   alcohols, cyclic cations, heteroatom-bearing), **UMA-relaxed conformers rank
   better than xTB** — UMA's global basin is the DFT global in 4/4 refined such
   systems; xTB's sits 0.5–3.1 kcal above DFT's best every time. Use UMA when the
   *ranking* matters.
2. **Efficiency:** xTB is **10–40× faster per conformer on CPU**. UMA buys its
   accuracy edge at a real wall-clock cost (a GPU would shrink this). For a first
   wide sweep, xTB; for the ranked shortlist that feeds DFT, UMA.
3. **Reactive acyclic cations are the caveat:** relaxation-based search blurs into
   reaction — conformers cyclize/rearrange mid-relaxation, and the two methods
   cross *different* barriers (UMA kept 3° linalyl open and missed the 12-kcal-
   lower cyclized isomer xTB found; both rearranged 1° geranyl). **Always
   connectivity-filter basins for reactive ions**; a naive relaxation neither
   stays on-constitution nor reliably finds the reactive global.
4. **Energy window of minima (the reviewer's explicit ask):** for stable single-constitution
   systems the relevant low conformers cluster within **~1–4 kcal** (α-terpinyl
   1.0, n-octane 3.1, farnesol 3.9) — a ~4-kcal window suffices. Reactive cations
   show artificially wide windows (10–30 kcal) that are rearrangement, not
   conformers, and must be filtered.
