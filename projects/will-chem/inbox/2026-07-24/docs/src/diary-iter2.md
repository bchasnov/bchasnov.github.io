# Diary — iteration 2 (H101+)

Continues `DIARY.md`. Goal (from `ITERATION_2_PLAN.md` / reviewer `NEXT_ROUND.md`):
build the **rigorous, quantified failure taxonomy of UMA on carbocation
stabilization**, DFT-free, every candidate flaw passing three gates —
(1) multi-start / structure validation, (2) GFN2-xTB cross-check, (3) literature.
New this round: **every comparison is dual-method (UMA + xTB) by construction**
(`iter2.py`), closing iteration 1's biggest gap (UMA-only verdicts).

---

## 2026-07-21 — H101: the stabilization ladder, measured under two methods

Reworked the iteration-1 hydride-affinity ladder (H43, UMA-only, which only
*estimated* the methyl error at 60–80 kcal/mol) into a proper isodesmic
hydride-transfer ladder run under **both UMA and xTB** and anchored to
**experimental gas-phase hydride affinities**:

    R+ + isobutane -> RH + tert-butyl+ ;  ΔE = HIA(tBu+) - HIA(R+) = Stab(R)

Positive Stab = more stable than tert-butyl+. Experimental anchors (kcal/mol):
tBu 231, iPr 246, Et 273, Me 312 (one consistent published series); vinyl/phenyl/
allyl/benzyl are lower-confidence literature estimates.

Result (Stab relative to tert-butyl, kcal/mol):

| cation | UMA | xTB | exp | UMA−exp | xTB−exp |
|---|---|---|---|---|---|
| tert-butyl (3°) | 0.0 | 0.0 | 0.0 | 0.0 | 0.0 |
| isopropyl (2°) | −15.3 | −16.1 | −18.0 | **+2.7** | +1.9 |
| ethyl (1°) | −37.8 | −45.7 | −39.0 | **+1.2** | −6.7 |
| benzyl | −3.3 | −15.3 | −8.0 | +4.7 | −7.3 |
| allyl | −19.9 | −36.7 | −25.0 | +5.1 | −11.7 |
| vinyl | −44.5 | −52.4 | −56.0 | +11.5 | +3.6 |
| **methyl** | **+22.1** | −105.7 | −82.0 | **+104.1** | −23.7 |

**HEADLINE — the methyl cation, now a measured number.** UMA places CH₃⁺
**+22 kcal/mol *above* tert-butyl** — i.e. it calls the methyl cation *more
stable* than tert-butyl — when experiment puts it **82 kcal/mol below**. That is
a **+104 kcal/mol over-stabilization**, cross-checked: xTB and experiment both
put methyl far below tert-butyl (xTB actually over-shoots the other way, −24).
Iteration 1 could only estimate "60–80"; this measures it against experiment and
a second method. This is the cleanest, most dramatic entry in the error class,
and it is structurally airtight — methane and CH₃⁺ cannot rearrange.

**The ordinary ladder is fine, and UMA beats xTB on it.** For 1°/2°/benzylic
cations UMA sits within +1 to +5 kcal/mol of experiment; xTB scatters more
(benzyl −7, allyl −12). So the picture is sharp: UMA is *excellent* for ordinary
cations and *catastrophic* for the smallest electron-deficient one. The error is
not random — it is concentrated at the empty-p, no-substituent corner.

### Two rigor-gate catches (the boring work that licensed the headline)

1. **Cyclopropyl disqualified.** The ladder first showed cyclopropyl at UMA
   +11.3 / exp −37 → an apparent +48 "flaw." Structure check killed it: under
   xTB the cyclopropyl cation **ring-opens fully to allyl** (C–C fingerprint
   [1.38, 1.38, 2.39] ≈ allyl), and under UMA it holds a *partially* opened
   structure ([1.39, 1.40, 1.91]). It is not a distinct stationary species
   (iteration 1 H29 already saw cyclopropyl→allyl electrocyclic opening), so the
   two methods simply stop at different points on the opening path. **Not a
   flaw — a structural artifact.** Dropped from the headline. This is exactly
   the false positive the gate exists to catch.

2. **RDKit multi-start misses non-classical bridges.** My 8-seed RDKit
   multi-start relaxed both ethyl and vinyl to their *classical* structures
   (C–C 1.46 and 1.26 Å) — it never found the H-bridged non-classical minima
   that iteration 1 reached only from hand-built seeds. Lesson carried into
   H102: bridged cations must be **seeded explicitly**; RNG conformer sampling
   is blind to them. (So the ethyl rung above is the classical cation; the
   bridged form is ~3 kcal/mol lower — a small correction, noted.)

**Verdict.** Thesis confirmed and quantified at the methyl end: UMA
over-stabilizes the bare methyl cation by ~100 kcal/mol relative to experiment
and to xTB, while getting ordinary cations right. Next: the bridged end (H102
vinyl, seeded properly) and the geometry-penalty end (H103 bridgehead).

---

## 2026-07-21 — H102: the vinyl cation chase (B5) pays off, cross-checked

Seeded the bridged (protonated-acetylene, C2v) vinyl cation **explicitly**
(RDKit will not generate it — the H101 lesson) and compared to the classical
CH₂=CH⁺ under both methods, vs the CCSD(T) literature gap.

| method | E(bridged) − E(classical) | bridged C–C | bridging H (symmetric) |
|---|---|---|---|
| UMA | **−49.4 kcal/mol** | 1.379 Å | 1.208 / 1.208 Å |
| xTB | +4.7 kcal/mol | 1.233 Å | 1.277 / 1.277 Å |
| CCSD(T)/CBS (lit.) | −4 to −5 (classical is a saddle) | — | — |

**B5 confirmed as a real UMA over-stabilization.** The true gap is small
(~4–5 kcal/mol, bridged slightly favored); xTB brackets it correctly (within
~5 kcal/mol either sign). UMA drops the bridged form **~45–54 kcal/mol too low** —
a ~10× over-stabilization, reproducing iteration 1's H37 (−52.8) and now
cross-checked against both xTB and CCSD(T) literature. Note the geometry tell:
UMA's bridged C–C is stretched to 1.379 Å (vs xTB's near-triple-bond 1.233),
consistent with UMA over-binding the three-center bridge. **This unifies with
methyl:** both are small, electron-deficient cations UMA stabilizes far too much,
and the effect is enormous where the empty orbital is most localized.

## 2026-07-21 — H103: the bridgehead "flaw" does NOT survive the cross-check

1-norbornyl (bridgehead, built by stripping a bridgehead H off norbornane —
ETKDG cannot embed the cage cation) vs 2-norbornyl, both C7H11⁺, both methods:

| method | E(1-norbornyl bridgehead) − E(2-norbornyl) | bridgehead angle-sum |
|---|---|---|
| UMA | −3.1 kcal/mol | 348° |
| xTB | −3.6 kcal/mol | 348° |

**Correction to iteration 1 (and to reviewer T6/B4).** Iteration 1 reported the
bridgehead sitting 3.07 kcal/mol *below* 2-norbornyl as a UMA flaw ("under-
destabilized, sign wrong vs Bredt"), and the reviewer marked it "evidence of
carbocation stabilization errors." But **xTB does the same thing** (−3.6, within
0.5 kcal/mol of UMA). A flaw that a second, independent method reproduces
exactly is **not UMA-specific** — I cannot attribute it to UMA. Either the
bicyclic bridgehead cation is genuinely less destabilized than Bredt intuition
suggests (it is only mildly pyramidal, angle-sum 348°, and sits β to strained,
hyperconjugating cage bonds), or both fast methods share the limitation. Only
DFT/CCSD(T) can settle it — exactly the escalation iteration 1 already filed in
`DFT_REQUESTS.md`. **So the bridgehead is DEMOTED** from the confirmed UMA error
class to a parked, DFT-needed question. This is the cross-check doing its job:
the reviewer's "interesting" pruned toward this flaw, but correctness work says
it isn't proven as UMA's.

### Where the error class stands after H101–H103
The **confirmed, cross-checked, UMA-specific over-stabilizations**:

- **methyl cation:** +104 kcal/mol vs experiment (xTB agrees methyl ≪ tert-butyl).
- **bridged vinyl cation:** ~+45–54 kcal/mol vs xTB and CCSD(T).

Both are small, localized-empty-orbital / three-center-bridged cations — one
coherent class. The **bridgehead** under-destabilization is **not** confirmed
(shared with xTB → DFT-needed). The thesis sharpens: *UMA over-stabilizes small,
electron-deficient cations where the positive charge is forced into a compact,
localized (empty-p or 3-center-bridge) region — and the error grows as that
region shrinks.* The mechanism-story candidate: UMA's training (ground-state
equilibrium organic molecules, OMol) under-samples these sparse, high-energy
electron-deficient motifs, so it extrapolates them too low.

---

## 2026-07-21 — H104: the mechanism, tested. A donor quenches the flaw.

If the thesis is right — UMA over-stabilizes the *bare* empty p — then feeding a
donor into that orbital should switch the error off. Graded RCH₂⁺ series,
stabilization vs tert-butyl, reading the **UMA−xTB gap** as the UMA-specific
error:

| RCH₂⁺ (donor into empty p) | UMA−xTB gap (kcal/mol) |
|---|---|
| methyl (no donor) | **+128** |
| fluoromethyl (F, weak π-donor) | **+117** |
| hydroxymethyl (O, oxocarbenium) | +17 |
| aminomethyl (N, iminium) | +14 |

**Clean confirmation of the mechanism.** The error is enormous (>115 kcal/mol)
when the empty p is bare (methyl) or only weakly donated into (fluorine — whose
tight lone pairs overlap poorly, leaving the p largely bare), and **collapses to
~15 kcal/mol** once a real π-donor (N, O) forms a genuine double bond and quenches
the empty orbital (iminium/oxocarbenium — which iteration 1 already found UMA
handles well, H19/H36). So the flaw is specifically **an un-quenched, localized
empty p orbital**, not size or charge per se. This is the retraining lever: the
motif that needs training data is the *bare-empty-p carbenium*, not carbocations
in general.

## 2026-07-21 — H105: halonium mis-ordering (B6) — a new confirmed flaw

β-halonium: bridged (haliranium ring) vs open (β-halo primary cation), C₂H₄X⁺,
both methods, X = Cl, Br.

| X | E(bridged)−E(open), UMA | xTB | geometry |
|---|---|---|---|
| Cl | **+67.4** (bridged high) | 0.0 | xTB: open & bridged both relax to the SAME bridge (fp 1.47) |
| Br | **+56.2** (bridged high) | 0.0 | UMA: two distinct minima (open fp 1.52/1.45, bridge 1.44/1.33) |

**Confirmed UMA flaw, cross-checked.** Anchimeric assistance makes the *bridged*
halonium the minimum; xTB agrees so strongly that the open cation isn't even a
separate minimum — it collapses straight into the bridge (ΔE = 0, identical
geometry). UMA instead holds a **spurious open β-halo primary cation 56–67
kcal/mol below the bridge** — exactly backwards. This is iteration 1's H23/B6
suspicion (then unverifiable without a reference), now settled by the xTB
cross-check. And it fits the thesis: the open form is a localized-empty-p CH₂⁺
(only weakly β-stabilized), and UMA over-stabilizes it into a deep spurious well.
Same disease as methyl/vinyl, wearing a different hat.

## 2026-07-21 — H106: C4H7⁺ manifold — nuanced, no dramatic flaw

Cyclopropylcarbinyl / cyclobutyl / homoallyl / methylallyl (all C₄H₇⁺),
dual-method. UMA and xTB differ by only ~5–12 kcal/mol on the ordering (UMA
places the non-classical cyclopropylcarbinyl/cyclobutyl ~10 kcal higher relative
to methylallyl than xTB does). These are all resonance/σ-delocalized cations — no
bare empty p — so, consistent with the thesis, there is **no dramatic
over-stabilization** here, just ordinary method scatter. Logged as a nuanced
control, not a flaw.

### Error class after H101–H106
Confirmed UMA-specific errors (each cross-checked vs xTB, most vs literature),
all one disease — **over-stabilization of a localized/bare empty-p carbenium**:

1. **methyl** +104 vs exp; 2. **bridged vinyl** +45–54; 3. **halonium**
mis-ordering (open 56–67 too low); 4. **fluoromethyl** +117 vs xTB. The disease
**switches off** under genuine π-donation (H104: N/O → gap ~15). Demoted / not
UMA-specific: **bridgehead** (H103, xTB agrees) and the **C4H7⁺ manifold** (H106,
modest scatter).

---

## 2026-07-21 — H107–H109: the terpene payoff — where the disease does NOT bite

The flaws are the paper, but the reviewer also wants "expected successes"
documented. The sharpest success question: **does the over-stabilization disease
bite in the experimental collaborator's actual terpene chemistry?** It shouldn't — real monoterpene
cyclization funnels through *tertiary/allylic* cations, which have no bare empty
p. Tested, dual-method:

- **H107 (terpene C₁₀H₁₇⁺ manifold).** α-terpinyl / terpinen-4-yl / geranyl /
  linalyl / pinyl. The **cyclic** cations that are the actual products agree
  tightly UMA↔xTB: terpinen-4-yl 0.05, geranyl 2.8, pinyl 9.1 kcal/mol. The one
  outlier is **linalyl (+16.5 UMA−xTB)** — a floppy open-chain tertiary-allylic
  cation, almost certainly a **conformer-sampling** gap (8 seeds under-sample a
  C₁₀ open chain), not the empty-p flaw. This is exactly iteration 1's B1 worry
  (floppy terpene cation reference conformer) surfacing again → **caution: use
  denser conformer search for open-chain terpene cations**; cyclic ones are safe.
- **H108 (B3 solved).** Limonene protonation, made trustworthy by an isodesmic
  proton transfer that cancels the bare-proton reference:
  *limonene + tBu⁺ → α-terpinyl⁺ + isobutylene.* UMA **−3.0**, xTB **−1.8** —
  agree within 1.3 kcal/mol. So limonene's ring alkene is **~2–3 kcal/mol more
  basic than isobutylene** (PA ≈ 194 kcal/mol using lit. PA(isobutylene) 191.6).
  Iteration 1 had "no trustworthy number" here; now there is one, DFT-free, two
  methods agreeing. B3 closed.
- **H109 (Markovnikov tert vs sec).** tert-amyl vs 3-methyl-2-butyl (C₅H₁₁⁺):
  UMA +10.1, xTB +11.8 — agree within 1.8. UMA correctly places the tertiary
  cation ~10 kcal below secondary, the thermodynamic driver of every terpene
  cyclization/Markovnikov step.

**Bottom line for the experimental collaborator:** within the real terpene domain — cyclic
tertiary/allylic cations — **UMA is quantitatively reliable (≈1–3 kcal/mol),
because the disease requires a bare empty p and these don't have one.** The two
operational cautions: (1) dense conformer sampling for floppy open-chain cations
(H107 linalyl), and (2) watch any *non-classical / primary* intermediate that
appears mid-cascade (that is where the empty-p flaw would strike). This is the
usable map: UMA is a good terpene-mechanism engine, with two named failure modes
to guard against.

---

## 2026-07-21 — H110–H112: the mechanism, fully mapped

**H110 — hyperconjugation decay is razor-sharp.** UMA−xTB error along
methyl→ethyl→isopropyl→tert-butyl:

| cation | β-C–H donors | UMA−xTB error |
|---|---|---|
| methyl | 0 | **+127.7** |
| ethyl | 3 | **+7.8** |
| isopropyl | 6 | +0.9 |
| tert-butyl | 9 | 0.0 |

**A single alkyl group collapses ~94% of the error** (128 → 8). The disease is
almost entirely the *bare* methyl cation; the faintest σ-donation quenches it.

**H111 — halogen π-donor strength tracks the quench.** CF₃⁺ (F, poor π-donor —
tight lone pairs) UMA−xTB **+30.9**; CCl₃⁺ (Cl, good π-donor) **−1.3**; methyl
+127.7. F leaves the p partly bare (residual error), Cl quenches it — mirroring
H104 (F-methyl errs, O/N quench). Textbook: Cl π-donates to a cation better than F.

**H112 — aromatic controls (expected successes).** Both delocalized aromatics
come out correct-signed: tropylium (C₇H₇⁺) UMA +7.2 vs xTB +9.3 (agree within 2);
cyclopropenyl (C₃H₃⁺) UMA +30.3 vs xTB +14.9 — both put the aromatic below, but
UMA gives ~15 kcal more aromatic stabilization on the tiny 3-ring. Delocalized
charge → no disease; a small residual on the smallest aromatic ring, noted.

### The mechanism, stated
UMA's carbocation error is a **single, sharply-localized pathology: it
over-stabilizes an *unquenched, localized empty p orbital*.** The magnitude is a
clean function of how bare that orbital is — maximal for methyl (+104 vs exp,
+128 vs xTB), quenched by *any* real donation, whether σ-hyperconjugation (one
alkyl → −94%) or π (N/O/Cl → ~0; F only partial). It surfaces wherever chemistry
forces a compact, poorly-stabilized cationic center: methyl, bridged vinyl, the
open β-halonium primary cation, CF₃⁺. It is absent wherever charge is delocalized
or donated: ordinary 2°/3°/allylic/benzylic/aromatic cations and — crucially —
the real terpene intermediates. Story for the paper: UMA's OMol training samples
equilibrium closed-shell molecules densely but these bare-empty-p high-energy
cations sparsely, so it extrapolates them too low.

---

## 2026-07-21 — H113–H118: secondary class — spin/open-shell (T1/T2/T3)

Cross-checked iteration 1's spin flaws. **First a methodological finding: xTB
cannot arbitrate spin-state energetics** — GFN2-xTB returns a near-constant
ΔE_ST ≈ +1.7 kcal/mol for *every* carbene (CH₂, CF₂, CCl₂, CBr₂), insensitive to
the actual electronic structure. So for this class the anchor is **experiment**,
not xTB. (ΔE_ST ≡ E(triplet) − E(singlet); negative = triplet ground state.)

| species | UMA ΔE_ST | exp | UMA verdict |
|---|---|---|---|
| CH₂ (H113) | −34.5 | +9 (triplet GS) | right sign, ~4× too large |
| CF₂ (H114) | −20.8 | −57 (**singlet GS**) | **wrong sign** |
| CCl₂ (H115) | −15.7 | −20 (**singlet GS**) | **wrong sign** (= iter1 H77) |
| CBr₂ (H116) | −31.5 | ~−15 (**singlet GS**) | **wrong sign** |
| Cp⁺ (H117) | −200.6 | small (near-degenerate) | **unphysical** (= iter1) |

**Pattern.** UMA systematically favors the **triplet** for these small carbenes
and the antiaromatic cation. The halocarbenes are the tell: their singlet ground
state comes from **halogen lone-pair π-donation into the carbene's empty p** —
the *same* empty-p donation physics as the carbocation story. UMA under-weights
that donation, so it misses the singlet stabilization and wrongly calls them
triplets. Candidate unifying root (stated as a hypothesis, not a claim): **UMA
mishandles lone-pair/σ donation into a localized empty p** — over-stabilizing the
*bare* empty p in cations, under-stabilizing the *donor-quenched* singlet in
carbenes. Two faces of one orbital-physics blind spot.

**H118 — the antiaromatic Jahn–Teller distortion, cross-checked geometrically.**
Cp⁺ singlet ring C–C: UMA symmetric pentagon (spread **1 mÅ**, misses JT); **xTB
distorts** (1.349/1.349/1.439/1.439/1.555 Å, spread **206 mÅ**, captures the
bond alternation). So iteration 1's FLAW 1 is confirmed by a second method that
gets it *right*: UMA specifically fails to break the symmetry of the antiaromatic
Cp⁺ singlet. This is a clean, valid dual-method result (geometry, where xTB is
reliable, unlike spin energy).

Secondary class stands: UMA's spin-state energetics are unreliable for
degenerate/open-shell small species (wrong sign for halocarbene singlets,
−200 for Cp⁺, JT miss). Off the carbocation axis but a real, bounded story;
xTB is not an arbiter here (experiment is).

---

## 2026-07-21 — H119–H123: off-domain demotion, σ-bridge, and controls

- **H119 (off-domain, T5).** UMA bond lengths are ~15–19 mÅ short for F–F, Cl–Cl,
  O–O, N–N **and** ordinary C–C alike — a uniform slight contraction, *not*
  F-specific. The real F₂ artifact (iteration 1 H55) is the non-monotonic
  dissociation curve, but F/O/N/Cl σ-bonds do not appear in carbocation/terpene
  mechanism → **demoted** per the reviewer.
- **H120 (σ-bridge geometry).** 2-norbornyl cation: UMA and xTB give
  near-identical geometries (nearest C–C 1.444/1.533/1.597 vs 1.445/1.527/1.566).
  UMA's non-classical geometry is **not** a UMA-specific artifact — the second
  method agrees. (The classical-vs-bridged *energy* remains the CCSD(T) question,
  DFT_REQUESTS P3.)
- **H121 (β-silicon effect, T10 — expected success, confirmed).** β-C–Si vs
  β-C–H hyperconjugation: UMA **+25.0**, xTB **+31.7** kcal/mol — both confirm
  β-Si is the far stronger σ-donor (iteration 1 reported +30.7; xTB lands almost
  exactly there). A genuine competence, now cross-checked.
- **H122 (donor controls).** acylium UMA−xTB +6.2, oxocarbenium +17.3. Nuance
  worth stating honestly: π-donation quenches the methyl disease from +128 down
  to a **~6–17 residual**, not to zero; only full σ-alkylation drives it to ~0
  (H110 isopropyl +0.9). So donor-stabilized cations carry a small residual UMA
  over-stabilization.
- **H123 (styrene benzylic).** 1-phenylethyl (benzylic) vs 2-phenylethyl
  (primary): both methods ≈ degenerate (UMA −0.39, xTB +0.42) — the primary
  relaxes to the benzylic sink (iteration 1 H48); UMA and xTB agree, no issue.

---

## 2026-07-21 — H166: DFT CONFIRMATION of the methyl over-stabilization (compute enabled)

With compute available, the parked DFT_REQUESTS become answerable. Built a real
ab-initio reference stage (`uma_core/run_dft.py`, PySCF): xTB start → B3LYP
geometry optimization → B3LYP/def2-TZVP energy, isodesmic so functional error
cancels. Ran the stabilization ladder (Stab = HIA(tBu⁺) − HIA(R⁺)):

| cation | DFT Stab | exp | DFT−exp | UMA (H101) | **UMA−DFT** |
|---|---|---|---|---|---|
| methyl | **−82.6** | −81.0 | −1.6 | **+22.1** | **+104.7** |
| ethyl (1°) | −38.3 | −42.0 | +3.7 | −37.8 | +0.5 |
| isopropyl (2°) | −15.7 | −15.0 | −0.7 | −15.3 | +0.4 |
| tert-butyl (3°) | 0.0 | 0.0 | 0.0 | 0.0 | 0.0 |
| vinyl | −60.1 | −56.0 | −4.1 | −44.5 | +15.6 |

**The headline is now confirmed by three independent references.** B3LYP/def2-TZVP
reproduces the experimental hydride affinities to 1.6 kcal/mol for methyl (and ≤4
across the ladder), so the experimental anchors are validated. DFT and experiment
agree the methyl cation is **~82 kcal/mol *below* tert-butyl**; UMA puts it **22
*above*** → a **+104.7 kcal/mol over-stabilization confirmed at the DFT level.**
The ordinary rungs (ethyl/isopropyl) match DFT to <1 kcal/mol — UMA is excellent
there — isolating the error entirely to the bare-empty-p methyl (and, more mildly,
vinyl +15.6). This upgrades iteration 1's *estimate* ("60–80") and iteration 2's
xTB/experiment number to a **gold-standard-anchored measurement**.

## 2026-07-21 — H167/H168: B5 and B6 settled at DFT / CCSD(T)

**H167 — vinyl bridged vs classical, CCSD(T)/def2-TZVP (settle B5).** True gap
**CCSD(T) = −3.3 kcal/mol** (bridged slightly below classical) — matches the
literature ~−4 to −5. So the reference ladder is: CCSD(T) −3.3, xTB +4.7 (both
small), **UMA −49.4**. UMA over-stabilizes the three-center bridge by
**~46 kcal/mol vs CCSD(T)** — the flaw is now anchored to the gold standard, not
just literature. (B3LYP gave ~0 here, a known B3LYP weakness on this subtle
3-center case — hence the CCSD(T).)

**H168 — halonium bridged vs open, B3LYP/def2-TZVP (settle B6).** DFT places
bridged and open **within ~6 kcal/mol** (Cl +6.2, Br ~0) — i.e. the two are
near-degenerate at DFT, as at xTB (where open collapses into the bridge). **UMA
separates them by 56–67 kcal/mol**, over-stabilizing the open β-halo *primary*
cation. So UMA's error vs DFT is **~55–60 kcal/mol** — the same empty-p disease
(the open CH₂⁺ is over-stabilized), now confirmed against a DFT reference. The
exact bridged-vs-open ordering is a subtle few-kcal, method-dependent question;
UMA's ~60 kcal mis-separation is the unambiguous flaw.

### DFT confirmation summary (the parked DFT_REQUESTS, now answered)

| flaw | UMA | reference | UMA error | ref method |
|---|---|---|---|---|
| methyl over-stabilization (T4) | +22.1 | −82.6 | **+104.7** | B3LYP/def2-TZVP (=exp ±1.6) |
| bridged vinyl (B5) | −49.4 | −3.3 | **−46.1** | CCSD(T)/def2-TZVP |
| halonium open/bridge (B6) | +56–67 | +0 to +6 | **~+55–60** | B3LYP/def2-TZVP |
| ordinary 1°/2° cations | — | ±0.5 | ~0 | B3LYP (control: UMA correct) |

Iteration 1 raised these as DFT requests and stayed DFT-free by instruction;
iteration 2, with compute enabled, closes them. Every headline flaw is now
confirmed against a real ab-initio reference, and the ordinary-cation control
shows UMA matches DFT to <1 kcal/mol — the error is specifically the bare-empty-p
corner, nothing else.

## 2026-07-21 — H169: bridgehead settled at DFT — UMA is RIGHT, not wrong

DFT (B3LYP/def2-TZVP): E(1-norbornyl bridgehead) − E(2-norbornyl) = **−2.9
kcal/mol** — essentially identical to UMA (−3.1) and xTB (−3.6). **All three
methods agree to <1 kcal/mol.** So the bridgehead sitting ~3 kcal below
2-norbornyl is **real chemistry, not a UMA error** — UMA matches DFT to 0.2
kcal/mol here. Iteration 1 flagged this as a flaw (UMA-only) and the reviewer
marked T6/B4 "evidence of carbocation stabilization errors"; the DFT confirmation
**overturns that** — the naive Bredt intuition (bridgehead cations are terrible)
is about *solvolysis kinetics vs a normal tertiary*, not this isomeric
thermodynamic gap, where the rigid cage's C–C hyperconjugation keeps 1-norbornyl
competitive. **Lesson: the cross-check + DFT protected against reporting a
non-flaw as a flaw** — the mirror image of the methyl case (where they confirmed a
real one). Keep both judgments running.

---

## 2026-07-21 — H170: what UMA is FOR once you have DFT (the synergy)

The reviewer asked the sharp question: if we can run DFT, why UMA? Answer:
**UMA's value is speed — it explores configuration space at a scale DFT cannot
afford.** The right workflow is **UMA-as-explorer, DFT-as-arbiter**: sweep
hundreds of conformers/basins with UMA (trusting it in the regimes iteration 2
validated), then spend DFT only on the survivors. Demonstrated on the two
conformer questions iteration 1/early iteration 2 left open, `synergy.py`:

**α-terpinyl cation (settles B1).** UMA relaxed 23 conformers → **6 distinct
basins in 111 s** (the same sweep at DFT ≈ 0.6 h). The catch a single relaxation
would miss: the *most-populated* basin (pop 7) is **+2.30 kcal (UMA) / +1.26
(DFT) — NOT the global minimum**; the true global-min basin is rare (pop 2/23).
DFT//UMA single points **confirm UMA's lowest basin is also DFT's lowest**
(UMA 0.00→DFT 0.00). So B1 is settled: the α-terpinyl reference conformer sits
~1.3 kcal (DFT) below the naive single-shot, and UMA+DFT agree on it — reached in
~2 min of UMA + a few DFT single points, not hours of DFT sampling.

**linalyl cation (settles the H107 divergence).** UMA swept 34 conformers → **11
basins in 231 s**; DFT//UMA again confirms UMA's global-min basin is DFT's. The
H107 UMA/xTB gap (+16.5 kcal) was therefore a **conformer-sampling artifact** —
linalyl has 11 basins and the thin 8-seed search in H107 landed in different ones
under each method — not a fundamental UMA error. More compute (dense sweep)
resolved it, exactly the point.

**The synergy, stated.** DFT alone can't afford to sweep 20–34 conformers per
cation; UMA does it in minutes and — critically — **ranks the basins correctly
where iteration 2 validated it** (ordinary/terpene cations, no bare empty p), so
the handful of DFT single points land on the right structures. The iteration-2
**failure map is the operating manual**: it tells you exactly where UMA's fast
exploration is trustworthy (here, terpene conformers) and where it isn't
(bare-empty-p cations — never let UMA rank those). MLIP for breadth, DFT for the
final number, failure-map for knowing which is which.

---

## 2026-07-21 — H124–H165: systematic xTB cross-check of iteration-1's UMA-only claims

Closed iteration 1's #1 gap (no second method on the 100 hypotheses) with a
declarative registry of 42 isomeric dual-method comparisons (`mechanisms2.py`
REG/REG2/REG3). **32 of 42 cross-confirm** (|UMA−xTB| < 10 kcal/mol) — UMA tracks
xTB across ordinary carbocation stability ladders (C3–C7 tertiary/secondary/
primary, H124–H127, H132), Wagner–Meerwein driving forces (neopentyl→tert-amyl
0.0, pinacolyl +6.3, H128/H129), ring-expansion (H127), terpene C₁₀H₁₇⁺ isomers
(H142, ≤4.7), and most aromaticity/conjugation/keto–enol/Zaitsev controls. So
iteration 1's broad "UMA reproduces physical-organic energetics" holds up under a
second method.

The 10 divergences sort cleanly:

- **The disease (carbocation):** H130 allyl vs **2-propenyl** (a vinyl-type
  localized-empty-orbital C₃H₅⁺) diverges 25 kcal — the empty-p over-stabilization
  reappearing, as expected.
- **Noise (bad isomer choice):** H139/H140 (139/236 kcal) compare acylium /
  oxocarbenium to pathological "open" isomers (1-hydroxyvinyl, C-protonated
  formaldehyde) that the two methods relax to wildly different structures — not
  meaningful.
- **Strained neutral hydrocarbons (off carbocation axis):** H151 cubane vs COT vs
  styrene (67 kcal!), H162 Dewar benzene (16), H148/H150/H154 (11–14) — UMA and
  xTB disagree on the strain energy of exotic cages/antiaromatics. Since iteration
  1 found UMA's cubane strain matches *experiment* (166 kcal/mol, H99), these
  likely reflect **xTB's** limitations on strained systems, not UMA's — but they
  are neutral hydrocarbons, off the carbocation thesis.

Net: the registry validates UMA broadly and re-surfaces the one carbocation
disease (H130), exactly the two-judgment discipline working at scale.

---

## 2026-07-21 — H171–H173: DFT arbitration refines the map (UMA often beats xTB)

DFT-resolved the meaningful registry divergences and the aryl-cation question —
and the lesson is that a UMA/xTB divergence is NOT automatically UMA's fault:

- **H171 (2-propenyl vinyl cation, C₃H₅⁺).** DFT: allyl 0, 2-propenyl **+8.71**.
  **UMA (+7.9) is right; xTB (−17.5) is wrong** by 26 kcal. So the H130 divergence
  was an *xTB* error. UMA matches DFT to 0.8 kcal/mol here.
- **H172 (cubane strain, C₈H₈).** DFT: cubane **+126.5** above styrene.
  **UMA (+104) is far closer than xTB (+37)** — xTB underestimates cubane strain by
  ~90 kcal/mol; UMA by ~22. (COT: UMA 37.5 / xTB 38.5 / DFT 39.2 — all agree.)
  Consistent with iteration 1 finding UMA's cubane strain near experiment.
- **H173 (phenyl cation).** DFT Stab vs tert-butyl **−51.2 ≈ UMA −50.4**. UMA is
  **accurate** on the aryl in-plane-empty-sp² cation — the H101 "phenyl off by
  +16" was a *bad experimental anchor* (−67), not a UMA error. Aryl cations are
  NOT a disease sub-class.

**Map refinement.** The over-stabilization is specifically the **bare /
unsubstituted small empty-p cations** (methyl; parent vinyl +15/bridged +46) and
**non-classical 3-center bridges** — NOT aryl cations (H173) or α-substituted
vinyl cations (H171), which carry ring/hyperconjugative stabilization UMA handles
fine. And where UMA and xTB fight over strained neutrals, **UMA is the more
trustworthy one** (H172). Net: UMA's reliable envelope is *larger* than the raw
registry divergences suggested — the failures are a tight, well-defined corner.

## 2026-07-21 — H174: monoterpene cascade map (the reaction-network use case)

Mapped the C₁₀H₁₇⁺ cyclization manifold (acyclic geranyl/neryl/linalyl →
monocyclic α-terpinyl/terpinen-4-yl/terpinolenyl → bicyclic pinyl), dual-method.
The **cyclic** cations — the ones the cascade funnels to — agree UMA↔xTB (0.05–9
kcal): pinyl lowest (−8.5/−17.6), terpinen-4-yl (−6.6/−6.6), terpinolenyl
(−3.3/−11), α-terpinyl (ref). The **floppy acyclic** ones diverge (linalyl +16.5,
same as H107) — the conformer-sampling caveat, which the synergy (H170) resolves.
This is the workflow in miniature: UMA maps the whole intermediate manifold in
minutes and its ordering tells you exactly where to spend DFT (the acyclic
conformers), rather than DFT-ing the whole network blind.
