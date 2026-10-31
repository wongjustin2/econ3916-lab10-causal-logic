# Correlation & Causality: Confounders, Colliders and the Limits of Controls

## Objective
I tested what happens to a causal estimate when you add control variables, and showed that whether a control removes bias, does nothing, or creates bias depends on the causal structure behind the data, not on the data itself.

## Methodology
- Simulated three data-generating processes, true causation, pure confounding, and pure selection, with true effects of 2.0, 0.0, and 0.0, and compared the correlation each one produces.
- Drew the three basic causal structures (a confounder, a collider, and a mediator) as directed acyclic graphs with `networkx`.
- Ran OLS regressions with `statsmodels` to compare the estimate with and without a confounder as a control, and with and without a collider as a control.
- Built an M-bias structure in which a variable W, measured before treatment, is a common effect of two hidden causes, and estimated the treatment effect with and without W.
- Wrote a function, `coef_on_d()`, that returns the coefficient on the treatment for any list of controls, and used it to control for W together with one of the hidden causes, u1.
- Reproduced the LaLonde (1986) comparison by replacing the experimental control group with CPS respondents, then adjusted the observational estimate for eight pre-treatment covariates.

## Key Findings
| Setting | Estimate | Truth |
|---|---|---|
| Correlations in the three worlds | 0.654, 0.664, 0.632 | effects of 2.0, 0.0, 0.0 |
| Controlling for a collider | -0.592 | 0 |
| M-bias, controlling for W | -0.191 | 0 |
| M-bias, controlling for W and u1 | 0.006 | 0 |

- **The same correlation can come from very different worlds.** All three correlations land near 0.65, yet only one world has a real effect. Nothing in the data tells them apart; the difference lives in the causal structure.
- **A control can create an effect out of nothing.** D and Y were generated independently, but controlling for the collider produced an estimate of -0.592 with a tiny p-value.
- **"Control for anything measured before treatment" is not a safe rule.** W was measured before treatment and still pushed the estimate to -0.191. Also controlling for u1 closed the path again (0.006), but in real data that hidden cause would usually be unmeasured.
- **LaLonde:** the randomized experiment gives an effect of \$1,794. Swapping in the CPS comparison group flips it to -\$8,498. Adding the eight covariates moves it to \$699, closing 89% of the gap. Getting closer to the benchmark doesn't prove the adjustment is right, and without an experiment there would be nothing to check it against.
