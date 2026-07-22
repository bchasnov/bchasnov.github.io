# Scientist diary — UMA for carbocation/terpene chemistry

A running lab notebook. Newest entries at the bottom. Feelings included, because
they're part of how the reasoning actually went.

_Entry timestamps (UTC) are taken from the git commit that recorded each batch —
the honest record of when the work actually landed._

---

## 2026-07-20 08:08 UTC — Getting the real model running

HF access to `facebook/UMA` finally came through (the token was fine all along;
the gate approval was the missing piece — mildly annoying to have chased the
token first, but the 403 was unambiguous once I looked). Installed the stack,
ran `uma-s-1p1` on all 7 systems. Everything converged in seconds on CPU. Good
first feeling: the instrument is fast enough to *explore*, not just measure.

**Finding.** The C4H9+ "ladder" came out compressed — tert-butyl at 0, then
isobutyl 12.4, n-butyl 12.8, sec-butyl 13.2. That immediately bothered me: the
primaries sitting *below* the secondary is not how carbocations work. Textbook
says primary cations don't even hold a minimum. Something was off — either UMA
is wrong, or the labels are lying about what actually relaxed.

## 2026-07-20 08:12 UTC — Chasing the compressed ladder (H1)

**Hypothesis H1:** the "primary" structures aren't primary minima — they
rearranged during relaxation.

**Test:** dissected every relaxed geometry — C–C connectivity, H counts per
carbon, planarity of the electron-deficient carbon.

**Result — H1 confirmed, and it was satisfying to watch it fall out cleanly:**
- tert-butyl → stays branched/tertiary (both UMA and xTB). ✓
- sec-butyl → stays linear/secondary. ✓
- isobutyl → built *branched/primary*, relaxed to *linear/secondary* in **both**
  methods. It rearranged.
- n-butyl → UMA relaxed it to *linear/secondary*; xTB left it stuck as a strained
  *primary* that never converged (fmax 0.36). So xTB's "+38 kcal/mol primary"
  isn't a minimum at all — it's a non-stationary point.

The compression was never a UMA error. The primaries collapse into the secondary
basin, exactly as carbocation chemistry demands.

## 2026-07-20 08:16 UTC — How many minima are really there? (H2)

**Hypothesis H2:** UMA's three non-tertiary entries are all the *same* 2-butyl
cation, reached from different starts.

**Test:** permutation-invariant distance-spectrum fingerprints + energy spread.

**Result — confirmed.** The three cluster within <1 kcal/mol and are the same
secondary (2-butyl) cation up to conformer scatter. So the C4H9+ surface UMA
sees has **two** real basins: tertiary (0) and secondary (~13). Reframing the
"4-entry ladder" as "2 basins + rearrangement funnels" felt like the moment the
data started telling the truth instead of the input labels.

## 2026-07-20 08:20 UTC — The non-classical worry (H3)

My crude classifier called the 2-norbornyl cation "secondary," which briefly
worried me: did UMA miss the famous non-classical σ-bridge? That would be a big
deal for the experimental collaborator's Wagner–Meerwein chemistry.

**Hypothesis H3:** UMA gives a *classical* 2-norbornyl, missing the bridge.

**Test:** measured the C1–C2–C6 bridging geometry.

**Result — H3 refuted (relief).** The bridging carbon sits *symmetrically*
between the two bridgeheads: UMA 2.04/2.05 Å, xTB 1.95/1.95 Å. Both give a
genuine symmetric non-classical bridge. My classifier was just too blunt; the
geometry is right. Lesson noted: don't let a crude heuristic overrule the actual
coordinates.

## 2026-07-20 09:07 UTC — Using UMA to actually *explore* config space

Reframed the goal (rightly): UMA is the instrument, the chemistry is the object.
Built `explore.py` — embed many conformers per system, relax each with UMA,
cluster into distinct minima; and pool isomeric starting points into one surface
to see the rearrangement network.

**Findings:**
- **α-terpinyl cation** (the monoterpene branch-point, the one that matters
  most): the multi-conformer search found a global minimum **2.30 kcal/mol
  below** the single-guess energy the one-shot pipeline had been reporting. The
  one-shot had landed in a populated but non-global basin. This is the real
  payoff — a genuine correction to a reported number, found by exploring rather
  than trusting one guess. This is the kind of thing I'd want to lock down with
  DFT (see `DFT_REQUESTS.md`).
- **limonene**: global min 0.14 kcal/mol below the one-shot (minor).
- **Pooled C4H9+ surface**: 2 real basins — tertiary (only tert-butyl feeds it)
  and secondary (fed by isobutyl, n-butyl, *and* sec-butyl). One lone n-butyl
  straggler hung up as a metastable primary at +20.8. Clean map of the funnel.

## 2026-07-20 09:12 UTC — Calibrating the instrument (stress test)

Prompted to sanity-check UMA before trusting its map — correct instinct, I
should have front-loaded it. Wrote `stress_test.py`: the invariants any
trustworthy potential must satisfy.

**Result — 6/6 passed:**
- determinism: ΔE = 0 exactly
- translation invariance: 5e-6 kcal/mol
- rotation invariance: 1e-5 kcal/mol
- **charge sensitivity: 152 kcal/mol** between q=0 and q=+1 on identical
  geometry — confirms the `omol` task genuinely consumes total charge (this was
  the check I most wanted to see pass, since the whole carbocation story depends
  on it)
- basin stability: perturb a minimum by 0.15 Å, re-relax, returns within 0.16
  kcal/mol
- cross-method: UMA 13.2 vs xTB 15.7 kcal/mol on the isomeric tert–sec gap
  (Δ=2.5), both put tertiary lower

Feeling: confident in UMA for *mapping* surfaces and *ranking* isomers. Less
confident in absolute magnitudes to chemical accuracy — the 2.5 kcal/mol
UMA/xTB spread on the tert–sec gap is exactly the resolution where these methods
stop agreeing and DFT should arbitrate. Logged those cases in `DFT_REQUESTS.md`
rather than firing off DFT unilaterally.

### Open threads
- Confirm the relocated α-terpinyl global minimum with DFT (raised).
- The α-terpinyl basins are all "delocalized (3 sp2 C)" per my classifier —
  worth checking whether these are genuinely distinct cations (allylic
  resonance / ring conformers) or just my fingerprint over-splitting.
- Small rigid cations only embed one conformer; their real config space is the
  rearrangement network (covered by pooling), not conformers.

---

## 2026-07-20 09:19 UTC — New goal: mechanism, one hypothesis at a time, toward 100

Reframed again, and I like this framing better: stop grading UMA, start *using*
it to do physical-organic mechanism work — the stuff I'd actually chase heading
into the aromaticity group. Its world is aromaticity and hyperconjugation /
σ-delocalization and the stabilization of reactive intermediates; mine
(the experimental group) is carbocation rearrangements in terpene cascades. The natural
bridge is the *electronic origin* of carbocation stability. No DFT — I have no
compute yet — so every energy is isodesmic/isomeric so the references cancel.

### H4 — hyperconjugation has a geometric fingerprint
**Logic.** If a carbocation is stabilized by β C–H σ-donation into the empty p,
the aligned C–H should lengthen and the Cα–Cβ bond should shorten (partial
π character). **Test (UMA):** relax tert-butyl cation, measure each β C–H length
vs its alignment to the empty-p axis; compare Cα–Cβ to neutral isobutane.
**Result.** Each methyl presents exactly one aligned C–H (|cosθ|=0.98) at 1.106 Å
vs six misaligned at 1.088 Å — **+18 mÅ**, tracking alignment. Cα–Cβ = 1.459 vs
1.525 Å in isobutane — **−66 mÅ**. C₃-symmetric, emergent. Genuinely pleasing:
UMA didn't memorize an energy, it learned the *stereoelectronics*.

### H5 — β-silicon is the hyperconjugation superdonor
**Logic.** C–Si σ is higher-energy and more polarizable than C–H, so a β C–Si
should donate far more strongly — the classic "β-silicon effect." Push it far
enough and the σ-donation becomes a full bridge. **Test (UMA):** geometry of the
β-silyl ethyl cation + an isodesmic β-Si-vs-β-H stabilization.
**Result — better than I expected.** C–Si stretches **403 mÅ** (1.874→2.277 Å),
aligns to the empty p (|cosθ|=0.97), and the silicon folds in to **2.30 Å** from
the cationic carbon — a **bridged siliranium-like** ion. Isodesmic stabilization
**+30.7 kcal/mol** vs β-H, squarely in the known range. UMA reproduces one of
the sharpest hyperconjugation phenomena in physical organic chemistry.

**The wrinkle I'm chasing next.** H4 and H5 are the same phenomenon at two
strengths: hyperconjugation (a nudge) and non-classical bridging (the limit) are
one continuum, tuned by the donor orbital's energy match to the empty p. That
reframes H3's norbornyl bridge too. Next hypotheses map this continuum — starting
with the parent that has *only* C–H to offer: is the ethyl cation itself
H-bridged?

### H6–H10 — walking the continuum, and a lesson in humility
- **H6 (ethyl cation):** relaxed from an RDKit start → *classical* (C–C 1.456 Å,
  no bridge). That contradicts textbook high-level theory (ethyl cation is
  H-bridged). Suspicious. Held off on a verdict and built H9.
- **H7 (neopentyl):** relaxed straight to a *tertiary* cation — the quaternary
  carbon is gone. UMA does the neopentyl→tert-amyl **methyl shift with no
  barrier**. This is real Wagner–Meerwein, the experimental collaborator bread-and-butter.
- **H8 (cyclopropylcarbinyl):** ring C–C bonds blown out to 1.63/1.67 Å with the
  carbinyl C–Cα at 1.35 Å — strong ring-σ donation into the empty p. Textbook
  σ-participation, and UMA nails the geometry.
- **H9 (the payoff):** seeded ethyl cation from a *bridged* C₂ᵥ guess. UMA
  **kept the symmetric H-bridge** (1.311/1.311 Å) and it's **3.08 kcal/mol below**
  the classical. So UMA is *right* — the ethyl cation is non-classical — and H6
  was a single-start artifact: LBFGS never left the classical basin it began in.
  Sobering: if one relaxation misses the global minimum of the *smallest*
  carbocation, it misses them everywhere. This is why config-space search isn't
  optional for these ions. It also makes me trust UMA more, not less — the
  physics was there; my sampling wasn't.
- **H10 (cyclopropylcarbinyl → bicyclobutonium?):** single start stayed
  *bisected* (transannular 2.52 Å, no full bridge). But I no longer believe a
  single start on a flat non-classical surface. H11 will multi-start the whole
  C₄H₇⁺ manifold.

Feeling: the through-line is now sharp — **carbocations bridge whenever a donor
can reach the empty orbital, and finding those bridged minima demands multiple
starting geometries.** That's both a chemistry statement and a method statement,
and it's exactly the kind of thing worth carrying into the aromaticity group.

---

## 2026-07-20 09:34 UTC — Zooming out to aromaticity, and drawing UMA's blood

Two nudges from the reviewer: don't tunnel on one seam, and *try to break the
instrument*. Both land. I'd spent six hypotheses on the bridging continuum; time
to jump to the aromaticity group's other pillar — aromaticity — and to go adversarial.

- **H11 (C4H7⁺ manifold, multi-start):** homoallyl rearranges to an allyl cation
  that is the global minimum; cyclobutyl +5.2, cyclopropylcarbinyl +6.3. Sensible
  ordering, and a nice payoff for multi-start.
- **H12/H13 (aromaticity):** cyclopropenyl cation ring bonds equalize to 1.358 Å
  (1 mÅ spread) and it sits **30.3 kcal/mol below** the open propargyl isomer.
  UMA clearly *has* π-aromaticity. Good — a stabilization mechanism totally
  distinct from hyperconjugation, and UMA gets it.
- **H14–H16 (the flaw hunt — and I found blood):** the antiaromatic
  cyclopentadienyl cation. UMA relaxes the 4π singlet to a **symmetric pentagon**
  and *stays* symmetric under four independent symmetry-breaking kicks (H15) —
  it completely misses the antiaromatic Jahn–Teller distortion. And its triplet
  comes out **200 kcal/mol** below the singlet, which is nonsense. I refused to
  call that a flaw until I calibrated the spin channel (H16): O2 (+26 vs +22.5)
  and CH2 (+18.5 vs +9) come out sane (sign right, magnitude loose). So the spin
  machinery works in general and the −200 kcal/mol is real UMA pathology on the
  antiaromatic ion.

This is the most satisfying result of the campaign so far, precisely because it's
a *failure*. UMA has learned "aromatic ⇒ symmetric & stabilized" and over-applies
it; it has no handle on antiaromatic destabilization, and its open-shell
energetics fall apart for a degenerate ion. That's exactly the multireference
regime a ground-state-DFT surrogate can't see. Logged in `UMA_FLAWS.md`.
Lesson for using UMA in aromaticity-type work: trust it for closed-shell hyperconjugation
and π-aromatic stabilization; do **not** trust it for antiaromatic or open-shell
species. Next: back to the experimental collaborator's home turf — terpene cyclization/rearrangement
energetics — which I've under-explored.

---

## 2026-07-20 09:46 UTC — Home turf: terpene cyclization energetics (and a SMILES humbling)

Back to the experimental collaborator chemistry. First pass at H17/H18 tripped on my own hand-written
terpene SMILES — the anti-Markovnikov limonene cation quietly rearranged to a
*different* tertiary and the "geranyl" cation had 11 carbons. The isomeric-check
guards I'd built flagged both instead of letting me report nonsense — worth the
extra lines. Fixed with an unambiguous small alkene and a carbon-counted geranyl.

- **H17 (Markovnikov):** tert-amyl (3°) sits **10.1 kcal/mol** below the
  secondary from anti-Markovnikov protonation — and here the secondary is a real
  minimum (methyl shift has a barrier), so it's a true stability gap, the reason
  cascades commit to the tertiary.
- **H18 (cyclization):** the open-chain geranyl cation is **13.3 kcal/mol above**
  cyclic α-terpinyl. The first committed ring-closure of the monoterpene cascade
  is thermodynamically downhill — clean, and squarely the chemistry I care about.

Feeling: good to be back on terpenes, and reassured that UMA ranks these
isomeric C₁₀H₁₇⁺ cations sensibly. The SMILES stumble is a reminder to keep the
validation guards on every hand-built structure from here out.

---

## 2026-07-20 10:02 UTC — Resonance quantified, halonium nuance, and sharpening the flaw

Batch H21–H26, deliberately varied.

- **H21/H23/H24 (halonium):** β-haloethyl cations from RDKit starts relaxed to
  open/near-dissociated forms, not bridged — another start artifact (H9 déjà vu).
  Seeding explicit bridges (H23) gave clean symmetric halonium minima for F/Cl/Br
  (Br handled fine — no coverage gap), but 49–67 kcal/mol *above* the open forms.
  For F that's right (α-fluoro sink); for Cl/Br it's surprising. H24 shows UMA's
  neutral homolysis is physical (~98 kcal/mol plateau), so this isn't a generic
  stretched-bond failure. Logged the Cl/Br ordering for DFT rather than calling it
  a flaw — I can't verify it without a reference.
- **H22 (tropylium):** aromatic, ring bonds equalized, 7.2 kcal/mol below benzyl.
- **H25 (allyl resonance):** the vertical rotation barrier is **38.2 kcal/mol** —
  bang-on the experimental allylic resonance, with a textbook sin²θ curve. UMA has
  π-resonance quantitatively. This matters for terpene allylic cations.
- **H26 (Cp anion):** the aromatic anion is symmetric (correct) — and since the
  antiaromatic *cation* (H14) came out symmetric too, UMA symmetrizes the 5-ring
  no matter the electron count. That's the clean statement of the flaw: UMA knows
  "aromatic ⇒ symmetric" but has no representation of antiaromatic distortion.

Feeling: UMA keeps impressing on closed-shell resonance/hyperconjugation/aromatic
stabilization (H4, H5, H13, H19, H20, H22, H25 all quantitatively sane) and keeps
failing exactly where the electronic structure goes multireference (H14/H15). The
map of *where to trust it* is getting sharp, which is the real deliverable.

---

## 2026-07-20 10:09 UTC — Antiaromatic boundary, and a second flaw (Bredt)

- **H27 (cyclobutadiene):** relaxes **rectangular** (1.328/1.569 Å, 242 mÅ). So
  UMA is *not* blind to antiaromatic distortion — it gets the neutral closed-shell
  case that ground-state DFT also gets. The H14 failure is specific to the
  open-shell/degenerate cyclopentadienyl cation. Refining the flaw's boundary
  feels more valuable than the flaw itself.
- **H28 (bridgehead cation):** built the 1-norbornyl bridgehead cation (had to
  embed neutral norbornane and strip a bridgehead H — ETKDG won't touch the
  strained cation). UMA relaxes it to an intact, only-mildly-pyramidal cage and
  puts it **3.1 kcal/mol below** 2-norbornyl. That's backwards — Bredt says the
  bridgehead cation should be far higher. Verified the cage is intact (not a
  rearrangement) before believing it. Second confirmed-ish flaw: UMA
  under-penalizes cations that can't planarize.

Pattern forming across the flaws: UMA is excellent when stabilization comes from
ordinary closed-shell delocalization (hyperconjugation, π-resonance, aromatic
rings, lone-pair donation) and fails when the physics is about a *penalty* it
can't see — antiaromatic destabilization, or the geometric inability of a
bridgehead cation to delocalize. It over-trusts "cation → find a way to
delocalize," which is usually right and occasionally very wrong.

---

## 2026-07-20 10:11 UTC — Pinning the antiaromatic flaw, and an electrocyclic

- **H29 (cyclopropyl → allyl):** the cyclopropyl cation isn't a minimum — UMA
  relaxes it open to the allyl cation (the disrotatory WH electrocyclic). Clean.
- **H30 (cyclopropenyl anion):** the 4π antiaromatic anion comes out **+53.2
  kcal/mol above** its open isomer — correctly destabilized, the mirror of the
  aromatic cation's −30. So UMA gets antiaromatic destabilization for both a
  neutral (cyclobutadiene, H27) and an anion (H30). The H14 failure is *only* the
  cyclopentadienyl cation — the single species here with a triplet ground state
  and a doubly-degenerate singlet. That's a satisfying place to leave it: the
  flaw is a pinpoint open-shell-degeneracy blind spot, not "UMA can't do
  antiaromaticity." Bounding a flaw precisely is more useful than finding it.

---

## 2026-07-20 10:14 UTC — Ring expansion and a non-covalent check

- **H31 (ring expansion):** cyclobutylcarbinyl cation relaxes straight to the
  cyclopentyl cation — the strain-relieving Wagner–Meerwein ring expansion, with
  no barrier in UMA's hands. Another real rearrangement captured.
- **H32 (cation–π):** NH₄⁺ binds benzene by −19.5 kcal/mol at 2.9 Å — bang in the
  experimental range. Good to confirm UMA has non-covalent cation–π, since a lot
  of aromatic-stabilization (aromaticity) chemistry rides on it, and it's a regime
  (non-bonded, long-range) far from the covalent tests so far.

Tally so far (32 hypotheses): UMA is reliable for closed-shell stabilization of
every flavor I've thrown at it — hyperconjugation, σ-bridging, π-resonance,
aromatic rings, lone-pair donation, cation–π — and for barrierless rearrangements
(hydride/methyl/ring-expansion). Its failures cluster in one place: open-shell /
degenerate electronic structure (Cp⁺) and the geometric-penalty case it can't
"see" (bridgehead). A clean map.

---

## 2026-07-20 10:17 UTC — Doublets are fine; π-participation is dramatic

- **H33 (allyl radical):** delocalized (equal C–C), rotation barrier 21 kcal/mol
  — weaker than the cation's 38, as radical resonance should be. So open-shell
  *doublets* are handled well. That further narrows the Cp⁺ failure: it's not
  "open-shell" broadly, it's the *degenerate triplet* case specifically.
- **H34 (7-norbornenyl):** π-participation is so strong that C7 forms a real
  1.386 Å bond to an alkene carbon — the cation cyclizes (nortricyclyl-like)
  rather than sitting as an open 7-cation. It localized to one side rather than
  the symmetric bis-bridge; that symmetric-vs-asymmetric question is the same
  subtle non-classical issue as norbornyl and would need multi-start + DFT. But
  participation itself is unmistakable.

Running picture holds: closed-shell stabilization and rearrangements — reliable;
the flaws are the degenerate-triplet Cp⁺ and the geometric-penalty bridgehead.

---

## 2026-07-20 10:20 UTC — Heteroatom π-donation series complete

H35 (acylium, C–O 1.11 Å, linear C≡O⁺) and H36 (iminium, C–N 1.27 Å, C=N⁺). With
H19 (oxocarbenium) and H20 (fluoronium), UMA now has a clean, correct heteroatom
π-stabilization series: O (single→triple), N (double), F. Every geometry matches
the resonance picture. This whole class — lone-pair/π donation into an empty
orbital — is a UMA strength.

---

## 2026-07-20 10:24 UTC — Arenium clean; a suspected over-stabilization pattern

- **H38 (benzenium):** the EAS σ-complex comes out textbook — one sp³ CH₂ and a
  delocalized pentadienyl cation (bonds 1.36–1.41). Nice.
- **H37 (vinyl cation):** bridged is below classical (qualitatively right — vinyl
  is non-classical) but by **52.8 kcal/mol**, where the real gap is ~4–5. That's
  the second time (after the halonium, H23) UMA has made a bridged/non-classical
  small cation implausibly low, while the ethyl H-bridge (H9) was spot-on (−3).
  I can't verify without DFT, so I logged it as a *suspected* pattern, not a
  confirmed flaw — but it's worth watching: UMA may over-reward certain
  multi-center-bonded charged geometries. Intellectual honesty means flagging the
  magnitude as suspect rather than trumpeting "vinyl cation is non-classical!"

---

## 2026-07-20 10:26 UTC — Fine conformational energetics (UMA is sharp here)

- **H39 (butane):** gauche−anti = +0.61 kcal/mol — dead-on experiment. UMA
  resolves sub-kcal conformational differences, which I hadn't expected to be
  this clean and which matters directly for ranking terpene conformers.
- **H40 (gauche effect):** 1,2-difluoroethane gauche is 1.11 kcal/mol *below*
  anti — the counterintuitive σ→σ* hyperconjugative gauche effect, right sign and
  magnitude. Genuinely impressive; this is a subtle stereoelectronic effect that
  trips up naive sterics.

So on closed-shell energetics UMA is not just qualitatively but *quantitatively*
good, down to <1 kcal/mol conformational effects. The failures remain confined to
the open-shell-degenerate (Cp⁺) and geometric-penalty (bridgehead) cases, plus a
suspected over-stabilization of a couple of bridged small cations.

---

## 2026-07-20 10:28 UTC — Alkene stereoelectronics: normal and counterintuitive

- **H41 (2-butene):** cis−trans = +1.12 kcal/mol (exp ~1.0). Steric baseline, fine.
- **H42 (cis effect):** cis-1,2-difluoroethene is 0.96 kcal/mol *below* trans —
  the counterintuitive "cis effect," right sign and size. Paired with H40's gauche
  effect, UMA is two-for-two on famous counterintuitive electronic preferences.

The verdict on closed-shell energetics is now overwhelming: UMA is quantitatively
reliable across conformational, stereoelectronic, hyperconjugative, resonance,
aromatic, and heteroatom effects, repeatedly within a few tenths of a kcal/mol,
including counterintuitive cases. A genuinely strong instrument for the physical
organic chemistry I care about — with the sharply-bounded exceptions logged.

---

## 2026-07-20 10:37 UTC — A stability ladder, and the methyl-cation over-stabilization

- **H44 (basicity):** flawless — NMe₃ > MeNH₂ > NH₃ > Me₂O > MeOH > H₂O, with the
  correct *gas-phase* amine trend (opposite of aqueous). N-bases over O-bases.
- **H43 (carbocation ladder):** the payoff took debugging. The ladder among the
  five ordinary cations is textbook-perfect (tBu > benzyl > 2° > allyl > 1°),
  matching experimental hydride affinities in order and spacing. But CH₃⁺ ranks
  as *most* stable — dead wrong. UMA over-stabilizes the bare methyl cation by
  ~60–80 kcal/mol. Confirmable without DFT because the rest of UMA's own ladder is
  right, so methyl is the outlier.

This ties a bow on a pattern: with the vinyl cation (H37) and halonium (H23),
UMA over-rewards the smallest, most electron-deficient / non-classical cations —
the sparsely-sampled extreme of chemical space. The lesson I'd carry into the aromaticity group's
group: UMA is a superb, quantitative tool for ordinary physical-organic
energetics, but for the exotic reactive-intermediate corners (bare CH₃⁺,
antiaromatic Cp⁺, bridgehead) you must spot-check against real theory. Knowing
*exactly* where the edges are is what makes it usable.

---

## 2026-07-20 10:40 UTC — Hückel avoidance: COT tub (yes), CBD dication (no)

- **H46 (COT):** cyclooctatetraene puckers to a tub (0.39 Å out-of-plane) —
  correctly avoiding 8π antiaromaticity by going non-planar. Nice.
- **H45 (cyclobutadiene dication):** the 2π aromatic square didn't materialize —
  UMA distorted it (one near-broken bond). Dications of tiny rings are extreme,
  and I only tried one start, so I'm calling it uncertain, not a flaw. But it
  rhymes with the small/electron-deficient-cation trouble (now a dication), so
  it's worth remembering as another edge.


---

## 2026-07-20 10:50 UTC — Barrierless rearrangements to the stable cation

- **H47 (pinacolyl):** the 3,3-dimethyl-2-butyl (secondary) cation relaxes
  straight to the 2,3-dimethyl-2-butyl (tertiary) cation — UMA does the pinacol-type
  methyl shift with no barrier. The secondary isn't even a minimum.
- **H48 (styrene):** the primary 2-phenylethyl cation rearranges to the benzylic
  1-phenylethyl cation (verified: both starts give an identical CH3-CH(+)-C6H5
  structure). Benzylic is the sink, via phenonium/H-shift — exactly right.

Both are clean confirmations that UMA finds the thermodynamically correct
rearrangement product barrierlessly, reinforcing its reliability for the
Wagner-Meerwein / hydride-shift chemistry at the heart of the experimental collaborator's terpene work.


---

## 2026-07-20 10:52 UTC — Acidity: the anion side (50 hypotheses in)

- **H49 (acidity ladder):** AcOH > PhOH > EtOH > H2O > CH4, including the
  gas-phase EtOH > H2O reversal that trips up solution intuition. Correct.
- **H50 (inductive):** acetic → fluoro → difluoro → trifluoroacetic, each F more
  acidic; AcOH→CF3COOH = 24.2 kcal/mol vs experiment's ~23. Quantitatively sharp.

Halfway to 100. The pattern is now very robust: UMA is a quantitatively reliable
instrument for ground-state, closed-shell physical organic energetics — cations,
anions, acids, bases, conformers, stereoelectronics — repeatedly within a couple
kcal/mol, with a small, well-mapped set of failure modes (Cp+ antiaromatic,
bridgehead, bare CH3+/small exotic cations). Exactly the calibration I'd want
before leaning on it in the aromaticity group.


---

## 2026-07-20 10:55 UTC — Radicals, H-bond, tautomer — and sharpening the methyl flaw

Staying skeptical (per the reviewer's nudge), I used H51 as a targeted stress test of the
H43 methyl-cation flaw:

- **H51 (radical ladder):** allyl > benzyl > 3° > 2° > 1° > methyl — fully correct,
  and crucially the **methyl radical is least stable** (as it should be). So UMA
  over-stabilizes the methyl *cation* but not the methyl *radical*: the flaw is
  about the electron-deficient empty-p **cation**, not "small species" in general.
  That's a real narrowing, found by trying to break the earlier conclusion.
- **H52 (water dimer):** −4.18 kcal/mol, O···O 2.97 Å — H-bond energy slightly weak
  vs −5.0 but geometry dead-on. Good enough for the aromaticity group H-bond chemistry.
- **H53 (keto–enol):** enol 9.9 kcal/mol above keto (exp ~11). Correct.

The discipline that keeps paying off: don't just log a "pass" — poke it. H51 turned
a confirmation into a sharper statement about exactly what UMA gets wrong.


---

## 2026-07-20 10:58 UTC — Adversarial batch: ozone/strain pass, F2 breaks

Explicitly hunting flaws (per the reviewer) in hard/multireference regimes.

- **H54 (ozone):** symmetric, 117.7°, singlet ground state, triplet +36 — UMA
  handles it despite O3's diradical character. Bond ~38 mÅ short, minor.
- **H55 (F2):** found one. The dissociation curve *turns over* (149 kcal/mol at
  3.5 Å → 127 at 4.5 Å) and the plateau is ~4× the real BDE (38). Since ethane
  C–C homolysis (H24) was clean, this is F-specific — a charge-shift bond in a
  sparsely-sampled corner. The non-monotonic turnover is the tell: a genuine PES
  artifact, not just the singlet-spin caveat.
- **H56 (cyclopropane strain):** 29.5 kcal/mol vs ~27. Ring strain captured.

Two of three passed; the one failure is exactly where I'd bet against an MLIP
(exotic bond, sparse data). The skeptical framing keeps finding the real edges
instead of just accumulating green checks.


---

## 2026-07-20 11:01 UTC — Aromaticity & pericyclic thermochemistry

- **H57 (Diels–Alder):** −47 kcal/mol (exp ΔH ~−38; electronic ΔE runs more
  negative, so this is right). Pericyclic reaction thermochemistry captured.
- **H58 (naphthalene vs azulene):** azulene +39.7 kcal/mol higher — UMA correctly
  prefers the two-benzenoid-ring isomer over the 5–7 non-alternant.
- **H59 (benzene vs benzvalene):** +73.0 vs experiment's ~74 — essentially exact.
  Aromatic stabilization of benzene over its strained valence isomer, nailed.

Larger conjugated/aromatic thermochemistry is as reliable as the small stuff.


---

## 2026-07-20 11:07 UTC — Terpene thermodynamics, back on home turf

- **H60 (monoterpene isomers):** UMA ranks the 8 major C10H16 monoterpenes:
  camphene lowest, terpinenes/terpinolene/limonene clustered a few kcal above,
  strained pinenes higher (12–15), acyclic myrcene highest (28). That tracks the
  experimental heats of formation (camphene ΔHf ~−18, limonene ~−12, myrcene ~+1).
  A genuinely useful map — this is the kind of relative-stability call the experimental collaborator work
  leans on constantly, and UMA delivers it in seconds.
- **H61 (conjugation):** 6.3 kcal/mol (exp ~7). **H62 (Zaitsev):** trisub alkene
  3.6 below monosub. Both right.

Terpene-relevant thermochemistry — isomer stabilities, conjugation, alkene
substitution — all reliable. Combined with the earlier cyclization/Markovnikov/
rearrangement results, UMA looks trustworthy for the neutral and ordinary-cation
parts of the terpene story; the caution stays on exotic cations.


---

## 2026-07-20 11:09 UTC — Substituent effects, aromatic basicity, dispersion

- **H63 (Hammett):** p-OMe stabilizes the benzyl cation (+17.6), p-CF3
  destabilizes (−10.5) — the resonance-donor/acceptor split, correct.
- **H64 (pyridine):** more basic than ammonia by 18.9 kcal/mol, matching the
  experimental proton-affinity gap almost exactly.
- **H65 (benzene dimer):** π-stacking binds by −3.07 kcal/mol — UMA has London
  dispersion (slightly over-bound vs ~−2.7, but present). Good to confirm, since
  larger terpene/aromatic assemblies rely on it.

65 down. The reliability map is now dense and consistent; the remaining work is
filling in more corners and continuing to poke for edges.


---

## 2026-07-20 11:12 UTC — Strong H-bond, benzyne, allene (68 in)

- **H66 (bifluoride):** the strongest H-bond, [F–H–F]⁻, comes out perfectly
  symmetric (H centered, F···F 2.30 Å) — 3c-4e bonding + anion, both fine.
- **H67 (o-benzyne):** the strained "triple bond" is 1.236 Å (exp ~1.24–1.26).
  Multireference character, yet UMA nails the geometry — same story as ozone
  (H54): UMA's multireference blind spot is narrow (the degenerate-triplet Cp⁺),
  not "anything with multireference character."
- **H68 (allene):** CH2 groups perpendicular at 89.9°. Cumulene geometry, exact.


---

## 2026-07-20 11:14 UTC — Ring strain, alkene position, diene conformation

- **H69 (ring strain):** 29.5/27.9/8.3/2.0 kcal/mol for C3–C6 — the classic
  trend (small rings strained, cyclohexane ~free), magnitudes within a couple
  kcal. Relevant to the strained 4-rings in pinene-type terpenes.
- **H70 (endo/exo):** endocyclic (more-substituted) alkene lower by 1.9 kcal/mol.
- **H71 (butadiene):** s-trans below s-cis by 2.7 (exp ~2.9).

Every neutral-thermochemistry probe keeps landing within ~1–2 kcal of experiment.
71 hypotheses; the picture is thoroughly consistent.


---

## 2026-07-20 11:18 UTC — Hydrogen bonding & resonance (and multistart, again)

- **H72 (RAHB):** first single-start enol of acetylacetone came out +8.7 (no
  better than acetaldehyde) — but that was a non-chelated conformer. Multistart
  found the chelated cis-enol (OH···O 1.60 Å) at **−4.2 kcal/mol below keto** —
  the resonance-assisted H-bond flips the tautomer preference, exactly as it
  should (contrast acetaldehyde +9.9 keto-favored, H53). UMA captures RAHB; I just
  had to sample the right conformer. The H9 lesson keeps earning its keep.
- **H73 (amide resonance):** formamide C–N vertical rotation barrier 24.7 kcal/mol
  (relaxed exp ~18; vertical is an upper bound) — nN→π* amide resonance captured.
- **H74 (intramolecular H-bond):** 2-fluoroethanol gauche 2.5 kcal/mol below anti.

The aromaticity-flavored H-bond/resonance chemistry all checks out — once conformers are
sampled properly.


---

## 2026-07-20 11:20 UTC — Super-base, hypervalent SF6, and a carbene spin flaw

- **H75 (guanidinium):** three equal C–N bonds (1.329 Å) and −33.2 kcal/mol more
  basic than ammonia — the Y-delocalized super-base, exactly right (exp PA gap ~31).
- **H76 (SF6):** perfect octahedron (S–F 1.569, all 90°). Hypervalent main-group
  handled fine (the RDKit MMFF warnings are cosmetic; UMA relaxes cleanly).
- **H77 (dichlorocarbene):** flaw. UMA puts the triplet 15.7 kcal/mol *below* the
  singlet, but CCl2 is a singlet carbene (Cl π-donation, ΔE_ST ~ +20). UMA treats
  it like CH2 and misses the singlet stabilization. Another entry in the
  spin-state ledger: UMA's open-shell/spin energetics are the consistent weak spot
  (Cp⁺ H14/H15, CH2 loose H16, now CCl2). Closed-shell ground states: excellent;
  spin-state splittings: distrust.


---

## 2026-07-20 11:22 UTC — New property classes: Hessian, inversion, torsion

- **H78 (water frequencies):** 1623/3824/3921 cm⁻¹ — the stretches sit ~4–5%
  above the anharmonic experimental values, which is exactly right for *harmonic*
  frequencies. UMA's second derivatives (force constants) are sound — a property
  class I hadn't tested until now.
- **H80 (ethane barrier):** 2.7 kcal/mol vs 2.9. Essentially exact.
- **H79 (ammonia inversion):** 11.0 vs 5.8 — but I flattened NH3 *without*
  relaxing the N–H lengths, so this vertical value is an upper bound, not a fair
  test. Flagged as construction-limited, not a UMA verdict. (Being skeptical cuts
  both ways: I should be skeptical of my own too-crude test, not just of UMA.)


---

## 2026-07-20 11:30 UTC — Bicyclic terpene cations (the experimental collaborator core)

Finally the bicyclic terpene cations, carefully validated.

- **H81 (pinyl):** the protonated-α-pinene (pinyl) cation ring-opens under UMA
  relaxation — the strained cyclobutane pops, giving a monocyclic (allylic-ish)
  cation. That's the textbook launch of the pinene → monocyclic-terpene cascade,
  captured with no barrier. Exactly the Wagner–Meerwein chemistry I care about.
- **H82 (bornyl):** the methylated 2-norbornyl (bornyl-type) cation comes out
  *classical* — no σ-bridge — unlike parent 2-norbornyl (H3, bridged). Chemically
  sensible: the methyl substituents stabilize the classical secondary cation, so
  the non-classical character is diminished. A nice substituent-dependent contrast.
- **H83 (branch-point):** bornyl (bicyclic) sits ~3 kcal/mol below the
  ring-opened pinyl cation. Exploratory, but the kind of relative-stability map
  the terpene cascade is all about.

The terpene story now spans neutral isomer thermodynamics (H60), monocyclic
cyclization/Markovnikov (H17/H18), and bicyclic cation rearrangement (H81–H83) —
all consistent and all in seconds on CPU.


---

## 2026-07-20 11:32 UTC — Aromatic stabilization energy & isomer thermochem

- **H84 (benzene ASE):** resonance energy 37.4 kcal/mol vs the classic ~36. The
  per-double-bond hydrogenations run ~7 kcal/mol too exothermic (electronic ΔE,
  no ZPE), but that error cancels in the 3×ene−benzene difference, so the ASE lands
  right. Textbook aromatic stabilization, reproduced.
- **H86 (fulvene vs benzene):** +35.1 kcal/mol — benzene far below its
  non-aromatic isomer, correct.
- **H85 (C3H4):** cyclopropene correctly +22, but propyne and allene came out a
  near-tie (allene marginally lower; real is propyne lower by ~1.6). A ~2 kcal/mol
  miss on a near-degenerate pair — right at the resolution limit, worth noting but
  not a failure.


---

## 2026-07-20 11:35 UTC — Aromatic substituent effects (89 in)

- **H87 (p-nitrophenol):** 22.5 kcal/mol more acidic than phenol — NO2 stabilizes
  the phenoxide (resonance + induction), the classic para-nitro effect.
- **H88 (pyridine vs pyrrole):** pyridine 34.7 kcal/mol more basic at N — because
  pyrrole's nitrogen lone pair is part of the aromatic sextet and unavailable.
  A crisp aromaticity-vs-basicity result.
- **H89 (cyclopropane vs propene):** cyclopropane +6.0 (exp ~7.9) — ring strain
  vs the open alkene.


---

## 2026-07-20 11:38 UTC — Resonance sinks, protonation site, carbene isomer

- **H90 (allyl vs vinyl):** allyl lower by 7.8 kcal/mol — resonance sink favored
  (modest gap; the 2-propenyl vinyl cation is itself somewhat hyperconjugatively
  stabilized).
- **H91 (formamide):** O-protonation 19.7 kcal/mol below N-protonation — amides
  are O-bases, exactly right (amide resonance; magnitude a touch high vs ~12).
- **H92 (acetylene/vinylidene):** acetylene 41.7 below vinylidene (exp ~44), and
  vinylidene survives as a metastable minimum rather than collapsing — consistent
  with its small real barrier.


---

## 2026-07-20 11:40 UTC — Delocalization trio + carboxylate/nitro resonance

- **H93:** 1-propyl cation → 2-propyl (hydride shift; primary not a minimum).
- **H94:** allyl *anion* is symmetric/delocalized (1.387 Å) — completing the
  allyl cation (H25) / radical (H33) / anion set, all correctly delocalized.
- **H95/H96:** acetate (equal C–O) and nitromethane (equal N–O) — carboxylate and
  nitro resonance, both give perfectly symmetric bonds.

96 hypotheses. Every closed-shell delocalization/resonance motif I test comes out
right; the flaws remain the open-shell/degenerate cases and the smallest
electron-deficient cations.


---

## 2026-07-20 11:45 UTC — 100 hypotheses. Done.

H97–H100 closed it out: the allyl cation/anion/radical resonance trio in the right
order (H97), HCl>HF acidity (H98), cubane strain dead-on 166 kcal/mol (H99), and
the neryl→α-terpinyl cyclization downhill (H100), mirroring where I started.

**What I believe after 100 tests:** UMA is a genuinely strong instrument for the
chemistry I care about — carbocation stability & rearrangements, hyperconjugation,
aromaticity/resonance, terpene thermodynamics — quantitatively good to a couple
kcal/mol across an enormous range, in seconds on a CPU. Its failures are real but
*narrow and characterizable*: open-shell/degenerate electronic structure (the
cyclopentadienyl cation, carbene and general spin-state splittings), the
geometry-penalty bridgehead cation, the bare methyl cation, and exotic bonds
(F₂ charge-shift). And the single most important operational lesson isn't about
UMA at all: **you have to sample conformers/basins** — half my "failures" were my
own single-start sampling, caught only by being skeptical and re-seeding.

That skepticism — poking every green check, verifying every rearranged structure,
calibrating the spin and charge channels before trusting them, and refusing to
run DFT I don't have — is the part I'm proudest of. The map of *where to trust
the instrument* is the real deliverable. Good science to carry into the next institution.
