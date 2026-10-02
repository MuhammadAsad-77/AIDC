
# Data suitability checklist

| Check | Rating | Evidence or unresolved question |
|---|---|---|
| Source is recorded | Yes | The source is `data/raw/plant_shift_log.csv`. |
| Licence or permitted use is recorded | Yes | The README permits coursework use. |
| Data status is clear | Partly | It is a teaching dataset; production status is not established. |
| Personal or sensitive fields are identified | Yes | No personal fields are present in the supplied columns. |
| Unit of observation is understood | Yes | One row represents one line-hour observation. |
| Variables match the proposed decision | Partly | Operational measurements are present, but no verified failure outcome exists. |
| Time span represents the intended use | No | The file covers one day only. |
| Outcome or target is available if required | No | No failure or inspection outcome is recorded. |
| Missing values and duplicates are assessed | Yes | One missing downtime value and one duplicate row were found. |
| Impossible or implausible values are assessed | Yes | One negative energy value and one temperature above 100°C were found. |
| Dataset size is manageable this semester | Yes | The dataset contains 73 rows and 8 columns. |
| Stakeholder or domain evidence is available | Partly | Candidate roles are identified, but domain confirmation is unresolved. |
| Required permission or ethics review is known | Unknown | Confirm requirements with the data owner. |
| Client, cross-border, or confidentiality obligations are identified | Unknown | No obligations are documented in the supplied evidence. |
| Human review and non-use conditions are defined | Partly | Maintenance staff should review any indication; autonomous action is not permitted. |

## Preliminary decision

**Change question**

## Justification

The proposed user is the maintenance supervisor, who would decide whether to
inspect a production line or schedule maintenance before shift handover. The
dataset is manageable and its unit of observation is understood: each row
represents a line-hour observation. It also contains useful operational fields
such as timestamp, shift, energy, temperature, and downtime. However, it cannot
currently support a claim that it predicts machine failures. The file covers
only one day, has no verified failure or inspection outcome, and does not include
approved engineering limits. Quality review found one duplicate row, one
missing downtime value, one negative energy value, and one temperature above
100°C. These issues require confirmation rather than silent correction. The
most appropriate decision is therefore to change the question from predicting
failures to documenting line conditions and identifying evidence needed for a
future feasibility study. Any operational use must remain advisory and subject
to human review. Before proceeding, the data owner and an engineer must confirm
the source status, permissible use, measurement limits, and whether longer
historical data with verified outcomes can be obtained.
````