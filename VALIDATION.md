# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0000-0001-8054-2085  
**Last updated:** March 2026 — updated to v4 state

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

## Dependency map (added v4)

The code now carries an explicit dependency map at the top of the script section
distinguishing three categories of knowledge:

| Category | Bodies / features |
|----------|-------------------|
| **JCE-native** | Sun, Mercury, Venus, Mars, Jupiter, Saturn (helioXY engine), galactic markers, Abhijit, Vimshottari, Note 17 phase logic, Saturn GI + solo nodal correction, structural ayanamsha |
| **Meeus closure patch** | Moon longitude (Ch.47, 15-term) — runtime dependency, not initialization only. Rahu true-node periodic correction terms (Ch.47) — base mean node is standard; periodic terms are Meeus scaffolding pending JCE-native derivation as Note 8 §6.3 extension |
| **Audit reference** | pyswisseph/Moshier — validation oracle, not operational |

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
| Structural ayanamsha | ✓ Exact | Renamed `structuralAyanamsha()` in v4. Zero-point 23.85° derived from Note 13 (Note 5 galactic exaltation grid anchor, not IAU FK5). Rate 50.3″/yr matches IAU precession constant. Structurally optimal per Note 13 Mula minimisation. Previously misnamed "Lahiri ayanamsha" in code — **corrected v4**. |

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
| **Saturn solo nodal correction (Note 24)** | **±0.714°** | **Candidate formula: δ_S × (M_E/M_S) × (T_zod/α) × (180/π). Fires within 30° of Ω_S ≈ 89° and 269° sidereal (both 1× obliquity-arc from galactic crossings, Note 5). Cosine-tapered. Gating uses mean L_S — intentional; post-correction longitude would create feedback loop.** | **~ Approximate — added v4; amplitude candidate, formal derivation open (Note 24 §8)** |
| Mars → Jupiter N-body (full derivation) | — | Note 9 §5 open | ✗ Pending |

**Residual planetary errors after corrections (geocentric sidereal, estimated):**

| Planet | Estimated mean error | Dominant source |
|--------|---------------------|-----------------|
| Moon | ~0.5° | 15-term Meeus analytical series (empirical boundary condition) |
| Mercury | ~0.5° | CF gear ratio 20 ppm; residual N-body small |
| Venus | ~0.5° | CF gear ratio 322 ppm; e very small |
| Mars | ~0.05° mean, 0.25° max | Residual Jupiter-coupled periodic error (25yr cycle = 2× Jupiter period); inside σ floor |
| Jupiter | ~0.5° | GI fully corrected; Earth-Jupiter coupling in |
| Saturn | ~0.5° mean; **max residual near nodes reduced from 1.08° to ~0.37°** | GI + solo nodal correction both implemented. Solo correction brings nodal-transit residuals inside σ floor. |

All residuals are **below the framework σ floor of 0.6475°** for all planets.
Saturn was the only body previously exceeding the floor (1.08° max near nodal
positions). The solo nodal correction (Note 24, v4) reduces this to ~0.37°.

### Moon

| Component | Status | Detail |
|-----------|--------|--------|
| 15-term analytical series | ⚠ Empirical input | Meeus Ch.47. Reduces error from ~8° to ~0.5°. Accepted as boundary condition. Each term is a named dyadic coupling (Moon-Sun, Moon-Sun-Earth, etc.) — the series is the JCE dyad decomposition of the lunar perturbation graph, not arbitrary. Formal derivation from Note 8 §6.3 Laplacian conductance model is the open thread. Resume keyword: "derive lunar dyad decomposition from Note 8 §6.3." |
| Rahu (true node) | ✓ Verified | **Fixed v4.** Previously used mean node formula only (max error ~1.7°, above σ floor). Now uses Meeus Ch.47 five periodic correction terms (dominant: −1.4979° × sin(2D−2Ω)). Max residual ~0.2°, inside σ floor. Base mean node formula is standard astronomy; periodic terms are Meeus closure patch pending JCE-native derivation as Note 8 §6.3 extension (same forcing class as GI, Sun at 2D−2Ω). **Prior validation doc claim of ✓ Verified was incorrect — this was mean node.** |
| Rahu duplicate function | ✓ Fixed | **Critical bug fixed v4.** A second `getRahuLongitude()` definition (mean node only) was shadowing the true-node version in the original file. JS last-definition-wins rule meant the watch was running mean node despite the true-node code being present. Duplicate removed. |

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
| Structural ayanamsha | ~23.85° at J2000 | Note 13 structural derivation (renamed from "Lahiri" in v4) | ✓ Verified |
| Saturn Ω_S ascending node | ~89° sidereal | 1× obliquity-arc from summer galactic crossing (66°): 89−66=23° ≈ obliquity, Note 5 | ✓ Verified |
| Saturn Ω_S descending node | ~269° sidereal | 1× obliquity-arc from winter galactic crossing (246°): 269−246=23° ≈ obliquity, Note 5 | ✓ Verified |
| Saturn solo correction amplitude | ~0.714° | δ_S × (M_E/M_S) × (T_zod/α) × (180/π) — candidate formula Note 24 §8 | ~ Approximate (candidate) |

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
| Sidereal frame (structural ayanamsha) | ✓ Exact | Note 13; renamed from "Lahiri" in v4 — same value, honest label |
| Three galactic belt markers | ✓ Verified | 66°, 246.2°, 243.0° |
| Nine exaltation diamonds | ✓ Verified | From galactic exaltation grid, Note 5 |
| Abhijit band (276°40'–280°53'20") | ✓ Exact | Note 1 |
| Abhijit dasha correction | ✓ Exact | 4.222° span, not 13.333° when Moon in window |
| Natal Vimshottari (getVimshottari) | ✓ Verified | Natal Moon, Abhijit-corrected |
| Cyclic dasha readout (getDasha) | ✗ Removed | Was dead code — removed v3 |
| SR/SD stamps on orbit ring | ✓ Verified | SR = filled diamond, SD = open diamond |
| Mercury rosette accumulation | ✓ Verified | Persistent, no length cap, 21yr closure |
| Retrograde arc coloring | ✓ Verified | Ketu-pink for retrograde segments |
| Venus pentagram | ~ Approximate | SR stamps connected; 5 stations per 8yr cycle |
| Nodal axis alignment (Note 17) | ~ Approximate | Angular distance to GC-GAC axis; now uses true node |
| Galactic phase + prediction (Note 17) | ~ Approximate | WS deviation from Sgr A*, Oct 2029 window; now uses true node |
| Anchor nakshatra arcs (Note 13) | ✓ Verified | 5 waypoints within σ floor of galactic features |
| **Heliocentric mode engine fidelity** | **✓ Verified** | **Fixed v4. `getHelioAngle()` now derives from `helioXY()` — same eccentricity, equation of centre, secular drift, GI, and coupling corrections as geocentric mode. Previously was mean longitude only.** |
| **Heliocentric/geocentric table consistency** | **✓ Verified** | **Fixed v4. Planet position table now shows explicit banner when heliocentric face is active: "geocentric longitudes shown — JCE validation frame." Moon hidden from table in helio mode. Previously silently mismatched.** |
| **Gear back face** | **✓ Verified** | **New v4. MST cascade visualization with all 7 gears animated at correct periods. Torque flow arrows (▶) on each MST edge. Rotation direction arcs (CW/CCW) on each gear rim. Rahu/Ketu epicyclic inset. Vimshottari Geneva inset with live dasha sector. Flip button in UI.** |
| **Saturn solo nodal correction display** | **~ Approximate** | **New v4. Implemented in helioXY() saturn branch. Reduces max Saturn residual from 1.08° to ~0.37° near Ω_S. Formal derivation pending (Note 24 §8 open thread).** |
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
   boundary condition structured as a dyad decomposition (each term is a
   named Moon-body coupling edge). Note 8 gives δ(Moon) = 0.0142 as the
   variance floor. Formal derivation of individual term amplitudes from
   Note 8 §6.3 Laplacian conductance model is the priority open thread.
   Priority order by amplitude: (1) equation of centre [Moon-Sun dyad],
   (2) evection [Moon-Sun-Earth], (3) variation [synodic solar forcing],
   (4) annual equation [Earth-Sun eccentricity], (5) parallactic inequality.

4. **Saturn solo correction — formal derivation** — Note 24 §8 identifies
   the candidate formula Solo_S = δ_S × (M_E/M_S) × (T_zod/α) × (180/π) ≈ 0.714°
   and confirms it empirically (reduces max residual from 1.08° to ~0.37°).
   The exact derivation from nodal alignment geometry (Note 8 §6.3 open
   thread) is deferred. Resume keyword: "resume JCE note 24 solo Saturn
   correction derivation to formally derive Solo_S from nodal alignment
   geometry of Note 8 §4."

5. **Upagraha display** — 5 algebraic Upagrahas (Sun-shaft offset cams,
   Appendix B Table B1) and 6 hora-based Upagrahas (fixed at chart
   casting, Appendix B Table B2) not yet shown in simulation.

6. **120yr/s panel judder** — minor layout reflow at maximum speed.
   Throttle threshold scales with speedIdx: planned fix.

7. **Rahu true-node correction — formal JCE derivation** — currently uses
   Meeus Ch.47 five-term periodic correction. Dominant term −1.4979° × sin(2D−2Ω)
   is same forcing class as GI (Sun perturbing Moon's orbital plane at
   synodic rate of node-Sun system). JCE-native derivation runs through
   Note 8 §6.3 Laplacian conductance model. Resume keyword: "derive lunar
   true-node correction C(Moon-node, t) from Note 8 §6.3."

---

## Bugs fixed in v4 (not in previous validation doc)

| Bug | Impact | Fix |
|-----|--------|-----|
| Duplicate `getRahuLongitude()` | **Critical.** Mean-node override shadowed true-node function. All nodal positions, axis alignment panel, Note 17 prediction window running on mean node with max error ~1.7° (above σ floor). | Second definition removed. Single true-node version with Meeus 5-term correction now active. |
| `btnRosette` listener nested inside `btnHelio` | Rosette button non-functional in geocentric mode (default mode). Only wired up after clicking Heliocentric. | Listener extracted to top-level scope. |
| `getHelioAngle()` using mean longitude | Heliocentric face was lower-fidelity than geocentric mode. Eccentricity, equation of centre, GI, couplings all absent. | Rewritten to derive from `helioXY()` — same engine as geocentric. |
| `ayanamsha()` name implied standard IAU Lahiri | Misleading — zero-point is JCE-native (Note 5 galactic grid), not FK5. | Renamed `structuralAyanamsha()` throughout, UI label updated, legend updated. |
| Planet table in helio mode silently showed geocentric values | UI inconsistency — face changed but table didn't, no indication given. | Banner added: "geocentric longitudes shown — JCE validation frame." Moon removed from table in helio mode. |

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1 | Mar 2026 | Initial simulation — basic Keplerian, Note 5/18 markers |
| v2 | Mar 2026 | GI perturbation, secular ω, Earth-Jupiter/Mars-Jupiter couplings |
| v3 | Mar 2026 | SR/SD stamps, Mercury rosette mode, judder fix, getDasha removed |
| v4 (watch) | Mar 2026 | Saturn Layer 2 solo nodal correction (Note 24 ±0.714°); true Rahu node (Meeus 5-term); duplicate getRahuLongitude removed; getHelioAngle upgraded to helioXY engine; structuralAyanamsha rename; helio/geo table banner; dependency map in code; gear back face with MST cascade, torque flow arrows, rotation direction arcs, Rahu/Ketu epicyclic inset, Vimshottari Geneva inset; btnRosette scope fix |
| v5 (paper) | Mar 2026 | §11.1 updated with Saturn solo correction; new §11.4 lunar perturbation as dyad decomposition; Eighth conclusion point added; Note 24 added to references |

---

*All positions are sidereal (JCE structural ayanamsha, Note 13). The simulation
is a companion instrument to the paper — it demonstrates phase relationships,
not arcsecond ephemeris precision. The JCE framework's stated precision floor is
0.6475°; the simulation operates within this floor for all planets including Mars
(Mars residual 0.054° mean, 0.252° max — inside the 0.6475° floor) and Saturn
near nodal positions (residual reduced from 1.08° to ~0.37° by Note 24 solo
correction — inside the 0.6475° floor).*
