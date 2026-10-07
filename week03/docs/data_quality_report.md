# Week 3 Data Quality Report

Keep this to one page. A supervisor should be able to read it in two minutes
and know what they may do with the file.

## 1. What I examined

- File name: `shift_log_muhammadasad-77.csv`
- SHA-256 fingerprint: `3738f7285c8c295ae0871547855a1dd098de664ba9593502f76eb090b495ab6f`
- Rows / columns: 72 rows / 8 columns
- One row represents: One production line operating during a single hourly period
- The business key is: `timestamp`, `line_id`
- Reviewed by / date: Muhammad Asad / 2026-10-07

## 2. What I found before running the validator

Fill this in **during Step 3 of the notebook**, before you compare against the
checks. Do not go back and improve it afterwards; the gap is the point.

| # | Record (line and hour) | What I noticed |
|---|---|---|
| 1 | Line_C at 13:00 (CSV line 24) | Downtime recorded as 91.3 minutes within a 60-minute hourly period |
| 2 | Line_B at 00:00 (CSV line 56) | `energy_kwh` value is missing / blank (`NaN`) |
| 3 | Line_C at 05:00 (CSV line 72) | Non-ISO timestamp format (`13/01/2026 05:00`) and high motor temperature (105.4 °C) |
| 4 | Line_A at 10:00 (CSV line 73) | Exact duplicate of CSV line 13 appended at the end of the file |

## 3. What the checks found

One row per distinct problem. If one fault trips two checks, say so rather than
counting it twice.

| Check | CSV line(s) | Observation (no speculation) | Impossible or surprising? | What should happen next | Who owns it |
|---|---|---|---|---|---|
| `timestamp_parse` | 72 | Field value is `"13/01/2026 05:00"`; does not conform to ISO-8601 (`YYYY-MM-DDTHH:MM`) | Impossible under contract | Correct telemetry logger format string; verify observation belongs to 05:00 | OT Data Engineer |
| `required_values_complete` | 56 | Field `energy_kwh` is empty string (`NaN`) for Line_B at 00:00 | Impossible under contract | Query physical submeter non-volatile memory buffer to recover true reading | Electrical Maintenance |
| `exact_row_uniqueness` & `business_key_uniqueness` | 13, 73 | Line_A at 10:00 is repeated verbatim as record 73 | Impossible (redundant data) | Drop duplicate record 73 and document deduplication in pipeline audit log | Data Pipeline Owner |
| `downtime_range` | 24 | Field `downtime_min` is 91.3 min in a 60-minute window | Impossible under contract | Inspect physical operator paper shift log to check if reading was 9.1 min or prior shift spillover | Shift Supervisor |
| `expected_line_hour_coverage` | Absence at Line_A 08:00 | Line_A has 23 distinct line-hours instead of 24; 08:00 record is completely absent | Impossible under coverage SLA | Query gateway network drop buffer for Line_A 08:00 to recover missing packet | Plant Network Admin |
| `motor_temperature_review_range` | 72 | Field `motor_temp_c` is 105.4 °C (> 100.0 °C review threshold) | Surprising (physically possible) | Dispatch technician to perform thermal imaging and bearing vibration checks on Line_C | Maintenance Supervisor |

## 4. The gap

- Found by the checks, missed by me: The absence of Line_A at 08:00 (coverage gap). Row-by-row manual scanning inspects rows that exist, but cannot detect missing rows without aggregating distinct hourly keys.
- Found by me, covered by no check: Cross-column operational impossibility, specifically claiming 400+ units of production during an hour with 60.0 minutes of downtime.
- The check I added or would add as a result: `check_output_during_full_downtime` to assert `units_produced == 0` whenever `downtime_min == 60.0`.

## 5. The judgement call

The high motor temperature reading is unusual, not impossible.

- Three explanations, one of which is not a fault:
  1. *Real heavy mechanical load or bearing friction surge (not a fault)*: The motor operated under legitimate heavy mechanical throughput or dense material processing.
  2. *Sensor drift or thermocouple wiring degradation*: Telemetry ADC noise, lead wire oxidation, or thermocouple degradation producing an inflated reading.
  3. *Cooling or ventilation failure*: Auxiliary coolant blower failure or clogged motor housing ventilation intake.
- The single piece of evidence that would separate them: High-frequency secondary SCADA telemetry (motor electrical current draw, vibration spectrum, ambient cabinet temperature) alongside physical inspection logs.
- Who holds it: Shift electrical maintenance technician and plant OT systems engineer.

## 6. What this file can and cannot support

Connect this back to the Week 2 decision boundary in two or three sentences.
Name one calculation that is still safe and one that is not, and say why the
difference follows from the specific faults you found.

This file can support qualitative shift handover discussions to alert supervisors to equipment needing immediate physical inspection (such as Line_C's elevated 105.4 °C motor temperature). 

- **Safe calculation**: Comparing shift-level unit throughput between Line_A and Line_B during normal Day shift hours (excluding the duplicated row 73), or ranking lines by peak observed temperature to direct maintenance attention.
- **Unsafe calculation**: Calculating total 24-hour plant energy efficiency (kWh/unit) or line availability (OEE). Missing energy at Line_B 00:00 distorts power totals, impossible downtime of 91.3 min at Line_C 13:00 corrupts availability percentages, duplicated row 73 artificially inflates Line_A output, and the missing Line_A 08:00 record leaves a gap in 24-hour coverage.
