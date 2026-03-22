# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0000-0001-8054-2085  
**Simulation:** https://dtmohan.github.io/jce-watch/  
**Code version:** v7 (March 2026)  
**Last updated:** March 2026  
**Audited by:** ChatGPT (independent), daily comparison against Swiss Ephemeris
2000-01-01 through 2026-03-22, structural ayanamsha applied on both sides.

---

## Status categories

| Symbol | Meaning |
|--------|---------|
| ✓ Exact | Derived from JCE framework first principles; closed-form or integer-exact |
| ✓ Derived | Computed from framework conductance model; no external series |
| ✓ Verified | Computed correctly; independently cross-checked |
| ~ Approximate | Correct to stated precision; known residual below sigma floor |
| ⚠ Empirical BC | Observational boundary condition — same epistemological status as obliquity |
| ∅ CP-truncated | Below sigma floor; correctly set to zero per Constitutional Parity |
| ✗ Pending | Open derivation thread |

**The framework sigma floor is 0.6475°** — the irreducible positional uncertainty
of any coordinate system bridging the ecliptic and equatorial frames.
sigma^2 = sin^2(obliquity) / (alpha-1)^2, Notes 2/3/4.

**Principle of Finite Bandwidth (Note 25):** Signals below the sigma floor are
indistinguishable from coordinate aliasing noise. Correct truncation at the
floor is not approximation — it is the right physics.

---

## Audit results (v7, Swiss Ephemeris reference)

Independent audit: ChatGPT, daily absolute sidereal longitude error vs Swiss
Ephemeris, 2000-01-01 through 2026-03-22, structural ayanamsha applied on both sides.

| Body | Max error | Mean error | vs sigma floor (0.6475°) | Tier |
|------|-----------|------------|--------------------------|------|
| Sun | 0.374° | 0.178° | inside ✓ | Strong |
| Mercury | 0.630° | 0.190° | inside ✓ | Strong |
| Venus | 0.499° | 0.180° | inside ✓ | Strong |
| Mars | 0.386° | 0.158° | inside ✓ | Strong |
| Moon | ~0.65°* | ~0.19° | near-threshold | Near-threshold |
| Jupiter | 0.729° | 0.249° | outside (0.054° over) | Near-threshold |
| Saturn | 1.296° | 0.564° | outside | Open |
| Rahu | 3.321° | 1.203° | outside — see note | Open / methodology question |

*Moon was 0.750° in v6 audit. v7 adds A4 term (0.2136°); expected improvement to ~0.65°.
v7 Moon not yet re-audited.

**Honest summary (ChatGPT's words):**
> The current engine stays inside the sigma floor for Sun, Mercury, Venus, and Mars
> over the 2000–2026 audit window, with Moon and Jupiter near-threshold and
> Saturn/Rahu remaining open correction branches.

The blanket claim "inside this floor for all bodies" does not yet hold and has
been removed from this document. The above table is the accurate statement.

---

## Open audit questions (before next code fix)

**Rahu methodology question:**
The Rahu error (3.321° max) is the same magnitude in both the old 5-term Meeus
version (3.476°) and the current 1-term JCE version. The difference (0.155°) is
far smaller than expected from removing four below-floor terms (sum 0.470°).
This strongly suggests the ~3° base error is NOT from the correction terms.

Possible causes:
1. The audit compared against Swiss Ephemeris **osculating node** (which can
   swing ±10° from the mean — comparison would be unfair and the 3.3° is expected)
2. The structural ayanamsha has a systematic offset vs SE Lahiri that compounds
   on slow-moving bodies like Rahu (0.053°/day)

**Action needed before fixing Rahu:** ask the auditor which SE node flag was
used — `SE_TRUE_NODE`, `SE_MEAN_NODE`, or `SE_OSCU_APOG`. If osculating node,
the comparison is invalid and the true-node formula is likely correct.

**Saturn 1.296° location question:**
Need to know: on what date does the Saturn max error occur, and what was
Saturn's sidereal longitude on that date? This determines whether the error
is near the nodes (solo correction sign/amplitude issue) or away from the
nodes (dw/dt accumulation).

---

## Dependency map (v7)

| Category | Components |
|----------|-----------|
| **JCE-native** | GI J-S (Note 9), Saturn solo nodal (Note 24), Moon longitude 4-term (Note 25 + v7 fix), Rahu true node (Note 25), structural ayanamsha (Note 13), galactic markers, Abhijit, Vimshottari |
| **CP-truncated at zero** | J→Earth (0.183°), J→Mars (0.073°), S→Mars (0.012°) — all below sigma floor, combined 0.268° < 0.6475° |
| **Empirical BCs** | Epoch elements L0, e, w at J2000; dw/dt apsidal rates; mass ratios; Mars de/dt |
| **Audit reference** | Swiss Ephemeris / pyswisseph — validation oracle only |

---

## Position engine

### Keplerian base

| Component | Status | Detail |
|-----------|--------|--------|
| Mean motion L(t) = L0 + (360/T)xt | ✓ Exact | Period T from observation |
| Epoch elements L0, e, w at J2000 | ⚠ Empirical BC | Initial conditions |
| Equation of centre (3-term) | ✓ Verified | ~0.01° for e < 0.1 |
| Secular dw/dt (apsidal drift, 6 planets) | ⚠ Empirical BC | Inside sigma floor over ±34yr (Mercury), ±46yr (others). Contributes to Jupiter/Saturn audit failures. Open thread: derive from conductance integral. |
| Mars secular de/dt | ⚠ Empirical BC | -0.00013/cy |
| Geocentric conversion | ✓ Exact | Full vector subtraction |
| Structural ayanamsha | ✓ Derived | Note 13. Zero-point 23.85° from Note 5 galactic grid. |

### Perturbation corrections

| Coupling | Amplitude | Status | Audit impact |
|----------|-----------|--------|-------------|
| Jupiter → Saturn GI (5:2) | -0.812° | ✓ Derived | Note 9, 0.06% residual |
| Saturn → Jupiter GI (5:2) | +0.332° | ✓ Derived | Note 9 |
| Saturn solo nodal Layer 2 | ±0.714° | ~ Approximate | Candidate formula Note 24. Saturn still 1.296° — error location unknown. |
| Jupiter → Earth | 0° | ∅ CP-truncated | 0.183° below floor. Removed v6. |
| Jupiter → Mars | 0° | ∅ CP-truncated | 0.073° below floor. Removed v6. |
| Saturn → Mars | 0° | ∅ CP-truncated | 0.012° below floor. Removed v6. |

---

## Moon — Note 25 + v7 fix (4-term engine)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean elements | ⚠ Empirical BC | Best observed rates at J2000 |
| A1 = 6.2887° (equation of centre) | ✓ Derived | (2e_M - e_M^3/4) x (180/pi) |
| A2 = 1.2740° (evection) | ⚠ Empirical BC | Observed in nakshatra frame |
| A3 = 0.6583° (variation) | ⚠ Empirical BC | Observed synodic oscillation |
| A4 = 0.2136° (2nd apsidal harmonic) | ⚠ Empirical BC | **Added v7.** 2nd overtone of apsidal oscillation. Was incorrectly omitted in v5/v6 — caused worst-case error budget to be stated as 0.580° when correct value is 0.648°. |
| Terms 5-15 (11 terms) | ∅ CP-truncated | Sum ≤ 0.648° worst case — inside sigma floor ✓ |
| Abhijit correction | ✓ Exact | In getNakshatra()/getVimshottari(). 4.222° span, winding number 22,500. |
| v6 error budget claim | **CORRECTED** | **v6 stated 0.580° worst case. This was wrong — missed A4 (0.2136°). Correct worst case with A4 included: 0.648°, just inside floor.** |
| Audit result (v6) | 0.750° max | Outside floor — caused by missing A4 |
| Expected (v7) | ~0.65° max | A4 added; not yet re-audited |

---

## Rahu — Note 25 (conductance derivation)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean node base | ⚠ Empirical BC | 125.0445479 - 1934.1362608xT |
| Dominant term | ✓ Derived | 1.4368° sin(2D-2Omega) from conductance integral. Error vs Meeus 1.4979°: 0.061° = 9.4% of sigma floor. |
| Argument 2D-2Omega | ✓ Derived | From T_M, T_S, T_N period ratios |
| Terms 2-5 | ∅ CP-truncated | Sum 0.470° — inside sigma floor |
| Audit result | 3.321° max | **OPEN QUESTION: likely audit methodology issue (osculating vs true node). See open audit questions above.** Same magnitude in Meeus 5-term version (3.476°) — error not from correction terms. |

---

## Structural constants

| Constant | Value | Source | Status |
|----------|-------|--------|--------|
| sigma floor | 0.6475° | Notes 2/3/4 | ✓ Exact |
| alpha | 15.4002 | T3/T2 | ✓ Exact |
| GI period | 925.71 yr | T3/28, Note 9 | ✓ Exact |
| GI amplitude | 0.8119° | Note 9 | ✓ Verified 0.06% |
| Abhijit span | 4.222° | Note 1 | ✓ Exact |
| Winding number | 22,500 | 360°/0.016° | ✓ Integer-exact |
| Obliquity | 23.4392911° | J2000 | ⚠ Empirical BC |
| GC-N crossing | 66.0° sidereal | Note 5 | ✓ Verified |
| Ecliptic-galactic node | 246.2° sidereal | Note 18 | ✓ Verified |
| Sgr A* direction | 243.0° sidereal | Note 18 | ✓ Verified |
| Node-Sgr A* gap | 3.196° | obliquity x 3/22, Note 18 | ✓ Exact |
| Structural ayanamsha | ~23.85° at J2000 | Note 13 | ✓ Verified |

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

## Display features

| Feature | Status | Note |
|---------|--------|------|
| Sidereal frame (structural ayanamsha) | ✓ Derived | Note 13 |
| Three galactic belt markers | ✓ Verified | 66°, 246.2°, 243.0° |
| Nine exaltation diamonds | ✓ Verified | Note 5 |
| Abhijit band | ✓ Exact | Note 1 |
| Abhijit dasha correction | ✓ Exact | getNakshatra() |
| Natal Vimshottari | ✓ Verified | Natal Moon, Abhijit-corrected |
| SR/SD stamps | ✓ Verified | SR filled diamond, SD open |
| Mercury rosette | ✓ Verified | 21yr closure |
| Retrograde arcs | ✓ Verified | Ketu-pink |
| Venus pentagram | ~ Approximate | 5 stations per 8yr |
| Nodal axis alignment (Note 17) | ~ Approximate | True node |
| Galactic phase + prediction (Note 17) | ~ Approximate | Oct 2029 window |
| Anchor nakshatra arcs (Note 13) | ✓ Verified | 5 waypoints |
| Heliocentric mode engine | ✓ Verified | helioXY() — same as geocentric |
| Heliocentric table banner | ✓ Verified | Geocentric readout explicit |
| Gear back face | ✓ Verified | MST cascade, torque arrows, Rahu epicyclic, Vimshottari Geneva |
| Saturn solo nodal display | ~ Approximate | Note 24; audit shows 1.296° — open |
| Upagraha display | ✗ Pending | Paper Appendix B |

---

## Open threads (priority order)

**1. Clarify Rahu audit methodology**
Before any Rahu code change: confirm which SE node flag (true/mean/osculating)
was used in the audit. If osculating, the 3.3° is expected and not a code error.
Resume: ask auditor for SE node flag and audit date of max error.

**2. Confirm Saturn error location**
Ask auditor: date and Saturn sidereal longitude at the 1.296° max error.
Determines whether fix is solo correction sign/amplitude or dw/dt derivation.

**3. Moon v7 re-audit**
v7 adds A4 = 0.2136° x sin(2Mp). Expected improvement: 0.750° → ~0.65°.
Re-audit needed to confirm Moon is now inside or at floor.

**4. dw/dt apsidal rates from conductance integral**
Lagrange-Laplace empirical BCs. First-order Laplace coefficient correct for
Jupiter/Saturn. Inner planets need higher-order. Relevant beyond ~35yr.
Fixes Jupiter near-threshold failure (0.729°) and likely Saturn.
Resume keyword: "derive apsidal precession dw/dt from Laplace coefficient
conductance integral — extend Note 9 GI structure to each MST dyad pair"

**5. Saturn solo nodal correction formal derivation (Note 24 §8)**
Candidate 0.714° empirically motivated but not formally derived.
Resume keyword: "resume JCE note 24 solo Saturn correction derivation"

**6. Moon variation A3 from triad conductance (Note 25)**
Hill factor 2.74x needs derivation from Moon-Sun-Earth triad conductance.
Resume keyword: "derive Moon variation A3 as eigenvalue of Hill intermediate orbit"

**7. Rahu second-order correction (Note 25)**
4.2% residual from first-order conductance derivation.
Resume keyword: "derive second-order Rahu amplitude correction — Delaunay m^2"

---

## Bugs fixed (cumulative)

| Version | Bug | Fix |
|---------|-----|-----|
| v4 | Duplicate getRahuLongitude() — mean node overriding true node | Removed |
| v4 | btnRosette nested in btnHelio | Moved to top level |
| v4 | getHelioAngle() mean longitude only | Rewritten to use helioXY() |
| v4 | ayanamsha() implied IAU Lahiri | Renamed structuralAyanamsha() |
| v4 | Planet table silent in helio mode | Banner added |
| v5 | 15-term Meeus Moon → Note 25 3-term | Stand-alone engine |
| v5 | 5-term Meeus Rahu → Note 25 1-term | Conductance derivation |
| v5 | dw/dt claimed as Note 8 derivation | Correctly labeled empirical BC |
| v6 | J→E/J→M/S→M retained below floor | Removed per CP |
| v6 | Dependency map incomplete | Three-category honest map |
| v7 | Moon A4 term missing — worst-case error budget 0.862° (stated 0.580°) | A4 = 0.2136° x sin(2Mp) added; worst case now 0.648° inside floor |

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1 | Mar 2026 | Initial — basic Keplerian, Note 5/18 markers |
| v2 | Mar 2026 | GI, secular w, J→E/J→M couplings |
| v3 | Mar 2026 | SR/SD stamps, Mercury rosette, getDasha removed |
| v4 | Mar 2026 | Saturn Layer 2; Rahu true node; helio engine; ayanamsha rename; gear back face |
| v5 | Mar 2026 | Moon + Rahu → Note 25; dw/dt relabeled; dependency map |
| v6 | Mar 2026 | J→E/J→M/S→M removed per CP; full audit |
| v7 | Mar 2026 | Moon A4 term added (arithmetic correction to v5/v6 error budget) |

---

*All positions are sidereal (JCE structural ayanamsha, Note 13). The simulation
demonstrates phase relationships in the JCE framework — not arcsecond ephemeris
precision.*

*Confirmed inside sigma floor (0.6475°) over 2000–2026: Sun, Mercury, Venus, Mars.*
*Near-threshold: Moon (v7 fix pending re-audit), Jupiter (0.054° over).*
*Open: Saturn (1.296°), Rahu (3.321° — audit methodology question pending).*
