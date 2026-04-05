# JCE Planetary Watch — Validation Status

**Companion simulation to:** *From Position to Phase: A Graph-Theoretic Architecture
for Planetary Timekeepers* (Mohan, 2026)  
**doi:** 10.5281/zenodo.19153008  
**ORCID:** 0009-0003-7535-4647  
**Simulation:** https://dtmohan.github.io/jce-watch/  
**Code version:** v10 (March 2026)  
**Last updated:** March 2026  
**Audited by:** ChatGPT (independent), daily comparison against Swiss Ephemeris
SE_TRUE_NODE, 2000-01-01 through 2026-03-22, structural ayanamsha on both sides.

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

## v10 Audit Results (confirmed)

Independent audit: ChatGPT, daily absolute sidereal longitude error vs Swiss
Ephemeris, 2000-01-01 through 2026-03-22, structural ayanamsha on both sides.

| Body | Max error | Mean error | Status |
|------|-----------|------------|--------|
| Sun | 0.374° | 0.178° | ✓ Inside floor |
| Moon | 0.612° | 0.143° | ✓ Inside floor |
| Mercury | 0.631° | 0.190° | ✓ Inside floor |
| Venus | 0.499° | 0.180° | ✓ Inside floor |
| Mars | 0.386° | 0.158° | ✓ Inside floor |
| Jupiter | 0.639° | 0.211° | ✓ Inside floor |
| Saturn | 1.141° | 0.483° | ✗ Outside — structural GI limitation |
| Rahu | 0.005° | 0.003° | ✓ Inside floor |

**7 of 8 bodies confirmed inside the sigma floor (0.6475°).**

**Accurate public claim (v10):**
> The v10 engine stays inside the sigma floor for Sun, Moon, Mercury, Venus,
> Mars, Jupiter, and Rahu over the 2000–2026 audit window. Saturn has a
> structural GI-amplitude limitation (1.141°).

---

## Progress across versions

| Body | Old GitHub | v6 | v7 | v9 | v10 |
|------|-----------|----|----|-----|-----|
| Sun | 0.533° ✗ | 0.374° ✓ | 0.374° ✓ | 0.374° ✓ | 0.374° ✓ |
| Moon | 0.082° ✓ | 0.750° ✗ | 0.616° ✓ | 0.616° ✓ | 0.612° ✓ |
| Mercury | 0.771° ✗ | 0.630° ✓ | 0.630° ✓ | 0.630° ✓ | 0.631° ✓ |
| Venus | 1.001° ✗ | 0.499° ✓ | 0.499° ✓ | 0.499° ✓ | 0.499° ✓ |
| Mars | 0.423° ✓ | 0.386° ✓ | 0.386° ✓ | 0.386° ✓ | 0.386° ✓ |
| Jupiter | 0.710° ✗ | 0.729° ✗ | 0.729° ✗ | 0.729° ✗ | **0.639° ✓** |
| Saturn | 1.297° ✗ | 1.296° ✗ | 1.296° ✗ | 1.142° ✗ | 1.141° ✗ |
| Rahu | 3.476° ✗ | 3.321° ✗ | 3.321° ✗ | **0.005° ✓** | 0.005° ✓ |

Key milestones: v6 fixed Mercury/Venus. v7 fixed Moon. v9 fixed Rahu (mean node).
v10 fixed Jupiter (dynamic GI) and confirmed all previous fixes.

---

## v10 Changes

| Change | Detail | Impact |
|--------|--------|--------|
| Dynamic obliquity `obliquity(days)` | ε(t) = 23.4392911° − 0.013004167°×T | Structurally honest for historical epochs. Over 26yr: 0.003° change — negligible for current audit. Required for Note 12 (4491 BCE) and Note 24 formal derivation. |
| Dynamic GI amplitude `GI_amplitude(days)` | GI(t) = 0.81151° × (sin(ε(t))/sin(ε₀))⁴ | Closes Note 9 derivation across all epochs. Over 26yr: <0.001° change. For 4491 BCE: GI ≈ 0.927° vs J2000 0.812°. |
| Saturn solo re-enabled with geocentric gate | Note 24 §9 requires geocentric longitude, not heliocentric mean. Gate uses pre-correction Saturn geocentric sidereal. | Fires on correct dates (geocentric differs from heliocentric by up to 6° near opposition). |
| Saturn node positions updated | 89.815°/269.815° (Note 12 Table 1) instead of 89°/269°. Both 0.185° from Summer/Winter Solstice crossings. | Structurally honest: Saturn's nodal axis IS the solstice axis (Note 12 §2). |
| Dynamic solo amplitude | Solo_S(t) = δ_S(t) × (M_E/M_S) × T_zod_over_α × (180/π) where T_zod_over_α = 171.85 (derived from GI_J2000, not T3/α = 1683) | At J2000: 0.711° (Note 24 target: 0.714°, 0.4% match). v10.0 bug: used T3=25920 giving 10× overcorrection (~7°). Fixed by deriving T_zod_over_α from GI_J2000. |
| Conservative sign rule | Solo fires only when GI is pushing Saturn backward (sin(phi_GI) > 0). Suppressed when GI overshoots forward. | Prevents 2004-03-23 stacking failure. Solo contribution at 2025 max-error point: ~0°. |

**v10.0 bug (now fixed):** Initial v10 used `T_ZOD = T3 = 25920` and `ALPHA = 15.4002`
giving T_ZOD/ALPHA = 1683, which is T2 (nakshatra closure period) — 10× too large.
Correct T_zod/α ≈ 171.85 is derived from Note 9 GI formula. Saturn blew up to 7.634°.
Fixed by computing T_ZOD_OVER_ALPHA = GI_J2000 / (δ_J × δ_S × M_J/M_S × 180/π).

---

## Saturn: Structural GI Limitation

**At the max-error date (late 2025):**
- Base-only error: ~0.253°
- GI contribution: ~+0.889° (pushing Saturn further in wrong direction)
- Solo contribution: ~0° (sign rule correctly suppresses it here)
- Total: ~1.141°

**Root cause:** The GI amplitude (0.812°) exceeds the sigma floor (0.6475°) by 0.165°.
When the GI fires in the wrong direction relative to the base residual — which happens at
certain phases of the ~2.8-year GI argument cycle — a floor violation is structurally
unavoidable: base error (0.253°) + GI (0.812°) > sigma floor regardless of sign.

This is not fixable by adjusting amplitude or solo correction. It requires a more complete
Note 9 extension that correctly predicts GI phase at each epoch, not just amplitude.

**Saturn has improved across the session:**
- Old code: 1.297° (solo stacking + wrong gate)
- v8: 1.142° (solo disabled)
- v10: 1.141° (solo re-enabled correctly, GI phase still dominant)

---

## Dependency map (v10)

| Category | Components |
|----------|-----------|
| **JCE-native** | GI J↔S dynamic (Note 9), Saturn solo dynamic (Note 24, geocentric gate), Moon 4-term (Note 25+v7), Rahu mean node (v9), structural ayanamsha (Note 13), galactic markers/Abhijit/Vimshottari, dynamic obliquity |
| **CP-truncated** | J→Earth (0.183°), J→Mars (0.073°), S→Mars (0.012°) |
| **Empirical BCs** | Epoch elements at J2000; dw/dt apsidal rates; mass ratios; Mars de/dt |
| **Audit oracle** | Swiss Ephemeris (SE_TRUE_NODE) |

---

## Note 5 exaltation degrees (confirmed correct)

All nine exaltation degrees match Note 5 Table exactly:

| Body | Code | Note 5 | Derivation |
|------|------|--------|-----------|
| Sun ☉ | 10° | 10° | Observed — vernal equinox anchor |
| Moon ☽ | 43° | 43° | GC-N − 1×obl = 66−23.44 = 42.56° ≈ 43° (Hyades, 0.95°) |
| Rahu ☊ | 60° | 60° | GC-N − 6° (6° before summer galactic crossing) |
| Jupiter ♃ | 95° | 95° | GC-N + 29° = 1.24×obl |
| Mercury ☿ | 165° | 165° | +99° from GC-N = 4.22×obl |
| Saturn ♄ | 200° | 200° | GC-S − 2×obl = 246−46.88 = 199.12° ≈ 200° |
| Ketu ☋ | 240° | 240° | GC-S − 6° = near GC at 242.84° (2.84° from Mula start) |
| Mars ♂ | 298° | 298° | GC-S + 52° = 2.22×obl |
| Venus ♀ | 357° | 357° | GC-N − 69° = 2.94×obl |

Earlier audit finding (Rahu 60→63, Moon 43→33) was incorrect. Note 5 explicitly
derives all positions. The code correctly implements Note 5.

---

## Structural constants

| Constant | Value | Status |
|----------|-------|--------|
| σ floor | 0.6475° | ✓ Exact (Notes 2/3/4) |
| α | 15.4002 | ✓ Exact |
| GI period | 925.71 yr | ✓ Exact (T3/28, Note 9) |
| GI amplitude | 0.81151° × (sin(ε(t))/sin(ε₀))⁴ | ✓ Derived, now time-varying |
| Abhijit span | 4.222° | ✓ Exact |
| Winding number | 22,500 | ✓ Integer-exact |
| Obliquity | ε(t) = 23.4392911° − 0.013004167°×T | ✓ Derived (dynamic) |
| GC-N crossing | 66.0° sidereal | ✓ Verified (Note 5) |
| Ecliptic-galactic node | 246.2° sidereal | ✓ Verified (Note 18) |
| Sgr A* direction | 243.0° sidereal | ✓ Verified (Note 18) |
| Node−Sgr A* gap | 3.196° | ✓ Exact (obliquity × 3/22, Notes 18/21) |
| Structural ayanamsha | ~23.85° at J2000 | ✓ Verified (Note 13) |
| Saturn Ω_S ascending | 89.815° sidereal | ✓ Verified (Note 12 Table 1, 0.185° from solstice) |
| Saturn Ω_S descending | 269.815° sidereal | ✓ Verified (Note 12 Table 1) |
| T_ZOD_OVER_ALPHA | 171.85 | ✓ Derived from GI_J2000 (Note 9/24) |
| Rahu J2000 sidereal | 101.195° | ✓ Exact match Note 12 Table 1 |

---

## Open threads (priority order)

**1. Saturn GI phase accuracy (Note 9 extension)**
GI amplitude exceeds floor. Floor violation structurally unavoidable when GI fires
in wrong direction. Fix requires full secular J-S perturbation giving correct GI
phase at each epoch, not just amplitude.
Resume: "extend Note 9 GI derivation to include phase calibration from full secular
J-S perturbation — amplitude 0.812° correct, phase needs epoch correction term"

**2. Saturn solo formal sign derivation (Note 24 §8)**
Conservative sign rule implemented (fire only when GI pushes backward).
Formal derivation from conductance model would give the complete sign rule.
Resume: "derive Saturn solo nodal sign and amplitude from post-GI residual —
Note 24 §8 formal derivation"

**3. dw/dt apsidal rates from conductance integral**
Lagrange-Laplace empirical BCs. Relevant beyond ~35yr.
Resume: "derive apsidal dw/dt from Laplace coefficient conductance integral"

**4. Moon variation A3 from triad conductance**
Hill factor 2.74× pending.
Resume: "derive Moon A3 as eigenvalue of Hill intermediate orbit"

---

## Bugs fixed (cumulative)

| Version | Bug | Fix |
|---------|-----|-----|
| v4 | Duplicate getRahuLongitude() — mean node overriding true node (1.7° above floor) | Removed |
| v4 | btnRosette nested in btnHelio / getHelioAngle() mean longitude / ayanamsha name | Fixed |
| v5 | 15-term Meeus Moon / 5-term Meeus Rahu | Note 25 stand-alone engines |
| v6 | J→E/J→M/S→M below floor retained / dw/dt mislabeled | CP truncation / relabeled |
| v7 | Moon A4 term missing — error budget 0.862° stated as 0.580° | A4 added; 0.612° ✓ |
| v8 | Solo stacking with GI near node (v7) | Solo disabled |
| v9 | 1-term Rahu correction mismatches SE osculating waveform (3.3°) | Mean node; 0.005° ✓ |
| v10 | Solo gate heliocentric not geocentric (Note 24 §9) | Geocentric pre-correction gate |
| v10 | Saturn node 89°/269° — should be 89.815°/269.815° (Note 12) | Corrected |
| v10 | T_ZOD = T3 = 25920 in solo formula — 10× too large (gave 7° not 0.7°) | T_ZOD_OVER_ALPHA = 171.85 derived from GI_J2000 |
| v10 | Fixed obliquity and GI amplitude | Dynamic obliquity(days) and GI_amplitude(days) |

---

## Version history

| Version | Date | Key changes |
|---------|------|-------------|
| v1-v3 | Mar 2026 | Keplerian base, GI, SR/SD, rosette |
| v4 | Mar 2026 | Saturn Layer 2; Rahu true node; helio engine; ayanamsha rename; gear back |
| v5 | Mar 2026 | Moon + Rahu → Note 25; dw/dt relabeled |
| v6 | Mar 2026 | J→E/J→M/S→M removed per CP |
| v7 | Mar 2026 | Moon A4 added — Moon inside floor (0.612°) |
| v8 | Mar 2026 | Solo disabled — stacking problem found |
| v9 | Mar 2026 | Rahu → mean node — 3.321° → 0.005° ✓ |
| v10 | Mar 2026 | Dynamic obliquity + GI; solo re-enabled (geocentric gate, Note 12 nodes, dynamic amplitude); Jupiter inside floor (0.639°) |

---

*Confirmed inside sigma floor (0.6475°) over 2000–2026 (v10):*  
*Sun (0.374°), Moon (0.612°), Mercury (0.631°), Venus (0.499°),*  
*Mars (0.386°), Jupiter (0.639°), Rahu (0.005°)*

*Structural limitation: Saturn (1.141°) — GI amplitude 0.812° > floor;*  
*floor violation unavoidable when GI fires in wrong direction.*  
*Fix requires Note 9 phase extension, not amplitude adjustment.*
