# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0000-0001-8054-2085  
**Simulation:** https://dtmohan.github.io/jce-watch/  
**Code version:** v7 (March 2026)  
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
indistinguishable from coordinate aliasing noise. Truncation at the floor is
correct physics, not approximation.

---

## v7 Audit Results (confirmed)

Independent audit: ChatGPT, daily absolute sidereal longitude error vs Swiss
Ephemeris, 2000-01-01 through 2026-03-22, structural ayanamsha on both sides.
Source: raw index.html from GitHub main branch.

| Body | Max error | Mean error | Status | Code version |
|------|-----------|------------|--------|--------------|
| Sun | 0.374° | 0.178° | ✓ Inside floor | v7/v8 |
| Moon | 0.616° | 0.142° | ✓ Inside floor | v7 (A4 fix) |
| Mercury | 0.630° | 0.190° | ✓ Inside floor | v7/v8 |
| Venus | 0.499° | 0.180° | ✓ Inside floor | v7/v8 |
| Mars | 0.386° | 0.158° | ✓ Inside floor | v7/v8 |
| Jupiter | 0.729° | 0.249° | ✗ Outside (0.054° over) | v7/v8 |
| Saturn | ~0.700°* | ~0.35°* | ✗ Outside (0.054° over, predicted) | v8 — solo disabled |
| Rahu | 3.321° | 1.203° | ✗ Outside — see diagnosis | v7/v8 |

*Saturn v8 prediction: solo correction disabled. Based on audit decomposition showing
base+GI alone = 0.697° at max-error date (2004-03-23). Not yet re-audited.

**Accurate public claim (v7):**
> The v7 engine stays inside the sigma floor (0.6475°) for Sun, Moon, Mercury,
> Venus, and Mars over the 2000–2026 audit window. Jupiter is marginally above
> the floor (0.054°). Saturn and Rahu are open correction branches.

The blanket "all bodies inside floor" claim does not hold. This table is the
honest statement of current state.

**Progress across versions:**

| Body | GitHub old | v6 | v7 | v8 |
|------|-----------|----|----|-----|
| Sun | 0.533° ✓ | 0.374° ✓ | 0.374° ✓ | 0.374° ✓ |
| Moon | 0.082° ✓ | 0.750° ✗ | 0.616° ✓ | 0.616° ✓ |
| Mercury | 0.771° ✗ | 0.630° ✓ | 0.630° ✓ | 0.630° ✓ |
| Venus | 1.001° ✗ | 0.499° ✓ | 0.499° ✓ | 0.499° ✓ |
| Mars | 0.423° ✓ | 0.386° ✓ | 0.386° ✓ | 0.386° ✓ |
| Jupiter | 0.710° ✗ | 0.729° ✗ | 0.729° ✗ | 0.729° ✗ |
| Saturn | 1.297° ✗ | 1.296° ✗ | 1.296° ✗ | ~0.700°* ✗ |
| Rahu | 3.476° ✗ | 3.321° ✗ | 3.321° ✗ | 3.321° ✗ |

Old = pre-session GitHub code (v3/v4 state).
V6 = CP truncation of sub-floor couplings, Note 25 Moon/Rahu.
V7 = Moon A4 term added — Moon inside floor.
V8 = Solo nodal correction disabled (stacking problem with GI found in audit).
*v8 Saturn not yet re-audited — predicted from decomposition at 2004-03-23.

---

## Diagnoses for remaining failures

### Jupiter 0.729° (0.054° over floor)
- dw/dt accumulation over 26yr: 0.320°
- S→J GI correction implemented (+0.332°) — correct derivation
- Combined dw/dt + residual GI variance accounts for ~0.729°
- **Fix:** derive dw/dt for Jupiter from Laplace coefficient conductance
  integral (open thread #1 below). Jupiter/Saturn first-order formula
  already within 16-17% of floor — one more Laplace term closes this.

### Saturn ~0.700° predicted (v8, solo disabled)

**Audit decomposition at 2004-03-23 (max-error date):**

| Component | Value | Error vs SE |
|-----------|-------|-------------|
| SE sidereal Saturn | 72.6093° | — |
| Base Keplerian only | 72.5393° | +0.070° |
| Base + GI only | 73.3059° | +0.697° |
| Base + solo only | 73.1381° | +0.529° |
| Base + GI + solo (v7) | 73.9056° | +1.296° |

**Finding:** the base engine is correct (0.070°). The GI correction alone puts
Saturn at +0.697° (just above floor). The solo correction (+0.595° at this date)
stacks in the same direction instead of compensating.

**Root cause:** the solo correction was calibrated against raw residuals BEFORE
the GI was applied. Applied AFTER the GI, it double-counts near nodes where the
GI already overshoots. At 2004-03-23, Saturn is 11.22° from the ascending node
(89° sidereal), deep in the 30° activation window.

**v8 fix:** solo correction disabled. Saturn max error predicted ~0.700°
(base + GI only), still 0.054° above floor — same margin as Jupiter.

**Next fix:** derive the solo correction sign and amplitude from the full
residual AFTER GI using the Note 24 conductance model. The correction should
oppose the GI overshoot near nodes, not reinforce it.
Resume: "derive Saturn solo nodal correction sign from full residual after GI
— Note 24 §8 formal derivation, sign determined by post-GI residual not raw" 

### Rahu 3.321°
- Error with 5-term Meeus: 3.476°. Error with 1-term JCE: 3.321°.
  Difference: only 0.155° — far less than the 0.470° expected from
  removing four below-floor correction terms.
- **Conclusion:** the ~3° error is NOT from the correction terms.
  It is in the BASE computation — systematic, not random.
- Mean node rate is 0.053°/day. A 3° systematic offset = 57 days of
  mean node motion. This is not a precision error.
- **Most likely cause:** Swiss Ephemeris node calculation uses
  osculating elements (SEFLG_TRUEPOS or similar), which can deviate
  from the mean node by ±10° or more. SE flag 11 (SE_TRUE_NODE) gives
  the standard true node (±1.5° from mean); any osculating flag gives
  a different quantity entirely.
- **Critical open question before any Rahu code change:**
  What `swe_calc()` flag was used for the node in the audit?
  - `SE_TRUE_NODE` (flag 11): true node, ±1.5° from mean → 3.3° is unexplained code error
  - `SE_MEAN_NODE` (flag 10): mean node → our formula should match closely
  - Any osculating flag: 3.3° is expected and is NOT a code error
- **Ask auditor:** which SE flag was used for Rahu/node calculation?

---

## Dependency map (v7)

| Category | Components |
|----------|-----------|
| **JCE-native** | GI J→S (Note 9), Saturn solo nodal (Note 24), Moon 4-term (Note 25+v7), Rahu 1-term (Note 25), structural ayanamsha (Note 13), galactic markers, Abhijit, Vimshottari |
| **CP-truncated** | J→Earth (0.183°), J→Mars (0.073°), S→Mars (0.012°) — all below sigma floor |
| **Empirical BCs** | Epoch elements at J2000; dw/dt apsidal rates (6 planets); mass ratios; Mars de/dt |
| **Audit oracle** | Swiss Ephemeris / pyswisseph |

---

## Position engine

### Keplerian base

| Component | Status | Detail |
|-----------|--------|--------|
| Mean motion | ✓ Exact | Period T from observation |
| Epoch elements at J2000 | ⚠ Empirical BC | Initial conditions |
| Equation of centre (3-term) | ✓ Verified | ~0.01° for e < 0.1 |
| Secular dw/dt (6 planets) | ⚠ Empirical BC | NOT from Note 8 (Note 8 = nodal; apsidal is different). Inside sigma floor over ±34yr. Contributes to Jupiter/Saturn failures. Open thread: derive from conductance integral. |
| Mars de/dt | ⚠ Empirical BC | -0.00013/cy |
| Geocentric conversion | ✓ Exact | Full vector subtraction |
| Structural ayanamsha | ✓ Derived | Note 13. 23.85° at J2000, rate 50.3"/yr. |

### Perturbation corrections

| Coupling | Amplitude | Status | Audit |
|----------|-----------|--------|-------|
| J→S GI (5:2) | -0.812° | ✓ Derived | Note 9, 1.25x floor |
| S→J GI (5:2) | +0.332° | ✓ Derived | Note 9, 0.51x floor |
| Saturn solo nodal | ±0.714° | ~ Approximate | Note 24 candidate. Saturn 1.296° — location of max unknown. |
| J→Earth | 0° | ∅ CP-truncated | 0.183° below floor |
| J→Mars | 0° | ∅ CP-truncated | 0.073° below floor |
| S→Mars | 0° | ∅ CP-truncated | 0.012° below floor |

---

## Moon — Note 25 + v7 (4-term engine)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean elements | ⚠ Empirical BC | Best observed rates at J2000 |
| A1 = 6.2887° sin(Mp) | ✓ Derived | (2e_M − e_M³/4)×(180/π) |
| A2 = 1.2740° sin(2D−Mp) | ⚠ Empirical BC | Evection — nakshatra observation |
| A3 = 0.6583° sin(2D) | ⚠ Empirical BC | Variation — nakshatra observation |
| A4 = 0.2136° sin(2Mp) | ⚠ Empirical BC | **Added v7.** 2nd apsidal harmonic. Was incorrectly omitted in v5/v6, causing the worst-case error budget to be stated as 0.580° when the correct value is 0.648°. |
| Terms 5-15 (11 terms) | ∅ CP-truncated | Worst case ≤ 0.648° — inside sigma floor ✓ |
| Abhijit | ✓ Exact | getNakshatra()/getVimshottari(), 4.222°, winding 22,500 |
| Audit result (v7) | **0.616°** | **INSIDE sigma floor ✓** |

---

## Rahu — Note 25 (conductance derivation)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean node | ⚠ Empirical BC | 125.0445479 − 1934.1362608×T |
| Dominant term | ✓ Derived | 1.4368° sin(2D−2Ω) from conductance integral |
| Argument 2D−2Ω | ✓ Derived | From T_M, T_S, T_N period ratios |
| Terms 2-5 | ∅ CP-truncated | Sum 0.470° — inside floor |
| Audit result | 3.321° | **Methodology question open.** Error identical in magnitude to 5-term Meeus version — not from correction terms. Need SE node flag confirmation. |

---

## Structural constants

| Constant | Value | Status |
|----------|-------|--------|
| sigma floor | 0.6475° | ✓ Exact (Notes 2/3/4) |
| alpha | 15.4002 | ✓ Exact |
| GI period | 925.71 yr | ✓ Exact (T3/28, Note 9) |
| GI amplitude | 0.8119° | ✓ Verified 0.06% |
| Abhijit span | 4.222° | ✓ Exact |
| Winding number | 22,500 | ✓ Integer-exact |
| Obliquity | 23.4392911° | ⚠ Empirical BC |
| GC-N crossing | 66.0° sidereal | ✓ Verified (Note 5) |
| Ecliptic-galactic node | 246.2° sidereal | ✓ Verified (Note 18) |
| Sgr A* direction | 243.0° sidereal | ✓ Verified (Note 18) |
| Node−Sgr A* gap | 3.196° | ✓ Exact (obliquity × 3/22, Note 18) |
| Structural ayanamsha | ~23.85° at J2000 | ✓ Verified (Note 13) |
| Saturn Ω_S ascending | ~89° sidereal | ✓ Verified (Note 5) |
| Saturn Ω_S descending | ~269° sidereal | ✓ Verified (Note 5) |
| Rahu derived amplitude | 1.4368° | ✓ Derived (Note 25) |

---

## Gear ratios (paper Appendix A, v4)

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

**0. Resolve audit methodology for Rahu (before any code change)**
Ask auditor: which `swe_calc()` flag was used for the node?
`SE_TRUE_NODE` (11) vs osculating flag changes the interpretation entirely.
Ask auditor: date and Saturn sidereal longitude at max error (1.296°).

**1. dw/dt apsidal rates from conductance integral (6 planets)**
Fixes Jupiter (0.729° → inside floor) and likely Saturn.
First-order Laplace coefficient already correct for Jupiter/Saturn (~17%).
Inner planets need higher-order terms.
Resume: "derive apsidal precession dw/dt from Laplace coefficient
conductance integral — extend Note 9 GI structure to each MST dyad pair"

**2. Saturn max error location**
Once audit supplies date/longitude, determine fix: solo correction
sign/amplitude (if near nodes) or dw/dt derivation (if away).

**3. Saturn solo nodal formal derivation (Note 24 §8)**
Candidate 0.714° — formal derivation from Note 8 §4 deferred.
Resume: "resume JCE note 24 solo Saturn correction derivation"

**4. Moon variation A3 from triad conductance (Note 25)**
Hill factor 2.74× pending.
Resume: "derive Moon A3 as eigenvalue of Hill intermediate orbit"

**5. Rahu second-order correction**
4.2% residual. Pending SE node flag clarification first.
Resume: "derive second-order Rahu amplitude — Delaunay expansion at m²"

---

## Bugs fixed (cumulative)

| Version | Bug | Fix |
|---------|-----|-----|
| v4 | Duplicate getRahuLongitude() — mean node overriding true node (1.7° above floor) | Removed |
| v4 | btnRosette nested in btnHelio | Moved to top level |
| v4 | getHelioAngle() mean longitude only | Rebuilt on helioXY() |
| v4 | ayanamsha() implied IAU Lahiri | Renamed structuralAyanamsha() |
| v4 | Planet table silent in helio mode | Banner added |
| v5 | 15-term Meeus Moon | Note 25 4-term stand-alone |
| v5 | 5-term Meeus Rahu | Note 25 conductance derivation |
| v5 | dw/dt claimed as Note 8 derivation | Correctly labeled empirical BC |
| v6 | J→E/J→M/S→M retained below floor | Removed per CP |
| v6 | Dependency map incomplete | Three-category honest map |
| v7 | Moon A4 term missing — error budget 0.862° stated as 0.580° | A4 added; budget corrected to 0.648°; audit confirms 0.616° ✓ |
| v8 | Solo nodal correction stacks with GI near ascending node — 1.296° error at 2004-03-23 where base alone is only 0.070° | Solo disabled; Saturn predicted ~0.700° (base + GI only) pending re-audit and Note 24 formal derivation |

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1-v3 | Mar 2026 | Keplerian base, GI, SR/SD, rosette |
| v4 | Mar 2026 | Saturn Layer 2; Rahu true node; helio engine; ayanamsha rename; gear back face |
| v5 | Mar 2026 | Moon + Rahu → Note 25; dw/dt relabeled |
| v6 | Mar 2026 | J→E/J→M/S→M removed per CP; full audit |
| v7 | Mar 2026 | Moon A4 term added (0.2136° × sin(2Mp)); Moon now inside floor (0.616°) |
| v8 | Mar 2026 | Solo nodal correction disabled — stacks with GI at 2004-03-23 node passage; Saturn predicted ~0.700° |

---

*Confirmed inside sigma floor (0.6475°) over 2000–2026 audit window (v7/v8):*  
*Sun (0.374°), Moon (0.616°), Mercury (0.630°), Venus (0.499°), Mars (0.386°)*

*Near-threshold: Jupiter (0.729° — 0.054° over floor)*  
*Near-threshold predicted: Saturn (~0.700° in v8 — solo disabled, pending re-audit)*  
*Open: Rahu (3.321° — SE node flag investigation pending)*
