# JCE Planetary Watch

**Interactive simulation companion to:**
*From Position to Phase: A Graph-Theoretic Architecture for Planetary Timekeepers*
Deepak T. Mohan · Helical Imperative Research · ORCID [0009-0003-7535-4647](https://orcid.org/0009-0003-7535-4647)

🔭 **[Open the simulation](https://dtmohan.github.io/jce-watch/)**
📄 **[Read the paper](https://doi.org/10.5281/zenodo.19153008)**

---

## What this is

A browser-based simulation of the JCE planetary watch architecture. All seven classical planets are displayed with **true geocentric apparent longitudes in the sidereal frame** (Lahiri ayanamsha) — the coordinate system native to the Jyotish astronomical tradition, derived from the ~500 BCE encoding epoch. Retrograde motion emerges naturally from heliocentric-to-geocentric vector computation.

The simulation implements the findings of the JCE Architecture Series (Notes 1–7, Mohan 2026) directly in running code.

---

## What it shows

### Planetary positions — sidereal frame
- All 7 classical planets at correct geocentric sidereal longitudes
- Lahiri ayanamsha applied at every frame (~23.85 degrees at J2000, +50.3 arcsec/yr)
- Toggle between Sidereal (default, JCE-correct) and Tropical frames
- Live ayanamsha value displayed as simulation advances

### Retrograde motion — Ketu zero-crossings
- Retrograde detected via sign of geocentric angular velocity
- Retrograde planets flagged with rose halo and rose-coloured trail arc
- Direct motion leaves per-planet colour trail
- Each retrograde episode contains three Ketu moments: Station Retrograde, midpoint, Station Direct

### Retrograde stamps and pattern accumulation
At each Station Direct, a diamond is stamped at the midpoint longitude on the orbit ring:
- **Mercury**: stamps numbered 1-2-3 (annual triplet); ~17 deg/yr precession visible over decades; full rosette closes at ~21 years
- **Venus**: successive stamps connected by dashed interior lines — the 8:5 pentagram builds over 8 years
- **Mars, Jupiter, Saturn**: stamps accumulate showing characteristic arc spacings

### JCE structural constants — fixed belt markers

| Marker | Sidereal position | Source |
|--------|------------------|--------|
| GC-N (blue line) | 66 deg | Summer solstice galactic crossing — Rahu exaltation region; ecliptic enters galactic N. hemisphere (Note 5) |
| GC-S (rose line) | 246 deg | Winter solstice galactic crossing — Mula nakshatra; Sgr A* at 242.84 deg (Note 5) |
| Exaltation diamonds | 9 positions | Galactic exaltation grid: each graha at integer x obliquity from nearest crossing (Notes 5-6) |
| Abhijit band (gold arc) | 276d40m to 280d53m20s | Correction node for sidereal month remainder — 4.222 deg absorbs 4.238 deg orbital residual (Note 1) |

### Abhijit correction detection
When the Moon transits the Abhijit band (~8.5 hours per sidereal month), the panel activates: "Correction node active — dasha balance uses 4.222 deg span." Without this correction, dasha timing carries plus/minus 6-18 months of systematic error depending on the lord.

### Nodal axis
- Rahu ring: one revolution per 18.6 years (integral operator); eclipse predictor
- Ketu needle: points toward descending node (differential operator)
- Galactically grounded: Ketu source role corresponds to galactic center in Mula pada 1

### Vimshottari dasha display
Current dasha lord, elapsed fraction, remaining years, next lord. 120-year cycle.

---

## Controls

| Control | Function |
|---------|----------|
| Speed slider | Pause to 120 yr/s in 8 steps |
| Sidereal / Tropical | Switch coordinate frame (clears trails) |
| Click a planet | Isolate it — others dim |
| Click again | Return to full view |
| Reset | Return to March 21, 2026 |

---

## Things to watch

**Mercury rosette** — isolate Mercury, set to 5 yr/s, run 25 sim-years. Watch the spirograph build petal by petal. Closes at ~21 years. Only visible in geocentric sidereal frame.

**Venus pentagram** — isolate Venus, 5 yr/s, run 10 sim-years. The 8:5 resonance draws itself.

**Saturn clock** — watch Saturn's stamps step around the zodiac at ~12.6 deg/yr. Full revolution: ~29 years.

**Abhijit trigger** — set 1 day/s, watch the Moon cross the gold band once per sidereal month.

**Galactic axis** — GC-N and GC-S mark where the galactic plane crosses the ecliptic. Rahu orbits near GC-N. Ketu source role corresponds to GC-S (galactic center direction).

---

## The core argument

Historical orreries asked: where is each planet? The Jyotish system asked: how are the planetary periods interfering? These are different questions requiring different instruments.

The sidereal frame is not a convention — it is a timestamp, preserving the ~500 BCE encoding epoch in the Sun's exaltation degree. Abhijit is not a mythological holdover — it is a precision correction mechanism whose removal introduced a known systematic error into every dasha calculation since ~500 BCE.

---

## Companion paper

Mohan, D.T. (2026). *From Position to Phase: A Graph-Theoretic Architecture for Planetary Timekeepers.* doi:10.5281/zenodo.19153008 · CC BY 4.0

## JCE Architecture Series

Mohan, D.T. (2026). Notes 1–7 and Corollaries. Helical Imperative Research, Zenodo.
Note 1: Abhijit as correction node | Note 2: Vega as topological attractor | Note 3: sin-squared(obliquity) as master CP constant | Note 4: Orion as calibration origin | Note 5: Galactic exaltation grid | Note 6: Signal architecture of exaltation | Note 7: Pythagorean closure

## License

CC BY 4.0 — free to use, adapt, and build upon with attribution.
