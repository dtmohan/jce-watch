# JCE Planetary Watch

**Interactive simulation companion to:**  
*From Position to Phase: A Graph-Theoretic Architecture for Planetary Timekeepers*  
Deepak T. Mohan · Helical Imperative Research · ORCID [0000-0001-8054-2085](https://orcid.org/0000-0001-8054-2085)

🔭 **[Open the simulation](https://deepakmohan.github.io/jce-watch/)**

---

## What this is

A browser-based simulation of the JCE planetary watch architecture described in the companion paper. All seven classical planets displayed with **true geocentric apparent longitudes** — meaning retrograde motion emerges naturally from the heliocentric → geocentric vector computation, not from any special-casing.

## What it shows

- **All 7 planets** at correct relative positions (geocentric ecliptic longitude)
- **Retrograde detection** — planets show a ℞ halo and rose-coloured trail arc when retrograde; direct motion leaves a gold trail
- **Rahu / Ketu** — the lunar nodal axis as integral/differential operator pair (18.6-year regression cycle)
- **Vimshottari Dasha** — Geneva-mechanism-style 120-year cycle display, current lord and elapsed fraction
- **Zodiac belt** — 12 signs with tick graduation
- **Time control** — pause, play, speed from 1 day/s up to 120 years/s

## The core claim

Historical orreries tracked absolute position. The Jyotish system tracked **phase relationships**. These are different instruments encoding different theories of astronomical relevance. The minimum spanning tree of the planetary resonance graph, epicyclic differential gears for the Upagrahas, and a 9-pin unequal Geneva mechanism for the dasha display together produce an instrument that is ~40% simpler mechanically while displaying quantities no historical orrery has shown.

Retrograde, in this framing, is not a planet going backward. It is a **Ketu zero-crossing** — the geocentric angular velocity passing through zero — occurring three times per retrograde episode (station retrograde, midpoint, station direct).

## Technical notes

- Pure HTML/CSS/JavaScript, no dependencies, no build step
- Orbital engine: circular mean-motion heliocentric positions → geocentric atan2 conversion
- Retrograde detection: sign of dλ/dt sampled at ±0.5 day interval
- Accuracy: sufficient for display-grade simulation (~100 ppm orbital period error)

## Companion paper

Mohan, D.T. (2026). *From Position to Phase: A Graph-Theoretic Architecture for Planetary Timekeepers.*  
Helical Imperative Research. Zenodo. CC BY 4.0.

## License

CC BY 4.0 — free to use, adapt, and build upon with attribution.
