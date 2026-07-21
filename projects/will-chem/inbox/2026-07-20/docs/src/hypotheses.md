# Hypothesis ledger

A wrinkle-driven, DFT-free investigation using UMA (`uma-s-1p1`) as the
exploration engine. Chemistry framed around the carbocation / terpene (Wagner–Meerwein rearrangement) × **the aromaticity group** (aromaticity,
hyperconjugation, σ-delocalization, stabilization of reactive intermediates)
intersection. Each hypothesis is developed one at a time, tested with UMA, and
the *wrinkle* it exposes seeds the next one. Narrative + feelings live in
`DIARY.md`; runnable experiments in `uma_core/mechanisms.py`.

Ground rules: no DFT (no compute yet — see `DFT_REQUESTS.md`); energies use
isodesmic/isomeric comparisons so absolute references cancel; UMA validated as
an instrument in `stress_test.py` (6/6).

Status: ✅ confirmed · ❌ refuted · 🌀 mixed/nuanced · ⏳ in progress · 💤 backlog

| # | Hypothesis (chemistry) | Status | Key UMA result |
|---|---|---|---|
| H1 | UMA's "primary" C4H9⁺ minima actually rearranged during relaxation | ✅ | isobutyl & n-butyl → 2-butyl (secondary) |
| H2 | The C4H9⁺ surface has only two real basins (tert + sec) | ✅ | pooled surface: 2 basins + funnels |
| H3 | UMA gives *classical* 2-norbornyl, missing the σ-bridge | ❌ | symmetric bridge 2.04/2.05 Å (non-classical) |
| H4 | Hyperconjugation leaves a geometric fingerprint UMA resolves | ✅ | aligned β C–H +18 mÅ; Cα–Cβ −66 mÅ |
| H5 | β-Si is a far stronger σ-donor than β-H (β-silicon effect) | ✅ | C–Si +403 mÅ, Si-**bridged**; +30.7 kcal/mol |
| H6 | The parent ethyl cation is H-bridged (non-classical) | ❌→H9 | single-start gave classical (C–C 1.456) — artifact |
| H7 | Neopentyl cation C–C-bridges / methyl-shifts to tert-amyl | ✅ | barrierless shift; relaxed cation is tertiary |
| H8 | Cyclopropylcarbinyl shows ring-σ participation | ✅ | ring C–C blown to 1.63/1.67 Å; carbinyl 1.35 Å |
| H9 | H6's classical was a start artifact; bridged is the true min | ✅ | from bridged seed: kept bridge, −3.08 kcal/mol |
| H10 | Cyclopropylcarbinyl goes to bicyclobutonium (transannular) | 🌀→H11 | bisected only (2.52 Å); needs multi-start |
| H11 | Multi-start maps the C4H7⁺ manifold | ✅ | allyl (from homoallyl) lowest; c-propylcarbinyl +6.3, cyclobutyl +5.2 |
| H12 | Cyclopropenyl cation is aromatic (equalized ring) | ✅ | ring bonds 1.358 Å (1 mÅ spread) |
| H13 | Aromatic ≪ open isomer (C3H3⁺) | ✅ | cyclopropenyl 30.3 kcal/mol below propargyl |
| H14 | UMA captures antiaromatic destabilization (C5H5⁺) | ❌🚩 | symmetric pentagon; triplet −200 kcal/mol (anomaly) |
| H15 | H14's symmetric singlet is a real minimum, not a saddle | ✅🚩 | symmetry-breaking returns to symmetric (flaw confirmed) |
| H16 | Spin channel is trustworthy (calibration) | ✅ | O2 +26.2, CH2 +18.5 kcal/mol → sane; H15 anomaly is real |
| H17 | Markovnikov: tertiary ≫ secondary on protonation | ✅ | tert-amyl 10.1 kcal/mol below 3-methyl-2-butyl (C5H11⁺) |
| H18 | Monoterpene cyclization (geranyl→α-terpinyl) is downhill | ✅ | open-chain 13.3 kcal/mol above cyclic tertiary (C10H17⁺) |
| H19 | α-Oxygen lone pair strongly stabilizes (oxocarbenium) | ✅ | C–O −174 mÅ (C=O⁺); +33.4 kcal/mol vs α-C |
| H20 | α-Fluorine net stabilizes (π-donation > induction) | ✅ | C–F −119 mÅ (fluoronium); +17.2 kcal/mol vs α-H |
| H21 | β-Haloethyl cations bridge from RDKit starts | ❌→H23 | relax to open/near-dissociated, not bridged (start artifact) |
| H22 | Tropylium (6π aromatic) below benzyl (C7H7⁺) | ✅ | ring spread 0 mÅ; benzyl +7.2 kcal/mol |
| H23 | Bridged halonium are UMA minima | 🌀 | symmetric F/Cl/Br minima exist (Br OK) but +49–67 kcal/mol ABOVE open — ordering uncertain (→DFT) |
| H24 | UMA bond-dissociation asymptote is physical | ✅ | ethane C–C: monotonic to ~98 kcal/mol plateau |
| H25 | Allyl rotation barrier ≈ allylic resonance energy | ✅ | vertical barrier 38.2 kcal/mol (exp ≈ 35–38), sin²θ shape |
| H26 | Cp anion (aromatic 6π) is symmetric; sharpens H14 | ✅ | anion 1.408 Å equalized → UMA symmetrizes 5-ring regardless of count |
| H27 | Cyclobutadiene (neutral antiaromatic) is rectangular | ✅ | 1.328/1.569 Å (242 mÅ) → distortion captured; narrows H14 flaw |
| H28 | Bridgehead (1-norbornyl) cation is Bredt-destabilized | ❌🚩 | UMA puts it 3.1 kcal/mol BELOW 2-norbornyl (cage intact) — flaw |
| H29 | Cyclopropyl cation ring-opens to allyl (electrocyclic) | ✅ | relaxes to open allyl (2 C–C among 3 C) |
| H30 | Cyclopropenyl anion (4π) is antiaromatic-destabilized | ✅ | cyclic +53.2 kcal/mol above open → flaw is Cp⁺-specific, not general |
| H31 | Cyclobutylcarbinyl ring-expands to cyclopentyl | ✅ | relaxes to cyclopentyl (degree seq [2,2,2,2,2]) |
| H32 | UMA captures cation–π (NH4⁺···benzene) | ✅ | binding −19.5 kcal/mol at 2.92 Å (exp ≈ −15 to −20) |
| H33 | Allyl radical (doublet) delocalized, weaker resonance | ✅ | C–C equal (0 mÅ); barrier 21 vs cation's 38 → doublets fine |
| H34 | 7-norbornenyl cation shows π-participation | ✅🌀 | C7 bonds alkene at 1.386 Å (cyclizes); asymmetric, not symmetric bridge |
| H35 | Acylium (CH3C≡O⁺): O stabilizes via C≡O triple bond | ✅ | C–O 1.109 Å (−93 mÅ), C–C–O 179.9° (linear) |
| H36 | Iminium (CH2=NH2⁺): N π-donor, C=N⁺ double bond | ✅ | C–N 1.270 Å (−191 mÅ vs amine) |
| H37 | Vinyl cation is non-classical (H-bridged) | 🌀🚩 | bridged below classical but by 52.8 kcal/mol (exp ~4) — over-stabilized (cf H23) |
| H38 | Benzenium (arenium/Wheland) is a delocalized σ-complex | ✅ | 1 sp³ CH₂ + pentadienyl (bonds 1.36–1.41, 44 mÅ) |
| H39 | UMA resolves sub-kcal conformational energy (butane) | ✅ | gauche−anti = +0.61 kcal/mol (exp 0.6–0.9) |
| H40 | Gauche effect (1,2-difluoroethane): gauche below anti | ✅ | anti−gauche = +1.11 kcal/mol (counterintuitive sign right) |
| H41 | 2-butene E/Z energetics (steric baseline) | ✅ | cis−trans = +1.12 kcal/mol (exp ~1.0) |
| H42 | cis effect (1,2-difluoroethene): cis below trans | ✅ | trans−cis = +0.96 kcal/mol (counterintuitive sign right) |
| H43 | Carbocation stability ladder (hydride affinity) | 🌀🚩 | 3°>benzyl>2°>allyl>1° CORRECT; but CH3⁺ over-stabilized (ranks above tBu) — flaw |
| H44 | Basicity/proton-affinity ladder (gas phase) | ✅ | NMe3>MeNH2>NH3>Me2O>MeOH>H2O; N>O, gas-phase amine trend right |
| H45 | Cyclobutadiene dication (2π) is aromatic square | 🌀 | distorted (1.29/1.29/1.40 + one >1.75), not clean square — exotic dication, fits small-cation trouble |
| H46 | Cyclooctatetraene tub-puckers (avoids 8π antiaromaticity) | ✅ | max out-of-plane 0.39 Å (non-planar tub) |
| H47 | Pinacolyl 2° cation methyl-shifts to 3° (barrierless WM) | ✅ | 3,3-diMe-2-butyl relaxes straight to 2,3-diMe-2-butyl (tertiary) |
| H48 | Styrene protonation: benzylic (Markovnikov) is the sink | ✅ | primary 2-phenylethyl rearranges to benzylic 1-phenylethyl (identical structure) |
| H49 | Gas-phase acidity ladder | ✅ | AcOH>PhOH>EtOH>H2O>CH4 (incl. EtOH>H2O gas-phase reversal) |
| H50 | Inductive effect on acidity (fluoroacetic series) | ✅ | monotonic; AcOH→CF3COOH = 24.2 kcal/mol (exp ~23) |
| H51 | Radical stability ladder (H-atom transfer) | ✅ | allyl>benzyl>3°>2°>1°>methyl — CORRECT incl. methyl least (unlike CH3⁺!) |
| H52 | Water-dimer hydrogen bond | ✅ | −4.18 kcal/mol, O···O 2.97 Å (exp −5.0, 2.98) |
| H53 | Keto–enol tautomerism (acetaldehyde/vinyl alcohol) | ✅ | enol +9.9 kcal/mol above keto (exp ~11) |
| H54 | Ozone (multireference) geometry + spin | ✅ | symmetric, 1.24 Å, 117.7°, triplet +36 (singlet GS) — handled |
| H55 | F2 dissociation (charge-shift bond) | ❌🚩 | non-monotonic curve, plateau ~149 vs BDE 38 — grossly wrong |
| H56 | Cyclopropane ring strain (homodesmotic) | ✅ | 29.5 kcal/mol (exp ~27) |
| H57 | Diels–Alder reaction energy | ✅ | butadiene+ethylene→cyclohexene = −47 kcal/mol (exp ~−38) |
| H58 | Naphthalene vs azulene (C10H8) | ✅ | azulene +39.7 kcal/mol above naphthalene (exp 30–38) |
| H59 | Benzene vs benzvalene (C6H6, aromaticity) | ✅ | benzvalene +73.0 kcal/mol above benzene (exp ~74) |
| H60 | Monoterpene C10H16 isomer stability ranking | ✅ | camphene<terpinenes~limonene<pinenes<myrcene; matches known ΔHf |
| H61 | Diene conjugation energy (1,3 vs 1,4-pentadiene) | ✅ | conjugated 6.3 kcal/mol lower (exp ~7) |
| H62 | Zaitsev: more-substituted alkene more stable | ✅ | trisub 3.6 kcal/mol below monosub (C5H10) |
| H63 | Hammett substituent effects on benzyl cation | ✅ | p-OMe +17.6 (stabilizes), p-CF3 −10.5 (destabilizes) |
| H64 | Pyridine more basic than ammonia (gas phase) | ✅ | −18.9 kcal/mol (exp PA diff ~18) |
| H65 | Benzene dimer π-stacking (dispersion) | ✅ | −3.07 kcal/mol (exp −1.6 to −2.8; dispersion present) |
| H66 | Bifluoride [F–H–F]⁻ symmetric strong H-bond | ✅ | F–H 1.148/1.148 (centered), F···F 2.30 Å (exp 2.28) |
| H67 | o-Benzyne strained triple bond (multireference) | ✅ | shortest C–C 1.236 Å (exp ~1.24–1.26) |
| H68 | Allene perpendicular CH2 groups (D2d cumulene) | ✅ | CH2-plane twist 89.9° |
| H69 | Ring strain trend C3–C6 (homodesmotic) | ✅ | 29.5/27.9/8.3/2.0 kcal/mol (exp 27/26/6/0) |
| H70 | Endocyclic > exocyclic alkene stability | ✅ | endocyclic lower by 1.9 kcal/mol (exp ~2) |
| H71 | s-trans > s-cis butadiene | ✅ | s-trans lower by 2.7 kcal/mol (exp ~2.9) |
| H72 | RAHB flips acetylacetone to enol (vs H53 keto) | ✅ | enol −4.2 kcal/mol below keto (multistart; chelated OH···O 1.60 Å) |
| H73 | Amide resonance — formamide C–N rotation barrier | ✅ | 24.7 kcal/mol vertical (exp relaxed ~16–19) |
| H74 | 2-fluoroethanol intramolecular OH···F H-bond | ✅ | gauche 2.5 kcal/mol below anti (exp 2–4) |
| H75 | Guanidinium Y-delocalized super-base | ✅ | 3 C–N equal (1.329 Å); basicity −33.2 vs NH3 (exp PA gap ~31) |
| H76 | SF6 octahedral hypervalent sulfur | ✅ | S–F 1.569 Å (exp 1.56), all 12 angles ~90° |
| H77 | Dichlorocarbene singlet ground state | ❌🚩 | UMA gives triplet 15.7 kcal/mol BELOW singlet (should be singlet GS) |
| H78 | Water vibrational frequencies (Hessian) | ✅ | 1623/3824/3921 cm⁻¹ (harmonic; exp 1595/3657/3756) |
| H79 | Ammonia inversion barrier | 🌀 | vertical estimate 11.0 kcal/mol (exp 5.8) — unrelaxed planar overestimates |
| H80 | Ethane rotation barrier (staggered→eclipsed) | ✅ | 2.7 kcal/mol (exp 2.9) |
| H81 | Pinyl cation ring-opens (pinene cascade) | ✅ | strained bicyclo[3.1.1] cation → monocyclic (2→1 rings) |
| H82 | Bornyl-type cation is classical (not bridged) | ✅ | C–C 1.51–1.54, no σ-bridge (methyls favor classical vs 2-norbornyl H3) |
| H83 | Pinyl vs bornyl cation (C10H17⁺ branch-point) | 🌀 | bornyl ~3 kcal/mol below ring-opened pinyl (exploratory) |
| H84 | Benzene resonance energy (hydrogenation ASE) | ✅ | 37.4 kcal/mol (exp ~36) |
| H85 | C3H4 isomer stability | 🌀 | cyclopropene +21.9 correct; propyne/allene near-tie, order off ~2 kcal/mol |
| H86 | Fulvene vs benzene (C6H6 aromaticity) | ✅ | fulvene +35.1 kcal/mol above benzene (exp 32–36) |
| H87 | p-Nitrophenol ≫ phenol acidity (NO2 effect) | ✅ | −22.5 kcal/mol more acidic |
| H88 | Pyridine ≫ pyrrole basicity | ✅ | −34.7 kcal/mol (pyrrole N lone pair is aromatic) |
| H89 | Cyclopropane vs propene (C3H6 strain) | ✅ | cyclopropane +6.0 kcal/mol above propene (exp ~7.9) |
| H90 | Allyl vs vinyl cation (C3H5⁺ resonance sink) | ✅ | allyl lower by 7.8 kcal/mol |
| H91 | Formamide O- vs N-protonation (amide O-base) | ✅ | O-protonation lower by 19.7 kcal/mol (exp 10–14) |
| H92 | Acetylene vs vinylidene (C2H2) | ✅ | acetylene 41.7 kcal/mol below vinylidene (exp ~44) |
| H93 | 1-propyl → 2-propyl hydride shift | ✅ | primary relaxes to secondary (not a minimum) |
| H94 | Allyl anion delocalization | ✅ | terminal C–C equal (1.387 Å); completes cation/radical/anion trio |
| H95 | Acetate carboxylate resonance | ✅ | C–O equal (1.2517/1.2523 Å) |
| H96 | Nitro-group resonance (nitromethane) | ✅ | N–O equal (1.2122/1.2127 Å) |
| H97 | Allyl anion rotation barrier (completes resonance trio) | ✅ | 32.8 kcal/mol → cation(38)≈anion(33)>radical(21) |
| H98 | HF vs HCl gas-phase acidity | ✅ | HCl more acidic by 38.3 kcal/mol (exp ~43) |
| H99 | Cubane strain energy (homodesmotic) | ✅ | 166 kcal/mol (exp ~166, essentially exact) |
| H100 | Neryl → α-terpinyl cyclization (capstone) | ✅ | open-chain 12.1 kcal/mol above cyclic (loop closed) |

## ⚑ UMA flaws / limitations found → see `UMA_FLAWS.md`
- **Antiaromatic distortion missed for the cyclopentadienyl cation** (H14/H15) —
  but NOT for neutral cyclobutadiene (H27), so it's the open-shell/degenerate
  cation specifically, not antiaromatics in general.
- **Grossly wrong triplet energy** for that cation (−200 kcal/mol; H15/H16).
- **Bridgehead (Bredt) cation under-destabilized** — 1-norbornyl placed below
  2-norbornyl (H28); sign qualitatively wrong.
- **Carbene/spin-state energetics unreliable** — CCl2 predicted triplet GS (should be singlet, H77); CH2 gap 2x loose (H16); Cp⁺ triplet −200 (H15).
- **F2 (charge-shift) dissociation grossly wrong** — non-monotonic curve, BDE ~149 vs 38 kcal/mol (H55); C–C homolysis is fine (H24), so it's F-specific.
- **Single-start relaxation misses non-classical minima** (ethyl cation, H6/H9;
  halonium H21/H23) — methodological trap, not a model error.

## The thread so far
H1/H2 (what minima exist) → H3 (norbornyl bridge real) → H4 (hyperconjugation is
geometric) → H5 (β-Si bridges) → H6/H9 (**even the ethyl cation is non-classical;
single-start relaxation misses it by 3 kcal/mol**) → H7 (barrierless Wagner–
Meerwein) + H8/H10 (cyclopropylcarbinyl σ-participation, bisected). **Live
wrinkle:** non-classical minima are easy to miss from one starting geometry —
multi-start is mandatory. Next (H11): multi-start the C4H7⁺ surface
(cyclopropylcarbinyl ⇌ cyclobutyl ⇌ bicyclobutonium) properly.

## Backlog / candidate directions (will be reordered by wrinkles)
- **Continuum:** ethyl cation H-bridge; β-C–C vs β-C–H vs β-C–Si bridging ladder;
  cyclopropylcarbinyl σ-participation (bicyclobutonium); phenonium (aryl) bridge.
- **Rearrangement stereoelectronics (the experimental collaborator):** antiperiplanar bond migrates;
  1,2-H vs 1,2-CH3 shift preference; endo/exo migration in norbornyl; ring-size
  selectivity; α-terpinyl → pinyl/bornyl/terpinen-4-yl partitioning energetics.
- **Aromaticity (the aromaticity group):** cyclopropenyl (2π aromatic) vs allyl; tropylium;
  antiaromatic cyclopentadienyl cation destabilization; homoaromaticity
  (homotropylium); arenium (protonated benzene); norbornadienyl (π-participation).
- **Heteroatom/substituent (the aromaticity group):** oxocarbenium & anomeric effect; acylium;
  fluorine hyperconjugation vs induction; H-bond stabilization of cations.


---

# ✅ Campaign complete — 100 hypotheses (H1–H100)

**Tally:** 85 confirmed · ~7 genuine UMA flaws · rest mixed/refuted-but-informative.
All DFT-free (isodesmic/isomeric comparisons + geometry); UMA `uma-s-1p1` on CPU.

## Where UMA is trustworthy (quantitatively, ~1–3 kcal/mol)
Hyperconjugation (β-C–H/C–Si), σ- and π-resonance (allyl/benzyl/aromatic),
aromatic stabilization (cyclopropenyl, tropylium, benzene ASE 37 kcal/mol),
lone-pair/π donation (oxocarbenium, acylium, iminium, fluoronium, guanidinium),
Wagner–Meerwein rearrangements (methyl/hydride shifts, ring expansion, pinene
ring-opening), Markovnikov & cyclization energetics, conformational &
stereoelectronic effects (butane, gauche/cis effects, anomeric-type),
acidity/basicity ladders, ring strain (incl. cubane 166 kcal/mol), cation–π,
H-bonds (water dimer, bifluoride, RAHB), dispersion, and Hessian/frequencies.

## Where UMA fails or needs a spot-check (see `UMA_FLAWS.md`)
1. **Antiaromatic cyclopentadienyl cation** — misses Jahn–Teller distortion
   (triplet-ground-state/degenerate; H14/H15). Neutral cyclobutadiene & the
   cyclopropenyl anion are fine (H27/H30) — the blind spot is narrow.
2. **Spin-state energetics** — Cp⁺ triplet −200 kcal/mol; dichlorocarbene
   predicted triplet (should be singlet); CH₂ gap 2× loose (H15/H16/H77).
3. **Bridgehead (Bredt) cation** under-destabilized (H28).
4. **Bare methyl cation** over-stabilized (~60–80 kcal/mol); the ordinary
   cation ladder is otherwise textbook-correct (H43). Methyl *radical* is fine.
5. **F₂ (charge-shift) dissociation** — non-monotonic, BDE ~4× too high (H55);
   C–C homolysis is fine (H24).
6. **Suspected over-stabilization of small non-classical cations** (vinyl,
   halonium) — unverified without DFT (H23/H37; raised in `DFT_REQUESTS.md`).

## Cross-cutting methodological lesson
Single-start relaxation repeatedly missed the true minimum for non-classical /
chelated / degenerate cases (ethyl cation H6→H9, halonium H21→H23, cyclopentadienyl
H14→H15, acetylacetone RAHB H72). **Multi-start / symmetry-broken seeding is
mandatory** — the core justification for `explore.py`.

*For the reviewer, heading to the aromaticity group: UMA is an excellent fast engine for
closed-shell physical-organic mechanism work — map surfaces, rank isomers,
quantify hyperconjugation/aromaticity/resonance — but spot-check open-shell,
degenerate, and bare-electron-deficient-cation cases against real theory.*
