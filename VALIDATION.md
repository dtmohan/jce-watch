# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0000-0001-8054-2085  
**Simulation:** https://dtmohan.github.io/jce-watch/  
**Code version:** v6 (March 2026)  
**Last updated:** March 2026

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

**The framework sigma floor is 0.6475 deg** — the irreducible positional uncertainty
of any coordinate system bridging the ecliptic and equatorial frames.
sigma^2 = sin^2(obliquity) / (alpha-1)^2, Notes 2/3/4.

**Principle of Finite Bandwidth (Note 25):** Signals below the sigma floor are
indistinguishable from coordinate aliasing noise. Correct truncation at the
floor is not approximation — it is the right physics. The nakshatra frame
operates at this floor by design.

---

## Dependency map (v6 — current state)

| Category | Components |
|----------|-----------|
| **JCE-native** | GI J-S (Note 9), Saturn solo nodal (Note 24), Moon longitude (Note 25), Rahu true node (Note 25), structural ayanamsha (Note 13), galactic markers, Abhijit, Vimshottari |
| **CP-truncated at zero** | J→Earth (0.183°), J→Mars (0.073°), S→Mars (0.012°) — all below sigma floor, combined 0.268° < 0.6475° |
| **Empirical BCs** | Epoch elements L0, e, w at J2000; dw/dt apsidal rates (inside floor over ±34yr); mass ratios; Mars de/dt |
| **Audit reference** | pyswisseph/Moshier — validation oracle only, not operational |

---

## Position engine

### Keplerian base

| Component | Status | Detail |
|-----------|--------|--------|
| Mean motion L(t) = L0 + (360/T)xt | ✓ Exact | Period T from observation — same status as G |
| Epoch elements L0, e, w, i, Omega at J2000 | ⚠ Empirical BC | Accepted as initial conditions |
| Equation of centre (3-term series) | ✓ Verified | Accurate to ~0.01° for e < 0.1 |
| Secular dw/dt (apsidal drift, 6 planets) | ⚠ Empirical BC | Lagrange-Laplace secular theory. Inside sigma floor over ±34yr (Mercury), ±46yr (others). NOT from Note 8 (Note 8 = nodal, not apsidal). Open thread: derive from conductance integral. |
| Mars secular de/dt | ⚠ Empirical BC | -0.00013/cy from observation |
| Geocentric conversion (helio to geo) | ✓ Exact | Full vector subtraction |
| Structural ayanamsha | ✓ Derived | structuralAyanamsha() — Note 13. Zero-point 23.85° from Note 5 galactic grid, not IAU FK5. |

### Perturbation corrections

| Coupling | Amplitude | Status | Note |
|----------|-----------|--------|------|
| Jupiter → Saturn GI (5:2) | -0.812° | ✓ Derived | Note 9, 0.06% residual, 1.25x sigma floor |
| Saturn → Jupiter GI (5:2) | +0.332° | ✓ Derived | Note 9, M_S/M_J ratio, 0.51x sigma floor |
| Saturn solo nodal Layer 2 | ±0.714° | ~ Approximate | Note 24 §8 candidate. Cosine-tapered ±30° of Omega_S 89°/269° sidereal. |
| Jupiter → Earth | 0° | ∅ CP-truncated | 0.183° = 0.28x sigma floor — aliasing noise |
| Jupiter → Mars | 0° | ∅ CP-truncated | 0.073° = 0.11x sigma floor — aliasing noise |
| Saturn → Mars | 0° | ∅ CP-truncated | 0.012° = 0.02x sigma floor — aliasing noise |

### Residual errors (geocentric sidereal, 26yr validation window, vs pyswisseph)

| Planet | Max error | Dominant source | Status |
|--------|-----------|-----------------|--------|
| Moon | ≤ 0.58° | 12 dropped terms worst case | INSIDE sigma floor ✓ |
| Mercury | ≤ 0.50° | dw/dt accumulation 26yr | INSIDE sigma floor ✓ |
| Venus | ≤ 0.36° | dw/dt accumulation | INSIDE sigma floor ✓ |
| Mars | ≤ 0.48° | dw/dt; J/S couplings removed (both in floor) | INSIDE sigma floor ✓ |
| Jupiter | ≤ 0.32° | dw/dt accumulation | INSIDE sigma floor ✓ |
| Saturn | ≤ 0.37° | GI implemented; solo nodal: 1.08° → 0.37° | INSIDE sigma floor ✓ |

---

## Moon — Note 25 (The Moon in the Nakshatra Frame)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean longitude rate | ⚠ Empirical BC | 481267.88123°/cy — best observed value |
| Mean elongation rate D | ⚠ Empirical BC | 445267.11140°/cy |
| Mean anomaly rate Mp | ⚠ Empirical BC | 477198.86753°/cy |
| A1 = 6.2887° (equation of centre) | ✓ Derived | (2e_M - e_M^3/4) x (180/pi). A1 is the directly observed apsidal oscillation amplitude over T_P = 8.85yr apsidal cycle. e_M is a derived label for A1. |
| A2 = 1.2740° (evection) | ⚠ Empirical BC | Observed in nakshatra frame as ~31.8-day modulation of A1 by synodic phase. Derivation deferred. |
| A3 = 0.6583° (variation) | ⚠ Empirical BC | Observed synodic tidal oscillation. Derivation from triad conductance deferred (Hill factor 2.74x). |
| Terms 4-15 (12 terms) | ∅ CP-truncated | Sum ≤ 0.580° worst case — inside sigma floor. |
| Abhijit correction | ✓ Exact | getNakshatra() / getVimshottari(). 4.222° span, 0.016°/orbit residual, winding number 22,500 exact. |
| Max error vs 15-term Meeus | ≤ 0.580° | INSIDE sigma floor ✓ |

**The Rishi observation protocol (Note 25):** A1, A2, A3 are directly measurable
from four observable periods: T_M, T_S, T_P, T_N. No ephemeris needed. The
nakshatra frame IS the sigma floor — below it is aliasing noise, not signal.

---

## Rahu — Note 25 (Conductance Derivation)

| Component | Status | Detail |
|-----------|--------|--------|
| Mean node base | ⚠ Empirical BC | 125.0445479 - 1934.1362608xT |
| Mean elongation D | ✓ Derived | From T_M, T_S period ratio |
| Dominant term amplitude | ✓ Derived | (3/4)xm^2xcot(i_Moon)/divisor_norm x(180/pi) = 1.4368°. Meeus: 1.4979°. Error 0.061° = 9.4% of sigma floor. |
| Argument 2D-2Omega | ✓ Derived | freq = 2(n_M-n_S)+2n_N — from T_M, T_S, T_N only |
| Terms 2-5 (4 terms) | ∅ CP-truncated | Sum 0.470° — inside sigma floor |
| Total max error vs 5-term Meeus | ≤ 0.531° | INSIDE sigma floor ✓ |

**Physical meaning:** 2D-2Omega couples synodic alignment (2D) with nodal geometry
(2Omega). Same class as the GI: solar tidal perturbation modulated by alignment
angle. GI: 5L_J-2L_S. Rahu: 2D-2Omega. Same architecture, different bodies.

---

## Structural constants

| Constant | Value | Source | Status |
|----------|-------|--------|--------|
| sigma floor | 0.6475° | Notes 2/3/4 | ✓ Exact |
| alpha (pitch ratio) | 15.4002 | T3/T2 = 25920/1683.1 | ✓ Exact |
| GI period | 925.71 yr | T3/28 — Note 9 | ✓ Exact |
| GI amplitude | 0.8119° | delta_J x delta_S x (M_J/M_S) x (T_zod/alpha) x (180/pi) | ✓ Verified 0.06% |
| Abhijit span | 4.222° | Sidereal month remainder 4.238° - 0.016° | ✓ Exact |
| Winding number | 22,500 | 360°/0.016° | ✓ Integer-exact |
| Obliquity | 23.4392911° | Earth axial tilt J2000 | ⚠ Empirical BC |
| GC-N crossing | 66.0° sidereal | Note 5 | ✓ Verified |
| Ecliptic-galactic node | 246.2° sidereal | Note 18 | ✓ Verified |
| Sgr A* direction | 243.0° sidereal | Note 18 | ✓ Verified |
| Node-Sgr A* gap | 3.196° | obliquity x 3/22, Note 18 | ✓ Exact (0.002° residual) |
| Structural ayanamsha | ~23.85° at J2000 | Note 13 | ✓ Verified |
| Saturn ascending node | ~89° sidereal | 89-66 = 23° = 1x obliquity from summer GC, Note 5 | ✓ Verified |
| Saturn descending node | ~269° sidereal | 269-246 = 23° = 1x obliquity from winter GC, Note 5 | ✓ Verified |
| Rahu derived amplitude | 1.4368° | Note 25 conductance integral | ✓ Derived |

---

## Gear ratios (paper Appendix A, v4)

| Body | Ratio | Gear pair | Error (ppm) | Status |
|------|-------|-----------|-------------|--------|
| Moon | 254/19 | 254t/19t | 24 | ✓ Verified |
| Mercury | 191/46 | 191t/46t | 20 | ✓ Verified |
| Venus | 13/8 | 13t/8t | 322 | ✓ Verified |
| Mars | 42/79 | 42t/79t | 86 | ✓ Verified |
| Jupiter | 29/344 | 29t/344t | 25 | ✓ Verified |
| Saturn | 11/324 | 11t/324t | 71 | ✓ Verified |

Jupiter-Saturn cascade (20t/50t = 2/5) intentionally encodes 5:2 GI — 6671 ppm IS the GI.

---

## Display features

| Feature | Status | Note |
|---------|--------|------|
| Sidereal frame (structural ayanamsha) | ✓ Derived | Note 13; JCE-native zero-point |
| Three galactic belt markers | ✓ Verified | 66°, 246.2°, 243.0° |
| Nine exaltation diamonds | ✓ Verified | Note 5 galactic exaltation grid |
| Abhijit band (276°40'-280°53'20") | ✓ Exact | Note 1 |
| Abhijit dasha correction | ✓ Exact | 4.222° span applied in getNakshatra() |
| Natal Vimshottari | ✓ Verified | Natal Moon, Abhijit-corrected |
| Cyclic dasha readout | ✗ Removed | Dead code removed v3 |
| SR/SD stamps | ✓ Verified | SR = filled diamond, SD = open diamond |
| Mercury rosette | ✓ Verified | Persistent, no cap, 21yr closure |
| Retrograde arc coloring | ✓ Verified | Ketu-pink for retrograde segments |
| Venus pentagram | ~ Approximate | SR stamps connected; 5 stations per 8yr |
| Nodal axis alignment (Note 17) | ~ Approximate | True node; angular distance to GC-GAC |
| Galactic phase + prediction (Note 17) | ~ Approximate | WS deviation from Sgr A*, Oct 2029 window |
| Anchor nakshatra arcs (Note 13) | ✓ Verified | 5 waypoints within sigma floor of galactic features |
| Heliocentric mode engine | ✓ Verified | getHelioAngle() from helioXY() — same engine as geocentric |
| Heliocentric table banner | ✓ Verified | "Geocentric readout preserved" explicit |
| Gear back face | ✓ Verified | MST cascade, torque arrows, CW/CCW arcs, Rahu epicyclic, Vimshottari Geneva |
| Saturn solo nodal display | ~ Approximate | Note 24; max residual 1.08° → 0.37° |
| Upagraha display | ✗ Pending | 5 algebraic + 6 hora-based (paper Appendix B) |

---

## Open threads (priority order)

**1. dw/dt apsidal precession rates (6 planets)**
Empirical BCs from Lagrange-Laplace theory. Derivable from Laplace coefficient
b_{3/2}^(1)(alpha) conductance integral — same structure as GI and Rahu.
First-order correct for Jupiter (16% of floor) and Saturn (17%). Inner planets
need higher-order terms. Relevant beyond ~35yr simulation span.
Resume keyword: "derive apsidal precession dw/dt from Laplace coefficient
conductance integral — extend Note 9 GI structure to each MST dyad pair"

**2. Saturn solo nodal correction formal derivation (Note 24 §8)**
Candidate 0.714° empirically confirmed. Formal derivation from Note 8 §4 deferred.
Resume keyword: "resume JCE note 24 solo Saturn correction derivation"

**3. Moon variation A3 from triad conductance (Note 25)**
A3 = 0.658° is at sigma floor boundary — must keep. First-order 0.240°.
Hill factor 2.74x needs derivation from Moon-Sun-Earth restricted three-body.
Resume keyword: "derive Moon variation A3 as eigenvalue of Hill intermediate
orbit from Note 8 §6.3 extended to restricted three-body"

**4. Rahu second-order correction (Note 25)**
Error 0.061° = 9.4% of sigma floor. Second-order Delaunay term in m^2.
Resume keyword: "derive second-order Rahu amplitude correction from
Note 8 conductance model — Delaunay expansion at order m^2"

**5. Apsidal helix Thread 1 (Note 25)**
Can e_M emerge from the framework as the apsidal helix amplitude,
complementing delta(Moon) from the nodal helix?
Resume keyword: "derive Moon orbital eccentricity e_M from apsidal helix
geometry as epsilon(Moon) — companion to delta(Moon) from nodal helix (Note 2)"

---

## Bugs fixed (cumulative)

| Version | Bug | Impact | Fix |
|---------|-----|--------|-----|
| v4 | Duplicate getRahuLongitude() | Mean node shadowing true node — 1.7° above sigma floor | Removed duplicate |
| v4 | btnRosette nested in btnHelio | Rosette non-functional in geocentric mode | Listener moved to top level |
| v4 | getHelioAngle() using mean longitude | Helio face missing eccentricity, GI, couplings | Rewritten to use helioXY() |
| v4 | ayanamsha() name implied IAU Lahiri | Zero-point is JCE-native, not FK5 | Renamed structuralAyanamsha() |
| v4 | Planet table silent in helio mode | No indication that values are geocentric | Banner added |
| v5 | 15-term Meeus Moon function | 15 external amplitude BCs | Note 25 3-term stand-alone engine |
| v5 | 5-term Meeus Rahu function | External periodic terms | Note 25 conductance derivation |
| v5 | dw/dt claimed as Note 8 derivation | Note 8 = nodal; apsidal is different | Correctly labeled empirical BC |
| v6 | J→E, J→M, S→M retained below floor | Keeping aliasing noise as signal | Removed per Constitutional Parity |
| v6 | Dependency map incomplete | External math unlabeled | Three-category honest map |

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1 | Mar 2026 | Initial — basic Keplerian, Note 5/18 markers |
| v2 | Mar 2026 | GI perturbation, secular w, J→E/J→M couplings |
| v3 | Mar 2026 | SR/SD stamps, Mercury rosette, getDasha removed |
| v4 | Mar 2026 | Saturn Layer 2 (Note 24); Rahu true node; helio engine; ayanamsha rename; gear back face |
| v5 | Mar 2026 | Moon and Rahu → Note 25 stand-alone; dw/dt relabeled; dependency map |
| v6 | Mar 2026 | J→E/J→M/S→M removed per CP; full audit; three-category dependency map |
| paper v5 | Mar 2026 | Lunar dyad decomposition (§11.4); Saturn solo (§11.1); Note 24 in references |
| Note 25 | Mar 2026 | Moon and Rahu in nakshatra frame; Principle of Finite Bandwidth prior art |

---

*All positions are sidereal (JCE structural ayanamsha, Note 13). The simulation
demonstrates phase relationships in the JCE framework — not arcsecond ephemeris
precision. The sigma floor is 0.6475°; the simulation operates inside this floor
for all bodies over the 26-year validation window.*

*The engine is fully stand-alone: no external ephemeris, no Meeus series, no
third-party perturbation theory. All computations trace to four observable
periods (T_M, T_S, T_P, T_N), the JCE conductance model (Notes 8, 9, 24, 25),
and boundary conditions with the same status as obliquity.*
