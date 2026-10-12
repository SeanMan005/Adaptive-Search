# The Selector Transfer

**An interactive model of adaptive search: when a system should stop learning from what arrives and start imposing what it has learned.**

Every adaptive search faces the same trade-off. Early on, the searcher knows nothing, so it should let the environment shape it: take in whatever arrives and adjust its model to fit. Later, once the model is good, it can flip roles. It uses what it has learned to decide which new inputs fit, filtering instead of absorbing. This is the familiar **exploration vs. exploitation** trade-off, and the hard question is *when* to switch, and whether the system ever switches back.

This simulation makes that switch visible. A lattice grows from particles arriving out of a field. The **selector**, the side that decides what counts, starts with the field and can transfer to the lattice once the lattice has enough weight. The page shows who holds the selector at each moment, and what happens when the transfer comes too early, too permanently, or not at all.

---

## The model in search terms

| In the simulation | In adaptive search |
|---|---|
| **Field** (incoming particles) | The environment, or the data the searcher samples |
| **Lattice** | The searcher's internal model, built from what it has absorbed |
| **Lattice axes** | The model's learned structure: its assumptions about where good solutions lie |
| **Interface holds the selector** | **Exploration**: inputs shape the model; the model bends to fit them |
| **Algorithm holds the selector** | **Exploitation**: the model judges inputs; off-axis ones are rejected |
| **Field drift (volatility)** | A non-stationary environment, where the right answer moves over time |
| **Record validity** | How well the model's structure still matches the current environment |
| **Pre-refined intake** | Inputs arriving already filtered toward the model's own assumptions |

### Exploration phase (interface selects)

While the field holds the selector, every retained arrival is **filed**: it joins the lattice where it landed, pulls nearby nodes toward itself, and nudges the lattice's axes toward the field's direction. The model is being rewritten by its inputs. This is where the model's weight is built up.

### Exploitation phase (algorithm selects)

Once the lattice holds the selector, arrivals are tested against its axes. On-axis arrivals are **placed**, snapped onto the lattice's own geometry without rewriting it. Off-axis arrivals are **rejected** and bounce away. The field also starts arriving **pre-refined**, its spread narrowing toward the lattice's axes. This makes the model cheaper to grow, but it also means the model mostly sees inputs that already agree with it.

### The transfer rule

The selector moves when one side outweighs the other:

- **Internal weight** = reach × linkage (how far the lattice extends × how load-bearing its links are)
- **Arriving weight** = (1 − pre-refinement) × (0.35 + 0.65 × volatility)

When internal weight exceeds arriving weight, the lattice can take the selector. A volatile environment raises the bar: the more the world is changing, the more a model has to have earned before it should stop listening.

---

## Controls

| Control | What it sets | Search interpretation |
|---|---|---|
| **Compute** | Arrivals per cycle | Sampling budget. More samples, same verdict on each one |
| **Interface gradient** | Share of arrivals that carry signal and form links | Signal-to-noise of the environment |
| **Landscape volatility** | How fast the field's direction drifts | How non-stationary the problem is |
| **Retention as linkage** | Share of arrivals kept rather than passing through | Learning rate: how much each input sticks |
| **Deposit split** | Growth spent on reach vs. tightening | Breadth vs. depth of the model |
| **Cycles per second** | Simulation speed | — |

### Custody schedules

These decide *how* the selector moves, the policy layer on top of the transfer rule.

| Schedule | Behavior | Search analogue |
|---|---|---|
| **Weighed** | Custody follows the weight comparison every cycle, in both directions | Adaptive explore/exploit balance |
| **Sequential** | Custody locks at the first crossing and never returns | Greedy commitment; prone to **premature convergence** |
| **Iterative** | Locked custody, with small placement corrections only | Local refinement; improves within a basin, never leaves it |
| **Evolutionary** | Custody handed back to the field for bounded intervals | Periodic re-exploration (restarts, annealing-style reheating) |

**Seize selector** forces the transfer by hand, ahead of the weight comparison. It shows what happens when a model starts judging before it has earned it.

---

## Readouts

- **Internal vs. arriving weight**: the balance beam that decides the transfer
- **Reach, linkage, mass**: the components of internal weight
- **Record validity**: alignment between the lattice's axes and the current field direction
- **Pre-refined intake**: how much incoming material is already shaped by the lattice
- **Legibility paid by**: who absorbs the cost of translation. The system pays while exploring; the field pays while the lattice selects
- **Placed / rejected**: the exploitation-phase behavioral signature
- **History chart**: weights and validity over time, with a band showing who held the selector each cycle
- **Arrivals log** and **Diagnosis** panel: plain-language readings of the current state

### Diagnosis flags

| Flag | Condition | What it means in search terms |
|---|---|---|
| The stall | Compute near zero | No samples, no search |
| Neutral drift | Almost no signal | Exploring noise; the model spreads but never bears load |
| Permanent porosity | Almost no retention | Nothing is learned; every cycle starts over |
| Reach-only | All growth to reach | Broad, shallow model |
| Tighten-only | All growth to tightening | Deep, narrow model that sees little |
| Premature custody | Lattice selects with low validity | Exploiting a model the environment never confirmed |
| Custody decaying | Validity slipping under high volatility | The world moved and the model didn't follow |
| Funded loop | Lattice selects with high validity and pre-refinement | Exploitation working as intended |

---

## Configurations to try

The page includes one-click presets:

1. **Funded loop**: balanced settings on stable ground; custody is earned, then inputs arrive pre-aligned.
2. **The stall**: compute at zero; everything else correct, nothing happens.
3. **Neutral drift**: maximum arrivals, no signal.
4. **Permanent porosity**: selection runs, nothing is retained.
5. **Reach-only** / **Tighten-only**: the two failure modes of the deposit split.
6. **Premature custody**: the selector is seized early, then rejects the inputs that would have corrected it.
7. **Incumbent decay**: custody locked while the environment swings away; watch rejections climb.
8. **Iterative seal**: placement keeps improving, but the structure is never re-examined.
9. **Bought returns**: custody handed back on a schedule, out of banked weight.

**Things to watch for:**
- Under **Sequential** with high volatility, validity falls while rejections rise. The model becomes more confident and less correct at the same time.
- Under **Evolutionary**, validity recovers during each return interval, at the cost of losing the pre-refinement subsidy while the field selects.
- **Seize selector** at the very start and the lattice rejects most arrivals on axes the field never confirmed.

---

## Running it

Open `index.html` in any modern browser. It's a single self-contained file: no build step and no dependencies beyond Google Fonts. It supports light and dark mode and works on phone-width screens.

---

## Limitations

- **Illustrative, not calibrated.** The weights, rates, and thresholds were chosen to make the mechanism visible, not fitted to any real system.
- **One lattice, one field.** Real search often involves many competing models, or several environments at once.
- **Stochastic runs.** Particle direction, signal, and retention are random, so the same settings can play out differently each time.
- **Geometric stand-in.** Validity is measured as the angle between the lattice's axes and the field direction, a simple proxy for model accuracy rather than a general measure.

The point is the shape of the dynamic, not the numbers: *a system that switches from learning to judging too early, or never switches back, can become more confident while becoming less right.*
