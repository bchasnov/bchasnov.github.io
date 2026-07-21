# UMA flaws & limitations found (uma-s-1p1, omol task, CPU)

Adversarial calibration log. Each entry is a *reproducible* failure or limitation
discovered while using UMA as a mechanism-exploration engine, with the exact
experiment that exposes it. Rerun any with `python -m uma_core.mechanisms <id>`.

Context: UMA passed the basic instrument checks (`stress_test.py`, 6/6:
determinism, translation/rotation invariance, charge sensitivity, basin
stability, cross-method tert–sec gap). The failures below are all in regimes a
ground-state-DFT-trained potential is *expected* to struggle with —
antiaromatic / degenerate / open-shell electronic structure — which is why they
matter for anyone using UMA on reactive intermediates.

---

## FLAW 1 — misses antiaromatic Jahn–Teller distortion  (H14, H15)

**System.** Cyclopentadienyl cation, C5H5⁺ (singlet, 4π antiaromatic).

**Expected.** A 4π antiaromatic singlet is Jahn–Teller unstable at D5h and must
distort to a bond-alternated (C2v) structure — the geometric hallmark of
antiaromaticity.

**UMA.** Relaxes to a **perfectly symmetric pentagon** (ring C–C = 1.419 Å,
1 mÅ spread). Symmetry-breaking with four independent 0.20 Å random distortions
re-relaxes straight back to the symmetric structure with **zero energy gain** —
so this is a genuine symmetric minimum on UMA's surface, not a saddle artifact.

**Contrast.** UMA *does* correctly equalize the truly aromatic cyclopropenyl
cation (H12/H13) and stabilizes it (30.3 kcal/mol below its open isomer).

**Boundary of the flaw (H27).** UMA correctly gives *neutral* cyclobutadiene its
**rectangular, bond-alternated** antiaromatic structure (1.328/1.569 Å, 242 mÅ
alternation). So UMA is NOT blind to antiaromatic distortion in general — it
tracks ground-state DFT, which describes closed-shell cyclobutadiene fine. The
failure is specific to the **cyclopentadienyl cation**, whose true ground state
is an open-shell triplet and whose singlet is genuinely degenerate/multireference
— exactly where a ground-state-DFT surrogate has no signal. Use as a rule: trust
UMA on antiaromatics that are closed-shell singlets; distrust it on degenerate/
open-shell ions.

**Further narrowing (H30).** UMA also correctly *destabilizes* the cyclopropenyl
**anion** (4π antiaromatic): +53.2 kcal/mol above its open isomer, mirroring the
aromatic cyclopropenyl cation's −30 kcal/mol. So both a neutral (cyclobutadiene)
and an anionic (cyclopropenyl⁻) antiaromatic are handled correctly. The failure
is confined to the **cyclopentadienyl cation** specifically — the one case here
with a genuine triplet ground state and a doubly-degenerate singlet. Net: this is
a pinpoint multireference/open-shell-degeneracy blind spot, not a general
antiaromaticity failure.

## FLAW 2 — grossly wrong triplet energy for the antiaromatic cation  (H15, H16)

**System.** Same C5H5⁺, singlet vs triplet.

**UMA.** E(triplet) − E(singlet) ≈ **−200 kcal/mol** (both vertically on the
singlet geometry and after triplet relaxation). A spin-state gap of −200 kcal/mol
is unphysical (real gaps are a few kcal/mol).

**Not a usage error.** The spin channel is calibrated and sane on knowns
(H16): O2 gives +26.2 kcal/mol (exp ≈ +22.5, triplet ground state) and CH2 gives
+18.5 kcal/mol (exp ≈ +9). So spin works in general (though CH2 shows it is
already ~2× loose), and the −200 kcal/mol on the antiaromatic cation is a real
UMA pathology, not a mistake in how spin/charge are supplied.

**Takeaway.** Do not trust UMA spin-state energetics for antiaromatic /
degenerate open-shell ions; treat even simple carbene/O2-type gaps as only
qualitative (correct sign, loose magnitude).

---

## FLAW 3 — bridgehead (Bredt) cation under-destabilized  (H28)

**System.** 1-norbornyl cation (bridgehead, C7H11⁺) vs 2-norbornyl cation
(isomeric).

**Expected.** Bridgehead carbocations are hugely destabilized — they cannot
attain the planar sp² geometry a cation wants (Bredt's rule); 1-norbornyl
solvolyzes ~10¹³× slower than a normal tertiary substrate, so the 1-norbornyl
cation should lie well *above* the (specially stabilized, non-classical)
2-norbornyl cation.

**UMA.** Places 1-norbornyl **3.1 kcal/mol below** 2-norbornyl. The relaxed
bridgehead structure is a genuine intact norbornane cage (8 C–C bonds, cation on
a 3-coordinate bridgehead carbon, 0 H — verified, not a rearrangement), only
mildly pyramidalized (angle-sum 348° vs 360° planar). UMA under-penalizes a
cationic center that is prevented from planarizing/delocalizing.

**Status.** Sign qualitatively wrong vs Bredt's rule; exact magnitude wants a DFT
reference (raised in `DFT_REQUESTS.md`).

---

## LIMITATION 4 — single-start relaxation misses non-classical minima  (H6, H9, H21)

Not a model error, but a usage trap. A single RDKit-seeded relaxation of the
ethyl cation gave a *classical* structure (C–C 1.456 Å); seeding a bridged
geometry gave the correct **H-bridged non-classical** minimum, **3.08 kcal/mol
lower**. If one relaxation misses the global minimum of the smallest carbocation,
it will elsewhere — non-classical ions require multi-start / symmetry-broken
seeding (see `explore.py`).

---

*Method note: every energy here is an isomer or fixed-composition comparison, or
a spin/charge gap on one system, so UMA's absolute-reference issues do not
contaminate the conclusions. No DFT used.*

---

## SUSPECTED PATTERN — over-stabilization of some non-classical small cations  (H23, H37; unverified)

Two independent cases where UMA makes a bridged/non-classical small cation
implausibly *low* relative to the alternative:
- **β-halonium (H23):** bridged halonium sit 49–67 kcal/mol from the open forms.
- **vinyl cation (H37):** the bridged (non-classical) C2H3⁺ comes out **52.8
  kcal/mol below** the classical vinyl cation; the known gap is only ~4–5 kcal/mol.

Counter-example: the **ethyl** cation H-bridge (H9) is only −3.08 kcal/mol below
classical — correct. So this is not universal; it seems to hit specific
multi-center-bonded charged species. Neutral bond dissociation is fine (H24), so
it is not a generic stretched-bond issue.

**Status: UNVERIFIED.** Magnitudes are implausible but I have no DFT reference to
confirm the sign/size. Raised in `DFT_REQUESTS.md`. Do not treat these particular
energy gaps as trustworthy.

---

## FLAW 5 — methyl cation over-stabilized; small electron-deficient cations over-rewarded  (H43; H37, H23)

**System.** Carbocation hydride-affinity ladder (isodesmic, cross-size).

**Result.** The ladder among ethyl/isopropyl/tert-butyl/allyl/benzyl is
*textbook-correct* in both order and spacing (tBu > benzyl > 2° > allyl > 1°,
matching experimental hydride affinities). But the **methyl cation** breaks it:
UMA ranks CH3⁺ as the *most* stable of all (above tert-butyl), when it is in fact
by far the *least* stable carbocation. So UMA over-stabilizes CH3⁺ by roughly
60–80 kcal/mol relative to where the rest of its own (correct) ladder places it.

**Why it's confirmable without DFT.** The internal ladder is self-consistent and
matches experiment for the five larger cations, so the methyl outlier is a real
UMA error, not a reference artifact.

**Connection.** This joins the vinyl-cation (H37, +52.8 vs classical) and
halonium (H23) anomalies: **UMA tends to over-stabilize small, highly
electron-deficient or non-classical cations** — the extreme, sparsely-sampled
corner of chemical space. The ethyl cation (H9) sits at the edge (its bridged
form is only slightly favored, which is correct), so the effect grows as the
cation gets smaller / more electron-deficient. Practical rule: trust UMA for
*relative* stabilities of ordinary (secondary/tertiary/benzylic/allylic) cations
and isomeric comparisons; distrust absolute or cross-size comparisons that
involve CH3⁺ or other tiny/exotic cations.

---

## FLAW 6 — F2 (charge-shift bond) dissociation is grossly wrong  (H55)

**System.** F2 homolytic dissociation curve (rigid F–F scan).

**Expected.** Monotonic rise to a plateau ≈ the F–F bond dissociation energy,
~38 kcal/mol.

**UMA.** The curve rises to +149 kcal/mol at 3.5 Å then **drops to +127 at 4.5 Å**
— non-monotonic, with a spurious turnover — and the plateau (~4× the true BDE)
grossly overestimates the bond strength. The equilibrium bond is also ~30 mÅ
short (1.38 vs 1.41 Å).

**Contrast.** Ethane C–C homolysis (H24) is perfectly monotonic and lands near
the true BDE (~98 kcal/mol vs ~90). So this is specific to F2 / the charge-shift
F–F bond, not a generic dissociation failure — almost certainly a training-
coverage gap (stretched F–F geometries are rare in the dataset).

**Caveat.** The scan fixes the singlet spin state; restricted-singlet dissociation
is hard for any single-reference method. But the non-monotonic turnover is a real
potential-energy-surface artifact regardless, and the near-equilibrium bond length
error is independent of it.

---

## NARROWING of FLAW 5 (methyl over-stabilization is cation-specific)  (H51)

The radical stability ladder (H51) ranks correctly with the **methyl radical
least stable** — so UMA does NOT over-stabilize CH3•. The over-stabilization is
specific to the electron-deficient **methyl cation** (empty p orbital), not small
species in general. This tightens FLAW 5.

---

## FLAW 7 — carbene singlet–triplet gaps wrong (halocarbene singlet GS missed)  (H77; H16)

**System.** Dichlorocarbene CCl2 singlet vs triplet.

**Expected.** CCl2 has a **singlet** ground state (Cl lone-pair π-donation into
the empty carbene p; ΔE_ST ≈ +20 kcal/mol favoring singlet), unlike CH2 (triplet
GS).

**UMA.** Puts the **triplet 15.7 kcal/mol below** the singlet — a triplet ground
state, wrong by ~35 kcal/mol and the wrong sign. UMA evidently treats CCl2 like
CH2 (H16: CH2 triplet correctly favored but gap already 2× too large) and does not
capture the singlet-stabilizing halogen π-donation.

**Pattern.** With the Cp⁺ triplet anomaly (FLAW 2) and the loose CH2 gap, this
confirms UMA's **spin-state / singlet–triplet energetics are unreliable** —
qualitatively wrong for cases where electronic structure (lone-pair donation,
degeneracy) controls the multiplicity. Closed-shell ground-state energetics remain
excellent; spin-state splittings should be checked against real theory.
