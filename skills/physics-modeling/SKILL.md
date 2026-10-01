---
name: physics-modeling
description: >-
  Load for mechanics, thermodynamics, electromagnetism, waves, unit conversion,
  physical derivations, or simulations based on physical laws.
---

# Physics Modeling

## When to use

- Derive, calculate, or simulate a system governed by physical laws.
- Convert units or estimate physical quantities.
- Validate a model using dimensions, limiting behavior, or conservation laws.

## Hard rules

- Define system boundary, coordinates, sign convention, reference state, and time interval.
- List all quantities with values, uncertainty where relevant, and SI units.
- Convert units before calculation; retain units through every equation.
- State modeling assumptions, including idealizations, neglected effects, and regime limits.
- Use vectors for direction-dependent quantities; state scalar projections explicitly.
- Solve symbolically before numerical substitution when practical.
- Show each non-obvious derivation step and identify governing law.

## Workflow

1. Define system, diagram, coordinates, variables, and known conditions.
2. Select applicable conservation law, constitutive relation, or field equation.
3. State assumptions and confirm model applies in stated regime.
4. Derive equations and solve with units intact.
5. Calculate values, directions, and uncertainty or significant figures.
6. Verify dimensions, limits, signs, conservation, and physical scale.

## Checklist

- SI conversion complete and dimensionally consistent.
- Directions, angles, and radians versus degrees handled correctly.
- Initial and boundary conditions specified.
- Conservation of energy, momentum, charge, or mass checked when applicable.
- Limiting case agrees with known behavior.
- Simulation step size or solver tolerance supports stable result.

## Anti-patterns

- Mixing unit systems in one equation.
- Treating mass and weight as interchangeable.
- Using scalar equations for vector forces without components.
- Hiding assumptions such as no friction, rigid body, or ideal gas.
- Trusting a simulation without convergence or conservation checks.

## Refs

- SI Brochure: unit definitions and conversions.
- SciPy `solve_ivp`: ordinary differential equation simulation.
- Use `scipy.constants` for standard physical constants.
