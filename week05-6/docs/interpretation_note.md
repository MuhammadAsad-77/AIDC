# Interpretation note

One page. Six questions, each answered in one sentence, each sentence backed
by something a reader can check in this repository. This is the first
of the lab's two deliverables, and the same six questions are how the capstone
model will be read.

Your file: `data/raw/shift_week_muhammadasad-77.csv`
SHA-256: `ed7c8c9d6e96479a0a56b31e85060711c7090f04be7909d4f9158ca3efb8daf5`

## 1. What does it predict?

`energy_next` is the next hour's energy in kWh for each production line, predicted at the top of the current hour.

## 2. What does it beat, and by how much?

The incumbent rule is "next hour looks like this one"; on the same 87 test hours its MAE was 5.45 kWh, while my regression's was 5.28 kWh, a 0.17 kWh improvement.

| Model | Average miss, kWh | Hours short | Hours scored |
|---|---:|---:|---:|
| Naive, last hour (the rule) | 5.45 | 45 | 87 |
| Line mean | 6.50 | 42 | 87 |
| Same hour yesterday | 6.50 | 56 | 87 |
| Regression | 5.28 | 50 | 87 |

Paste the table `scorecard()` prints, then write the sentence.

## 3. On which metric, and on which does it lose?

The regression wins on average miss (5.28 vs 5.45 kWh) but loses on hours short (50 vs 45).

## 4. What did it miss?

Every model missed the 22:00 next-hour energy on Line_C: the rule's residual was +47.7 kWh and the regression's was +50.0 kWh, alongside a 112.0 C motor reading, which the operator or maintenance team could explain.

## 5. What must be known at the top of the hour?

List every feature your regression uses and, for each, why it exists when the
prediction is made. Then name the column in the file that fails that test,
and how you knew.

| Feature | Known at the top of the hour because |
|---|---|
| `energy_kwh` | It has already been metered for the current hour. |
| `next_hour_night` | It follows from the clock and shift schedule. |

The column that fails the test: `energy_kwh_smoothed`, because its centered average includes the next hour's energy.

## 6. Should it replace the rule?

I would not replace the rule yet: the regression's MAE gain was only 0.17 kWh per hour, it was short more often (50 vs 45 hours), and this test covers only 87 hours.

## The sentence

On the 87 held-out hours from 18 January and early 19 January, the regression beat the rule by 0.17 kWh per hour in mean absolute error (5.28 vs 5.45 kWh).
