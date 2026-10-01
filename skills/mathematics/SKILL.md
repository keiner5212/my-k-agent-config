---
name: mathematics
description: >-
  Load for algebra, calculus, linear algebra, probability, statistics, proofs,
  optimization, or quantitative derivations that require explicit reasoning.
---

# Mathematics

## When to use

- Solve or derive equations, inequalities, integrals, matrices, probabilities, or statistics.
- Prove a statement or explain a quantitative method.
- Check a model result for validity, scale, or precision.

## Hard rules

- Define variables, domains, units, and assumptions before manipulating formulas.
- Keep exact forms until final evaluation when practical.
- Show transformations needed to justify result; do not skip non-obvious steps.
- State conditions for division, roots, logarithms, inverses, and convergence.
- Check extraneous solutions after squaring, multiplying by unknowns, or changing domains.
- Report numerical precision consistent with inputs and problem purpose.
- Separate known facts, assumptions, and estimates.

## Workflow

1. Restate target, known values, constraints, and requested output.
2. Select method and write governing definitions or equations.
3. Derive symbolically, then substitute values with units where applicable.
4. Solve and state all valid solutions in required domain.
5. Verify by substitution, limiting behavior, symmetry, bounds, or an alternate method.
6. Present result with units, precision, and assumptions.

## Checklist

- Variables and domains defined.
- Units dimensionally consistent.
- Probability lies in [0, 1]; variance is nonnegative.
- Matrix dimensions and rank support claimed operation or solution.
- Boundary and limiting cases behave as expected.
- Result magnitude and sign are plausible.

## Anti-patterns

- Decimal arithmetic before symbolic simplification.
- Applying a formula without its assumptions.
- Giving one root without checking domain restrictions.
- Reporting p-values without sample size, effect size, or assumptions.
- Claiming a numerical result is exact.

## Refs

- SymPy: symbolic algebra and exact expressions.
- NumPy and SciPy: numerical linear algebra, optimization, and integration.
- Use standard notation and define any nonstandard convention.
