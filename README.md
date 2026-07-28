# How a PDE becomes a matrix of numbers

**[▶ Open the live walkthrough](https://spawar-github.github.io/weno-jacobian-visualizer/)**

An interactive, single-file explainer for how WENO reconstruction, the banded Jacobian, and an implicit ODE solver work together to turn a coupled PDE system into a running numerical simulation. It's built around a real CO₂/N₂ breakthrough model on zeolite 13X — the case study of Haghpanah, Majumder, Nilam, Rajendran, Farooq, Karimi & Amanullah, *Ind. Eng. Chem. Res.* 2013, 52, 4249–4265 — but the point isn't to reproduce the paper's figures. It's to make the *construction* of the numerical solution visible, one decision at a time, for someone meeting this kind of model for the first time.

Everything runs client-side in the browser: no build step, no backend, no dependencies beyond two CDN stylesheets (KaTeX for the math, Tabler for icons).

## The seven steps

1. **Where the numbers live** — cell averages, the box balance, and why it needs face values nobody stores. Includes the model's actual initial condition and all six discretized balance equations (component mass, pressure, solid loading ×2, bed energy, wall energy) in the paper's own box-indexed notation.
2. **What sits at the face** — the two candidate stencils, WENO's smoothness weights, a draggable demo that produces a genuine overshoot, a three-way race (upwind / fixed-weight / WENO) around a periodic domain, and the boundary conditions at both ends of the column (the real inlet/outlet physics vs. the purely numerical ghost-cell trick).
3. **Why not just step forward** — the timescale spread that makes this system stiff, and the explicit-vs-implicit amplification factor on the simplest possible stiff ODE.
4. **Solving the step** — Newton's method on the implicit residual, stepped iteration by iteration, including the honest case where it doesn't converge and the step gets rejected.
5. **Why the Jacobian is banded** — hover any column of a real Jacobian to see exactly which boxes it touches, then the finite-difference colouring trick that builds the whole band in 25 probes instead of one per unknown.
6. **All of it, running** — the full six-field model, live: breakthrough curves at the column exit and profiles along the bed, reproducing the paper's roll-up effect (the CO₂ front outrunning the slower thermal front).
7. **The whole loop, traced** — not a diagram. A real implicit time step, run live on a small grid, stepped operation-by-operation with the actual residual norms, Jacobian probe counts, and line-search values it produces — alongside an auto-scaled view of what the CO₂ profile is actually doing at each operation.

## The model underneath

Six coupled dimensionless PDEs per finite-volume cell — component mass, total mass/pressure, two solid loadings, bed energy, wall energy — with a dual-site Langmuir isotherm and Arrhenius temperature dependence, using the paper's own column geometry and operating parameters (its Tables 2–3). WENO closes every face; a damped Newton iteration solves each implicit step; the Jacobian is built and factorised in band storage rather than densely.

## Running locally

Open `index.html` in any modern browser. That's the whole thing — the simulation, every diagram, and the math typesetting all live in that one file.

## Author

Sunny Pawar · University of Alberta
