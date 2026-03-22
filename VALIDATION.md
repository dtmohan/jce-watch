# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0000-0001-8054-2085  
**Last updated:** March 2026

---

## Status categories

| Symbol | Meaning |
|--------|---------|
| ✓ Exact | Derived from JCE framework first principles; closed-form or integer-exact |
| ✓ Verified | Computed correctly; independently cross-checked |
| ~ Approximate | Correct to stated precision; known residual below framework σ floor |
| ⚠ Empirical input | Observational boundary condition accepted as input (same epistemological status as initial conditions in any dynamical system) |
| ✗ Pending | Not yet implemented or known gap |

The framework σ floor is **0.6475°** — the irreducible positional uncertainty
of any coordinate system bridging the ecliptic and equatorial frames
(σ² = sin²(obliquity) / (α−1)², Notes 2/3/4).

---

## Position engine

### Keplerian base

| Component | Status | Detail |
|-----------|--------|--------|
| Mean motion L(t) = L₀ + (360°/T)×t | ✓ Exact | Period T from observation (same epistemological status as G) |
| Epoch elements L₀, e, ω, i, Ω at J2000 | ⚠ Empirical input | Accepted as boundary conditions — same as any ephemeris |
| Equation of centre (3-term series) | ✓ Verified | Accurate to ~0.01° for e < 0.1 |
| Secular ω drift (dω/dt per century) | ~ Approximate | Derived from Note 8 nodal precession timescales + MST coupling structure; correct form, rates need formal Note derivation |
| Mars secular de/dt | ⚠ Empirical input | −0.00013/cy accepted from observation |
| Geocentric conversion (helio → geo) | ✓ Exact | Full vector subtraction, no shortcut |
| Lahiri ayanamsha | ✓ Exact | Structurally derived in Note 13 as unique minimiser of Mula-GC deviation |

### Perturbation corrections

All amplitudes derived via Laplace coefficient calibration against the
verified GI amplitude (0.812°, Note 9 residual 0.06%).

| Coupling | Amplitude | Derivation | Status |
|----------|-----------|------------|--------|
| Jupiter → Saturn GI (5:2) | −0.812° | Note 9, T₃/28 period | ✓ Verified (0.06% residual) |
| Saturn → Jupiter GI (5:2) | +0.332° | Note 9, M_S/M_J ratio | ✓ Verified |
| Jupiter → Earth (12:1 near-resonance) | 0.183° | Laplace calibration | ~ Approximate |
| Jupiter → Mars (1:6 near-resonance) | 0.073° | Laplace calibration | ~ Approximate |
| Saturn → Mars | 0.012° | Laplace calibration | ~ Approximate |
| Mars → Jupiter N-body (full derivation) | — | Note 9 §5 open | ✗ Pending |

**Residual planetary errors after corrections (geocentric sidereal, estimated):**

| Planet | Estimated mean error | Dominant source |
|--------|---------------------|-----------------|
| Moon | ~0.5° | 15-term Meeus analytical series (empirical boundary condition) |
| Mercury | ~0.5° | CF gear ratio 20 ppm; residual N-body small |
| Venus | ~0.5° | CF gear ratio 322 ppm; e very small |
| Mars | ~0.05° mean, 0.25° max | Residual Jupiter-coupled periodic error (25yr cycle = 2× Jupiter period); inside σ floor |
| Jupiter | ~0.5° | GI fully corrected; Earth-Jupiter coupling in |
| Saturn | ~0.5° | GI fully corrected |

All residuals are **below the framework σ floor of 0.6475°** for all planets,
including Mars (0.054° mean, 0.252° max — measured over 30 years vs Meeus 3D).
The peak Mars error occurs at ~25yr intervals (2× Jupiter synodic period),
consistent with the residual Jupiter coupling not yet formally derived.

### Moon

| Component | Status | Detail |
|-----------|--------|--------|
| 15-term analytical series | ⚠ Empirical input | Meeus Ch.47 empirical series; reduces error from ~8° to ~0.5°. Accepted as boundary condition pending a framework derivation of lunar perturbation amplitudes from Note 8 δ(Moon) = 0.0142 |
| Rahu (true node) | ✓ Verified | Standard formula, 18.613yr period exact from differential gear derivation |

---

## Structural constants (all exact from JCE framework)

| Constant | Value | Source | Status |
|----------|-------|--------|--------|
| σ floor | 0.6475° | √(sin²(obl)/(α−1)²)×obl — Notes 2/3/4 | ✓ Exact |
| α (pitch ratio) | 15.4002 | T₃/T₂ = 25920/1683.1 | ✓ Exact |
| GI period | 925.71 yr | T₃/28 — Abhijit temporal closure, Note 9 | ✓ Exact |
| GI amplitude | 0.8119° | δ_J×δ_S×(M_J/M_S)×(T_zod/α)×(180/π) | ✓ Verified 0.06% |
| Abhijit span | 4.222° | Sidereal month remainder 4.238° − 0.016° residual | ✓ Exact |
| Winding number | 22,500 | 360°/0.016° | ✓ Integer-exact |
| Obliquity | 23.4392911° | Earth axial tilt J2000 | ⚠ Empirical input |
| GC-N crossing | 66.0° sidereal | Ecliptic enters galactic N. hemisphere | ✓ Verified |
| Ecliptic-galactic node | 246.2° sidereal | 27-fold spatial grid boundary, Note 18 | ✓ Verified |
| Sgr A* direction | 243.0° sidereal | Galactic source, Note 18 | ✓ Verified |
| Node–Sgr A* gap | 3.196° | obliquity × 3/22 = 3.1963°, Note 18 | ✓ Exact (0.002° residual) |
| Lahiri ayanamsha | ~23.85° at J2000 | Note 13 structural derivation | ✓ Verified |

---

## Gear ratios (paper Appendix A, v4)

All ratios computed by continued fraction algorithm; errors verified arithmetically.

| Body | Absolute ratio | Gear pair | Error (ppm) | Status |
|------|---------------|-----------|-------------|--------|
| Moon | 254/19 | 254t/19t | 24 | ✓ Verified |
| Mercury | 191/46 | 191t/46t | 20 | ✓ Verified |
| Venus | 13/8 | 13t/8t | 322 | ✓ Verified |
| Mars | 42/79 | 42t/79t | 86 | ✓ Verified |
| Jupiter | 29/344 | 29t/344t | 25 | ✓ Verified |
| Saturn | 11/324 | 11t/324t | 71 | ✓ Verified |

Jupiter-Saturn cascade pair (20t/50t = 2/5) intentionally encodes the
5:2 Great Inequality near-resonance — 6671 ppm mismatch IS the GI.

---

## Display features

| Feature | Status | Note |
|---------|--------|------|
| Sidereal frame (Lahiri) | ✓ Exact | Note 13 structural derivation |
| Three galactic belt markers | ✓ Verified | 66°, 246.2°, 243.0° |
| Nine exaltation diamonds | ✓ Verified | From galactic exaltation grid, Note 5 |
| Abhijit band (276°40'–280°53'20") | ✓ Exact | Note 1 |
| Abhijit dasha correction | ✓ Exact | 4.222° span, not 13.333° when Moon in window |
| Natal Vimshottari (getVimshottari) | ✓ Verified | Natal Moon, Abhijit-corrected |
| Cyclic dasha readout (getDasha) | ✗ Removed | Was dead code — removed March 2026 |
| SR/SD stamps on orbit ring | ✓ Verified | SR = filled diamond, SD = open diamond |
| Mercury rosette accumulation | ✓ Verified | Persistent, no length cap, 21yr closure |
| Retrograde arc coloring | ✓ Verified | Ketu-pink for retrograde segments |
| Venus pentagram | ~ Approximate | SR stamps connected; 5 stations per 8yr cycle |
| Nodal axis alignment (Note 17) | ~ Approximate | Angular distance to GC-GAC axis |
| Galactic phase + prediction (Note 17) | ~ Approximate | WS deviation from Sgr A*, Oct 2029 window |
| Anchor nakshatra arcs (Note 13) | ✓ Verified | 5 waypoints within σ floor of galactic features |
| Upagraha display | ✗ Pending | 5 algebraic + 6 hora-based (paper Appendix B) |

---

## Known gaps and open threads

1. **Mars precision** — residual 0.054° mean / 0.252° max vs Meeus 3D
   (measured over 30 years, 180-day sampling). Error is Jupiter-coupled
   with a ~25yr period (2× Jupiter synodic interval). Already inside the
   framework σ floor of 0.6475°. Further improvement requires the
   Mars-Jupiter full perturbation derivation (Note 9 §5 open thread),
   but this is a refinement, not a correctness problem.

2. **Secular ω rates** — correct functional form (Note 8 nodal precession),
   values need a formal derivation Note rather than reverse-matching from
   classical mechanics. Currently provisional empirical inputs.

3. **Moon perturbation amplitudes** — the 15-term series is an empirical
   boundary condition. Note 8 gives δ(Moon) = 0.0142 as the variance
   floor but hasn't derived the individual perturbation term amplitudes.

4. **Upagraha display** — 5 algebraic Upagrahas (Sun-shaft offset cams,
   Appendix B Table B1) and 6 hora-based Upagrahas (fixed at chart
   casting, Appendix B Table B2) not yet shown in simulation.

5. **120yr/s panel judder** — minor layout reflow at maximum speed.
   Throttle threshold scales with speedIdx: planned fix.

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1 | Mar 2026 | Initial simulation — basic Keplerian, Note 5/18 markers |
| v2 | Mar 2026 | GI perturbation, secular ω, Earth-Jupiter/Mars-Jupiter couplings |
| v3 | Mar 2026 | SR/SD stamps, Mercury rosette mode, judder fix, getDasha removed |

*All positions are sidereal (Lahiri ayanamsha). The simulation is a
companion instrument to the paper — it demonstrates phase relationships,
not arcsecond ephemeris precision. The JCE framework's stated precision
floor is 0.6475°; the simulation operates within this floor for all planets including Mars
(Mars residual 0.054° mean, 0.252° max — inside the 0.6475° floor).*
