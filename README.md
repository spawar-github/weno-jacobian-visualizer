# WENO · Jacobian · implicit ODE solvers

**[▶ Open the live visualizer](https://spawar-github.github.io/weno-jacobian-visualizer/)**

An interactive, single-file explainer for how WENO reconstruction, the banded Jacobian, and implicit ODE integration work together in a full-physics pressure-swing adsorption (PSA) solver.

It runs a real method-of-lines simulation in the browser — no build step, no dependencies beyond two CDN stylesheets — and integrates the coupled PSA model to dimensionless time τ = 5 while you watch.

## What it simulates

Six coupled dimensionless PDEs per finite-volume cell, solved by the method of lines:

| Field | Equation |
|---|---|
| `y` | component mass balance (CO₂ gas fraction) |
| `x₁`, `x₂` | two solid loadings, linear-driving-force uptake |
| `T` | bed energy balance |
| `T_w` | wall energy balance |
| `P` | total mass balance → pressure, with Darcy velocity |

A dual-site Langmuir isotherm with Arrhenius, temperature-dependent affinities provides two-way thermal coupling, so the adsorption front and the thermal front interact the way they do in a real column.

## What you can see

- **Column profiles** — CO₂ fraction from both solvers plus bed temperature, along the column at the current τ.
- **Breakthrough curves** — outlet velocity, temperature and CO₂ fraction as the fronts elute.
- **The banded Jacobian** — the top-left 6×6 cell blocks of `J = I − Δt ∂F/∂u`, rendered as a heat map so the band structure is visible.
- **WENO reconstruction** — pick a field and a cell, and watch the two stencil weights blend at the face. Near a sharp front the steep-side weight collapses, which is exactly how WENO kills oscillations without tuning.
- **L1 error vs a fine-step reference** — `ode23tb` (BDF1) against `ode15s` (BDF2), using the relative L1 norm `Σ|y−y_ref| / Σ|y_ref|`.

Interactive controls for time step Δt, mass-transfer rate α (the stiffness knob), and cell count N.

## Why each piece is there

The physics forces the choices. A sharp, self-sharpening concentration front makes naïve high-order schemes ring — overshoots push a mole fraction out of [0,1] and break the isotherm — while low-order upwinding smears the front. WENO resolves both. Finite-volume framing makes the face values conservative by construction. The resulting system is stiff, so an implicit integrator is needed to take useful step sizes, which means a Newton solve and a Jacobian each step. WENO's compact stencil is what makes that Jacobian banded (lower half-bandwidth 12, upper 11 for six fields), and a banded LU turns the solve from `O(M³)` into `O(M b²)`.

Remove any one piece and the simulation is inaccurate, unstable, or too slow.

## Scope and caveats

The dimensionless groups are illustrative rather than the exact zeolite-13X set, and the integrators are fixed-step. That is deliberate: it isolates stability, order, and Jacobian structure, rather than the adaptive step-size control that real `ode15s` / `ode23tb` add on top.

## Running locally

Open `index.html` in any modern browser. That's the whole thing — the simulation, the rendering, and the math typesetting all live in that one file.

## Author

Sunny Pawar · University of Alberta
