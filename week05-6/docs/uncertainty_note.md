# Uncertainty and limitations note

One page. Six questions, each answered in one sentence, each sentence backed by
a number a reader can find in this repository: `artifacts/spread.csv`,
`artifacts/booking_scorecard.csv`, or a table the notebook prints.

Your file: `data/raw/shift_week_muhammadasad-77.csv`
SHA-256: `ed7c8c9d6e96479a0a56b31e85060711c7090f04be7909d4f9158ca3efb8daf5`

The improved model uses `energy_kwh`, `next_hour_night` and `energy_last_hour`.

## 1. What does the coefficient mean?

Holding the other features fixed, each extra kWh in `energy_last_hour` was associated with 0.226 kWh more next-hour energy, not a causal effect, and adding it improved test MAE from 5.28 to 4.96 kWh.

## 2. How sure is it?

The `energy_last_hour` coefficient's leave-one-day-out sensitivity range was 0.203 to 0.248 across six training days (12-17 January), and it is not a 95% confidence interval.

| Coefficient | All training days | Lowest | Highest |
|---|---:|---:|---:|
| `energy_last_hour` | 0.226 | 0.203 | 0.248 |

## 3. How far can one hour be?

For the improved model, $s=5.71$ kWh gives a nominal $\hat y \pm 11.2$ kWh range with 92% (80/87) empirical coverage; its largest miss was +49.3 kWh, 38.1 kWh beyond the range at Line_C's 22:00 hour, where the 112.0 C motor reading is a question for the operator or maintenance team.

## 4. Which assumptions did you check?

| Assumption | Check | Result |
|---|---|---|
| Misses are close to independent from hour to hour | Lag-1 autocorrelation $\rho_1$ | Improved model: 0.04, near zero; no evidence of strong serial dependence. |
| The spread is the same before day and night hours | $s$ for each | Day: 5.65 kWh (210 hours); night: 5.72 kWh (201 hours), similar in this training week. |

The line-mean model's misses ran in streaks ($\rho_1=0.67$) because it does not use the current hour's energy, so an interval treating its hourly errors as independent would be too narrow.

## 5. Which cannot be tested yet?

This week cannot test whether the energy patterns and fitted relationships stay stable across seasons or operating changes; if they shift, the model and margin may no longer fit, so logs across more weeks and conditions are needed.

## 6. Is it adequate for the booking?

At 3:1 the improved model used a 3.6 kWh margin and cost 712.1 on 87 test hours versus the rule's own 4.0 kWh margin and cost 793.0, with 23/87 (26%) hours short against the 25% aim; I would not rely on it alone yet because the one-week test included a 49.3 kWh miss.

## The sentence

With a 3.6 kWh margin, the improved model cost 712.1 against the rule's 793.0 on 87 test hours and was short on 23 hours (26%).
