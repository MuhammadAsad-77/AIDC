# Week 3 Dataset Approval

## The decision, in one sentence

Complete this sentence exactly. It is the sentence you would defend in a
handover meeting, and it is what you will read aloud if asked.

> This file **may** be used to guide qualitative descriptive shift handover review and prioritize physical maintenance checks on Line_C,
> but **may not** be used to calculate contractual plant OEE, automate maintenance scheduling, or train predictive failure models,
> on condition that the Line_A duplicate (row 73) is removed, missing Line_B energy (row 56) and Line_C downtime (row 24) are audited against source telemetry,
> and the decision is revisited when the missing Line_A 08:00 record is retrieved and the Line_C temperature spike is confirmed by physical inspection.

Decision: **Approve with conditions**

## Why this decision and not the one next to it

A narrow descriptive handover can proceed because the high temperature alert on Line_C provides actionable situational awareness for the incoming supervisor. However, full approval is impossible because corrupt downtime, missing energy, duplicate records, and missing hourly coverage make any quantitative production or efficiency KPI calculation untrustworthy. Rejecting completely is unnecessary because supervisors need to see the flagged issues before beginning their shift.

## Conditions

| Condition | Owner | Evidence that closes it | Review trigger |
|---|---|---|---|
| Reconcile Line_B 00:00 missing energy reading | Electrical Maintenance | Meter log export showing kWh consumed during midnight hour | Prior to weekly energy billing reconciliation |
| Verify Line_C 13:00 downtime overflow (91.3 min) | Shift Supervisor | Operator downtime log explaining cause and true duration | Before publishing daily downtime report |
| Inspect Line_C motor physical condition | Maintenance Supervisor | Work order sign-off on bearing vibration and thermal scan | Next shift start (08:00 handover) |
| Deduplicate Line_A 10:00 row in data warehouse | Data Pipeline Owner | Deduplication script logged in audit trail | Next pipeline batch run |

## Known issues I am accepting

List what stays broken after approval, and why the permitted use survives it.

| Issue | Why the permitted use survives it |
|---|---|
| Line_C 105.4 °C temperature reading | Handover only needs to know the line ran hot to dispatch a physical check; it does not rely on temperature for model training |
| Missing Line_A 08:00 coverage record | Supervisors can orally confirm Line_A 08:00 status from the physical shift logbook |

## Record

- Decided by: Muhammad Asad
- Date: 2026-10-07
- Dataset fingerprint: 3738f7285c8c295ae0871547855a1dd098de664ba9593502f76eb090b495ab6f
- Commit: Complete Week 3 validation checkpoint
