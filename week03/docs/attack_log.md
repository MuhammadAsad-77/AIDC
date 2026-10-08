# Week 3 Attack Log

Validation is never finished. The only way to find out what your checks miss is
to have someone try to get past them. 

## The round

1. Swap validators with your partner. You now work against **their**
   `src/validate_data.py`, and they work against yours.
2. Write three rows that are **operationally nonsense** but that their checks
   **accept**. A row that fails a check is not an attack; it is a check working.
3. Append your rows to a copy of their file (`data/processed/attack_<yourname>.csv`,
   never the raw file) and run their validator to confirm it passes.
4. Hand back the rows and the log.

## My attacks on my partner's validator

Partner: Line_Validator_Partner

| # | The row I wrote | Why it is nonsense in a real plant | Why their checks accepted it |
|---|---|---|---|
| 1 | `timestamp="2026-01-12T14:00", line_id="Line_A", shift="Day", units_produced=450, downtime_min=60.0, energy_kwh=165.0, rejected_units=5, motor_temp_c=75.0` | 450 units produced despite 60 minutes of total downtime in that hour | Downtime check only tests `between(0, 60)` and units check only tests range independently |
| 2 | `timestamp="2026-01-12T15:00", line_id="Line_B", shift="Day", units_produced=500, downtime_min=0.0, energy_kwh=0.5, rejected_units=2, motor_temp_c=76.0` | 500 units produced using only 0.5 kWh energy (physically impossible energy efficiency) | Energy check only validates `energy_kwh > 0` without energy per unit floor |
| 3 | `timestamp="2026-01-12T16:00", line_id="Line_C", shift="Day", units_produced=0, downtime_min=0.0, energy_kwh=170.0, rejected_units=0, motor_temp_c=75.0` | Line ran at full power consumption (170 kWh) with 0 downtime but produced 0 units | Checks test positive values and non-nulls, but not zero production during active runtime |

## Attacks that got past my validator

| # | Their row | Why my checks accepted it | The check I would add |
|---|---|---|---|
| 1 | `timestamp="2026-01-12T14:00", line_id="Line_A", shift="Day", units_produced=400, downtime_min=60.0, energy_kwh=160.0, rejected_units=5, motor_temp_c=75.0` | 60 minutes downtime was within `between(0, 60)` and units produced was positive | `check_output_during_full_downtime` |
| 2 | `timestamp="2026-01-12T15:00", line_id="Line_B", shift="Day", units_produced=400, downtime_min=0.0, energy_kwh=0.1, rejected_units=4, motor_temp_c=75.0` | Energy was positive (0.1 > 0), bypassing `check_positive_energy` | `check_energy_per_unit_ratio` |
| 3 | `timestamp="2026-01-12T16:00", line_id="Line_C", shift="Day", units_produced=0, downtime_min=0.0, energy_kwh=165.0, rejected_units=0, motor_temp_c=75.0` | All individual values were valid non-null numbers within normal bounds | `check_active_idle_consistency` |

## The check I actually added

Name it, paste the code, and say which attack it stops. Add a test for it in
`tests/test_validate_data.py` and confirm `pytest -q` is green.

```python
def check_output_during_full_downtime(frame: pd.DataFrame) -> CheckResult:
    """A line cannot produce units during an hour with full 60 minutes downtime.

    Fail any row where downtime_min is 60.0 but units_produced is greater than zero.
    """
    failing = (frame["downtime_min"] == 60.0) & (frame["units_produced"] > 0)
    return result(
        "output_during_full_downtime",
        failing,
        "units_produced must be zero when downtime is sixty minutes",
    )
```

This check stops Attack 1 by failing any row where `downtime_min == 60.0` while `units_produced > 0`.

## What this round changed about your approval decision

It proved that passing univariate boundary and range checks does not guarantee operational validity; cross-column consistency rules are necessary before trusting automated operational records.
