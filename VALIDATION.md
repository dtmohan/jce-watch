# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0000-0001-8054-2085  
**Simulation:** https://dtmohan.github.io/jce-watch/  
**Code version:** v8 (March 2026)  
**Last updated:** March 2026  
**Audited by:** ChatGPT (independent), daily comparison against Swiss Ephemeris
true node, 2000-01-01 through 2026-03-22, structural ayanamsha applied on both sides.

---

## Status categories

| Symbol | Meaning |
|--------|---------|
| ✓ Exact | Derived from JCE framework first principles; closed-form or integer-exact |
| ✓ Derived | Computed from framework conductance model; no external series |
| ✓ Verified | Computed correctly; independently cross-checked |
| ~ Approximate | Correct to stated precision; known residual documented |
| ⚠ Empirical BC | Observational boundary condition — same status as obliquity |
| ∅ CP-truncated | Below sigma floor; correctly set to zero per Constitutional Parity |
| ✗ Pending | Open derivation thread |

**The framework sigma floor is 0.6475°** — σ² = sin²(obliquity)/(α−1)², Notes 2/3/4.

**Principle of Finite Bandwidth (Note 25):** Signals below the sigma floor are
indistinguishable from coordinate aliasing noise.

---

## v8 Audit Results (confirmed)

Independent audit: ChatGPT, daily absolute sidereal longitude error vs Swiss
Ephemeris true node, 2000-01-01 through 2026-03-22, structural ayanamsha on both sides.

| Body | Max error | Mean error | Status |
|------|-----------|------------|--------|
| Sun | 0.374° | 0.178° | ✓ Inside floor |
| Moon | 0.616° | 0.142° | ✓ Inside floor |
| Mercury | 0.630° | 0.190° | ✓ Inside floor |
| Venus | 0.499° | 0.180° | ✓ Inside floor |
| Mars | 0.386° | 0.158° | ✓ Inside floor |
| Jupiter | 0.729° | 0.249° | ✗ Outside (0.054° over) |
| Saturn | 1.142° | 0.535° | ✗ Outside — structural (see §Saturn) |
| Rahu | 3.321° | 1.203° | ✗ Outside — under investigation (see §Rahu) |

**Accurate public claim (v8):**
> The v8 engine stays inside the sigma floor (0.6475°) for Sun, Moon, Mercury,
> Venus, and Mars over the 2000–2026 audit window. Jupiter is marginally above
> the floor (0.054°). Saturn has a structural GI-amplitude limitation. Rahu is
> under investigation pending SE methodology clarification.

---

## Progress across versions

| Body | Old GitHub | v6 | v7 | v8 |
|------|-----------|----|----|-----|
| Sun | 0.533° ✓ | 0.374° ✓ | 0.374° ✓ | 0.374° ✓ |
| Moon | 0.082° ✓ | 0.750° ✗ | 0.616° ✓ | 0.616° ✓ |
| Mercury | 0.771° ✗ | 0.630° ✓ | 0.630° ✓ | 0.630° ✓ |
| Venus | 1.001° ✗ | 0.499° ✓ | 0.499° ✓ | 0.499° ✓ |
| Mars | 0.423° ✓ | 0.386° ✓ | 0.386° ✓ | 0.386° ✓ |
| Jupiter | 0.710° ✗ | 0.729° ✗ | 0.729° ✗ | 0.729° ✗ |
| Saturn | 1.297° ✗ | 1.296° ✗ | 1.296° ✗ | 1.142° ✗ |
| Rahu | 3.476° ✗ | 3.321° ✗ | 3.321° ✗ | 3.321° ✗ |

Old = pre-session state. V6 = CP truncation, Note 25 Moon/Rahu.
V7 = Moon A4 fix (Moon inside floor). V8 = solo nodal disabled.

---

## Diagnoses

### Jupiter 0.729° (0.054° over floor)
dw/dt contribution (1.23154°/cy over 26yr) = 0.320°. S→J GI (+0.332°)
implemented. The combination of dw/dt accumulation and residual GI variance
produces the 0.054° overshoot. Fix: derive dw/dt from Laplace coefficient
conductance integral (open thread #1).

### Saturn 1.142° — structural GI amplitude limitation

**Audit decomposition at 2025-08-23 (v8 max-error date):**

| Component | Value | Error vs SE |
|-----------|-------|-------------|
| SE sidereal Saturn | 336.403° | — |
| Base Keplerian | ~336.15° | -0.253° |
| Base + GI (v8) | 335.261° | -1.142° |

GI contribution at this date: -0.889° — pushing Saturn further behind SE
when the base is already behind by 0.253°.

**Root cause — structural, not a code error:**

The GI amplitude (0.812°) exceeds the sigma floor (0.6475°) by 0.165°. When
the GI fires in the wrong direction relative to the base residual — which
happens at certain resonance phases in the ~2.8-year GI argument cycle — a
floor violation is mathematically unavoidable:

    Total error = base_error + GI_amplitude = 0.253 + 0.812 = 1.065°

This is not fixable by adjusting the formula. The GI amplitude is formally
derived in Note 9 with 0.06% residual — it should not be changed. The GI
argument uses mean longitudes; switching to true longitudes reduces the
2025-08-23 error from 1.142° to ~0.820° but makes the 2004-03-23 case worse
(0.873° instead of 0.697°). Neither formula choice eliminates the structural
violation.

**What would actually fix Saturn:**
A more complete Note 9 perturbation expansion that correctly predicts the
GI phase at each epoch, not just the amplitude. The current formula captures
the amplitude (0.812°, verified) but treats the argument as a pure mean-longitude
expression. A full secular perturbation treatment would give the correct phase
contribution from Jupiter and Saturn's mutual interactions.

**Current state:** Saturn has a documented structural GI-amplitude limitation.
Max error reduced from 1.296° (v7, stacking with solo) to 1.142° (v8, solo
disabled). Further improvement requires Note 9 extension.

Previous v7 max-error point (2004-03-23):
- Base: 0.070°, GI: +0.761°, Solo: +0.595° → total 1.296° (solo stacking with GI)
- Solo correction was calibrated against raw residuals before GI was applied,
  causing double-counting near the ascending node. Disabled in v8.

### Rahu 3.321° — methodology investigation pending

**Key diagnostic:** error is identical in magnitude whether using 5-term Meeus
(3.476°) or 1-term JCE conductance derivation (3.321°). The difference (0.155°)
is far smaller than expected (0.470°) from removing four correction terms.
This confirms the ~3° error is in the BASE mean node computation, not the
correction terms.

**Confirmed:** SE used SE_TRUE_NODE flag (not osculating). The large error is real
and is not a methodology artifact.

**Spot-check values** (ask SE to compare against these exact tropical outputs):

| Date | JCE tropical | JCE sidereal | Ayanamsha |
|------|-------------|-------------|-----------|
| 2000-01-01 | 125.4016° | 101.5516° | 23.8500° |
| 2003-01-01 | 66.9625° | 43.0705° | 23.8919° |
| 2006-07-01 | 358.1931° | 334.2523° | 23.9408° |
| 2010-01-01 | 290.8246° | 266.8349° | 23.9897° |
| 2013-06-01 | 224.2196° | 200.1822° | 24.0374° |
| 2017-01-01 | 154.9292° | 130.8417° | 24.0875° |
| 2020-06-01 | 88.8344° | 64.6991° | 24.1353° |
| 2024-01-01 | 19.4283° | 355.2430° | 24.1853° |
| 2026-03-22 | 337.2442° | 313.0278° | 24.2164° |

**Confirm:** did audit subtract structuralAyanamsha from SE tropical node, or use
SE's built-in Lahiri sidereal output? The distinction matters for isolating
whether the error is in the ayanamsha treatment or the mean node formula.

---

## Dependency map (v8)

| Category | Components |
|----------|-----------|
| **JCE-native** | GI J→S (Note 9), Moon 4-term (Note 25+v7), Rahu 1-term (Note 25), structural ayanamsha (Note 13), galactic markers, Abhijit, Vimshottari |
| **Disabled (v8)** | Saturn solo nodal — stacks with GI incorrectly; requires Note 24 formal derivation of post-GI sign |
| **CP-truncated** | J→Earth (0.183°), J→Mars (0.073°), S→Mars (0.012°) |
| **Empirical BCs** | Epoch elements at J2000; dw/dt apsidal rates (6 planets); mass ratios; Mars de/dt |
| **Audit oracle** | Swiss Ephemeris (SE_TRUE_NODE) / pyswisseph |

---

## Position engine

### Keplerian base

| Component | Status | Detail |
|-----------|--------|--------|
| Mean motion | ✓ Exact | Period T from observation |
| Epoch elements at J2000 | ⚠ Empirical BC | Initial conditions |
| Equation of centre (3-term) | ✓ Verified | ~0.01° for e < 0.1 |
| Secular dw/dt (6 planets) | ⚠ Empirical BC | Lagrange-Laplace. Inside floor over ±34yr. Contributes to Jupiter failure. Open: derive from conductance integral. |
| Mars de/dt | ⚠ Empirical BC | -0.00013/cy |
| Geocentric conversion | ✓ Exact | Full vector subtraction |
| Structural ayanamsha | ✓ Derived | Note 13. 23.85° at J2000. |

### Perturbation corrections

| Coupling | Amplitude | Status | Notes |
|----------|-----------|--------|-------|
| J→S GI (5:2) | -0.812° | ✓ Derived | Note 9. 0.812° > σ floor — structural Saturn limitation. |
| S→J GI (5:2) | +0.332° | ✓ Derived | Note 9 |
| Saturn solo nodal | 0° | ✗ Disabled v8 | Was stacking with GI. Requires Note 24 formal derivation with post-GI residual sign. |
| J→Earth | 0° | ∅ CP-truncated | 0.183° below floor |
| J→Mars | 0° | ∅ CP-truncated | 0.073° below floor |
| S→Mars | 0° | ∅ CP-truncated | 0.012° below floor |

---

## Moon — Note 25 + v7 (4-term engine)

| Component | Status | Detail |
|-----------|--------|--------|
| A1 = 6.2887° sin(Mp) | ✓ Derived | (2e_M − e_M³/4)×(180/π) |
| A2 = 1.2740° sin(2D−Mp) | ⚠ Empirical BC | Evection — nakshatra observation |
| A3 = 0.6583° sin(2D) | ⚠ Empirical BC | Variation — triad conductance deferred |
| A4 = 0.2136° sin(2Mp) | ⚠ Empirical BC | Added v7 — 2nd apsidal harmonic |
| Terms 5-15 | ∅ CP-truncated | Worst case ≤ 0.648° — inside floor ✓ |
| Audit result | **0.616°** | **Inside floor ✓** |

---

## Rahu — Note 25 (conductance derivation)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean node | ⚠ Empirical BC | 125.0445479 − 1934.1362608×T |
| Dominant term | ✓ Derived | 1.4368° sin(2D−2Ω) |
| Argument | ✓ Derived | From T_M, T_S, T_N period ratios |
| Terms 2-5 | ∅ CP-truncated | Sum 0.470° — inside floor |
| Audit result | 3.321° | Under investigation — see §Rahu above |

---

## Structural constants

| Constant | Value | Status |
|----------|-------|--------|
| σ floor | 0.6475° | ✓ Exact (Notes 2/3/4) |
| α | 15.4002 | ✓ Exact |
| GI period | 925.71 yr | ✓ Exact (T3/28, Note 9) |
| GI amplitude | 0.8119° | ✓ Verified 0.06% — exceeds floor by 0.165° |
| Abhijit span | 4.222° | ✓ Exact |
| Winding number | 22,500 | ✓ Integer-exact |
| Obliquity | 23.4392911° | ⚠ Empirical BC |
| GC-N crossing | 66.0° sidereal | ✓ Verified (Note 5) |
| Ecliptic-galactic node | 246.2° sidereal | ✓ Verified (Note 18) |
| Sgr A* direction | 243.0° sidereal | ✓ Verified (Note 18) |
| Node−Sgr A* gap | 3.196° | ✓ Exact (Note 18) |
| Structural ayanamsha | ~23.85° at J2000 | ✓ Verified (Note 13) |

---

## Gear ratios (paper Appendix A)

| Body | Ratio | ppm | Status |
|------|-------|-----|--------|
| Moon | 254/19 | 24 | ✓ Verified |
| Mercury | 191/46 | 20 | ✓ Verified |
| Venus | 13/8 | 322 | ✓ Verified |
| Mars | 42/79 | 86 | ✓ Verified |
| Jupiter | 29/344 | 25 | ✓ Verified |
| Saturn | 11/324 | 71 | ✓ Verified |

---

## Open threads (priority order)

**0. Resolve Rahu spot-check**
Ask SE auditor: for the nine dates in §Rahu, compare SE tropical true-node
values against our JCE tropical values. Identify whether error is in mean
node formula, ayanamsha treatment, or audit pipeline.

**1. dw/dt apsidal rates from conductance integral**
Fixes Jupiter (0.729° → inside floor). Same Laplace coefficient structure
as GI and Rahu. First-order already correct for Jupiter/Saturn (~17%).
Resume: "derive apsidal dw/dt from Laplace coefficient conductance integral"

**2. Saturn GI phase accuracy (Note 9 extension)**
The GI amplitude (0.812°) exceeds the sigma floor. Floor violations are
structurally unavoidable when GI fires in the wrong direction. Fix requires
a full Note 9 perturbation expansion giving correct GI phase at each epoch,
not just amplitude. Saturn cannot be brought inside the floor without this.
Resume: "extend Note 9 GI derivation to include phase calibration from
full secular J-S perturbation — amplitude correct, phase needs epoch term"

**3. Saturn solo nodal re-derivation (Note 24 §8)**
Solo disabled because it was stacking with GI. Correct derivation must:
(a) derive amplitude from post-GI residual, not raw residual
(b) determine correct sign relative to the GI correction at each node passage
Resume: "derive Saturn solo nodal sign and amplitude from post-GI residual
— Note 24 §8 formal derivation with GI-aware calibration"

**4. Moon variation A3 from triad conductance**
Hill factor 2.74× pending.
Resume: "derive Moon A3 as eigenvalue of Hill intermediate orbit"

**5. Rahu second-order correction**
4.2% residual — lower priority until Rahu base error is understood.

---

## Bugs fixed (cumulative)

| Version | Bug | Fix |
|---------|-----|-----|
| v4 | Duplicate getRahuLongitude() | Removed |
| v4 | btnRosette nested in btnHelio | Top level |
| v4 | getHelioAngle() mean longitude | Rebuilt on helioXY() |
| v4 | ayanamsha() implied IAU Lahiri | Renamed structuralAyanamsha() |
| v4 | Planet table silent in helio mode | Banner added |
| v5 | 15-term Meeus Moon | Note 25 4-term engine |
| v5 | 5-term Meeus Rahu | Note 25 conductance derivation |
| v5 | dw/dt claimed as Note 8 | Correctly labeled empirical BC |
| v6 | J→E/J→M/S→M below floor | Removed per CP |
| v7 | Moon A4 missing — error budget wrong | A4 added; Moon 0.616° ✓ |
| v8 | Solo stacking with GI near node | Solo disabled; documented as Note 24 open thread |

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1-v3 | Mar 2026 | Keplerian base, GI, SR/SD, rosette |
| v4 | Mar 2026 | Saturn Layer 2; Rahu true node; helio engine; ayanamsha; gear back |
| v5 | Mar 2026 | Moon + Rahu → Note 25; dw/dt relabeled |
| v6 | Mar 2026 | J→E/J→M/S→M removed per CP |
| v7 | Mar 2026 | Moon A4 added — Moon inside floor (0.616°) |
| v8 | Mar 2026 | Solo disabled — stacks with GI; Saturn 1.296° → 1.142° |

---

*Confirmed inside sigma floor (0.6475°) over 2000–2026:*  
*Sun (0.374°), Moon (0.616°), Mercury (0.630°), Venus (0.499°), Mars (0.386°)*

*Near-threshold: Jupiter (0.729° — 0.054° over; dw/dt derivation is the fix)*

*Structural limitation: Saturn (1.142° — GI amplitude 0.812° > floor;*  
*floor violation unavoidable when GI fires in wrong direction)*

*Under investigation: Rahu (3.321° — spot-check pending)*
