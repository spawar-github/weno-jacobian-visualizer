# WENO · Jacobian · implicit ODE solvers

**[▶ Open the live visualizer](https://spawar-github.github.io/weno-jacobian-visualizer/)**

An interactive, single-file explainer for how WENO reconstruction, the banded Jacobian, and implicit ODE integration work together in a finite-volume adsorption solver — reproducing the dynamic column breakthrough (DCB) case study of Haghpanah, Majumder, Nilam, Rajendran, Farooq, Karimi & Amanullah, *Ind. Eng. Chem. Res.* 2013, 52, 4249–4265.

It runs a real method-of-lines simulation in the browser — no build step, no dependencies beyond two CDN stylesheets — integrating CO₂/N₂ breakthrough on zeolite 13X out to real time τ = 8000 s while you watch.

## What it simulates

Six coupled dimensionless PDEs per finite-volume cell (paper eqs 1–7), with the paper's own column geometry, isotherm and operating parameters (Tables 2–3):

| Field | Equation |
|---|---|
| `y` | component mass balance (CO₂ gas fraction) |
| `x₁`, `x₂` | CO₂ and N₂ solid loadings, linear-driving-force uptake |
| `T` | bed energy balance |
| `T_w` | wall energy balance |
| `P` | total mass balance → pressure, with Darcy velocity |

A dual-site Langmuir isotherm with Arrhenius, temperature-dependent affinities couples the two fronts: the CO₂ concentration front runs ahead of the slower thermal front, so the outlet composition plateaus *below* the feed value until the heat front elutes — the roll-up effect the paper's Figure 4 shows.

## What you can see

- **Breakthrough at the column exit** — outlet velocity, temperature and CO₂ fraction vs time, plotted against a reference curve digitized from the paper's Figure 4a, with the two transition times (520 s, 3600 s) it reports marked.
- **Validation cards** — live comparison of transition times and plateau levels against the paper's numbers, with a percent-error readout.
- **Column profiles** — CO₂ gas fraction, bed temperature and solid loading along the column at the current time.
- **The banded Jacobian** — the top-left 6×6 cell blocks of `J = I − c·Δτ·∂F/∂u`, rendered as a heat map, plus the actual dense-vs-band entry counts and the number of colouring probes used to build it.
- **WENO reconstruction** — pick a field and a cell, and watch the two stencil weights blend at the face. Near a sharp front the steep-side weight collapses, which is exactly how WENO kills oscillations without tuning.

Interactive controls for grid resolution N, the adaptive solver's max step size, and the LDF mass-transfer rate.

## Why each piece is there

The physics forces the choices. A sharp, self-sharpening concentration front makes naïve high-order schemes ring — overshoots push a mole fraction out of [0,1] and break the isotherm — while low-order upwinding smears the front. WENO resolves both. Finite-volume framing makes the face values conservative by construction. The resulting system is stiff (the solid-to-gas capacity ratio ψ ≈ 279), so an implicit integrator is needed to take useful step sizes, which means a damped-Newton solve and a Jacobian each step. WENO's compact stencil is what makes that Jacobian banded (lower half-bandwidth 12, upper 11 for six fields), and the solver builds it with 25 coloured finite-difference probes instead of one per state variable, then factorises it in band storage.

Remove any one piece and the simulation is inaccurate, unstable, or too slow.

## How closely this matches the paper

Every qualitative feature of Figure 4a reproduces: the initial outlet-velocity dip, the sharp CO₂ transition onto a plateau below the feed composition, the temperature plateau, and the second transition where the heat front elutes and the outlet reaches the 0.15 feed value. Transition times land within about 2% of the ≈520 s / ≈3600 s the paper states in its text; the plateau levels match to better than 1%.

Two honest caveats. The reference curve drawn in the app is digitized by eye from Figure 4a — a guide, not data; the dotted verticals at 520 s and 3600 s are the numbers the paper actually states, and are the more reliable comparison. And the paper's own reference run used 2000 volume elements, while a browser is tuned here for a few dozen; raising N narrows the fronts further, but the transition times and plateau levels are already grid-converged at N ≈ 40.

## Running locally

Open `index.html` in any modern browser. That's the whole thing — the simulation, the rendering, and the math typesetting all live in that one file.

## Author

Sunny Pawar · University of Alberta
