---
name: numerical-computation
description: >-
  Load for numerical root finding, integration, optimization, curve fitting,
  linear algebra, ODEs, stability analysis, or SciPy and SymPy workflows.
---

# Numerical Computation

## When to use

- Compute approximate roots, integrals, optima, fits, ODE solutions, or matrix solutions.
- Select between symbolic and numerical methods.
- Diagnose precision loss, conditioning, convergence, or solver stability.

## Hard rules

- Use SymPy for exact algebra, derivatives, simplification, and symbolic checks.
- Use NumPy for dense arrays and SciPy for specialized solvers, sparse systems, and statistics.
- State units, variable scaling, domain, initial conditions, and boundary conditions.
- Set absolute and relative tolerances based on required output accuracy and scale.
- Check solver status, residuals, iteration counts, and error estimates before reporting results.
- Prefer a bracketed root method when a sign-changing bracket exists.
- Use `solve`, not an explicit matrix inverse, for linear systems.

## Workflow

1. Define mathematical problem, scale, expected accuracy, and reference solution if available.
2. Choose symbolic, analytic, or numerical method based on problem structure.
3. Set initial guesses, brackets, tolerances, bounds, and stopping criteria explicitly.
4. Run solver and inspect convergence status and diagnostic output.
5. Validate residuals and compare against analytic, high-precision, or refined-grid result.
6. Report method, tolerances, error estimate, limitations, and final precision.

## Checklist

- Root residual is within tolerance and bracket or initial guess is documented.
- Optimization constraints, bounds, and gradient availability are explicit.
- ODE method matches stiffness; RK45 or DOP853 for nonstiff, Radau or BDF for stiff.
- Integration error estimate fits requested precision.
- Linear-system condition number checked when result is sensitive.
- Fit includes residual plot and parameter uncertainty when data supports it.
- Refining tolerance, grid, or step size does not materially change result.

## Anti-patterns

- Treating solver success as proof of correctness.
- Choosing arbitrary tolerances without considering variable scale.
- Using Newton-like methods without a reliable initial guess or derivative behavior check.
- Inverting matrices to solve linear systems.
- Ignoring cancellation, overflow, underflow, or ill-conditioning.
- Using numerical output as exact proof.

## Refs

- NumPy: arrays and dense linear algebra.
- SciPy optimize, integrate, linalg, sparse, and stats.
- SymPy: symbolic derivation and verification.
