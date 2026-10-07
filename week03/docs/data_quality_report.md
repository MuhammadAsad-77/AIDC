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
| 2 | Line_B at 00:00 (CSV line 56) | `energy_kwh` value is missing / blank |
| 3 | Line_C at 05:00 (CSV line 72) | Non-ISO timestamp format (`13/01/2026 05:00`) and high motor temperature (105.4 °C) |
| 4 | Line_A at 10:00 (CSV line 73) | Exact duplicate of CSV line 13 appended at the end of the file |

## 3. What the checks found

One row per distinct problem. If one fault trips two checks, say so rather than
counting it twice.

| Check | CSV line(s) | Observation (no speculation) | Impossible or surprising? | What should happen next | Who owns it |
|---|---|---|---|---|---|
| `timestamp_parse` | 72 | Timestamp string is `"13/01/2026 05:00"` instead of ISO-8601 (`YYYY-MM-DDTHH:MM`) | Impossible under schema | Correct OT gateway timestamp format string; confirm observation belongs to 05:00 | OT Data Engineer |
| `required_values_complete` | 56 | `energy_kwh` is missing (`NaN`) for Line_B at 00:00 | Impossible under contract | Check physical submeter memory log to recover true energy reading | Electrical Maintenance |
| `exact_row_uniqueness` & `business_key_uniqueness` | 13, 73 | Line_A at 10:00 is duplicated verbatim as record 73 | Impossible (redundant data) | Remove duplicate record after logging transformation in pipeline audit trail | Data Pipeline Owner |
| `downtime_range` | 24 | `downtime_min` is 91.3 min in a 60-minute window | Impossible under contract | Audit shift operator log to confirm if reading was 9.1 min or overflow from previous hour | Shift Supervisor |
| `expected_line_hour_coverage` | Absence at Line_A 08:00 | Line_A has only 23 hourly records; the 08:00 observation is completely missing | Impossible under coverage SLA | Query raw edge buffer for Line_A 08:00 packet to see if network transmission dropped | Plant Network Admin |
| `motor_temperature_review_range` | 72 | `motor_temp_c` is 105.4 °C (> 100.0 °C review threshold) | Surprising (physically possible) | Dispatch technician to check motor cooling, lubrication, and thermocouple calibration | Maintenance Supervisor |

## 4. The gap

- Found by the checks, missed by me: The absence of Line_A at 08:00 (coverage gap). Row-by-row manual scanning misses records that are not in the file.
- Found by me, covered by no check: Cross-column operational impossibility, such as high production occurring during a full 60-minute downtime hour.
- The check I added or would add as a result: `check_output_during_full_downtime` to ensure `units_produced` is 0 whenever `downtime_min` is 60.0.

## 5. The judgement call

The high motor temperature reading is unusual, not impossible.

- Three explanations, one of which is not a fault:
  1. Legitimate high motor load or mechanical friction surge during heavy processing (not a fault).
  2. Thermocouple sensor drift, faulty wiring, or telemetry circuit noise.
  3. Motor cooling blower failure or clogged air ventilation filter.
- The single piece of evidence that would separate them: High-frequency secondary SCADA telemetry (motor vibration, current draw, ambient temperature) alongside physical inspection logs.
- Who holds it: Shift maintenance technician and plant electrical engineering team.

## 6. What this file can and cannot support

Connect this back to the Week 2 decision boundary in two or three sentences.
Name one calculation that is still safe and one that is not, and say why the
difference follows from the specific faults you found.

This file can support qualitative shift handover discussions to alert supervisors to lines needing immediate physical inspection (such as Line_C's elevated motor temperature). However, it cannot support plant-wide energy efficiency calculations or automated failure modeling because missing energy values (Line_B at 00:00), impossible downtime (Line_C at 13:00), duplicated production (Line_A at 10:00), and missing hourly coverage (Line_A at 08:00) distort totals and aggregate statistics.
