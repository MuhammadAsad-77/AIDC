# Problem Brief

## Decision-ready problem statement

The **maintenance supervisor** needs to identify production lines that may require attention **before the next shift handover**, so that maintenance staff can inspect the relevant equipment and reduce the risk of unplanned downtime.

The decision is whether to **prioritize a line for inspection or maintenance review** based on available operational evidence.

The immediate action window is **before shift handover / during the next maintenance planning period**.

The consequence of a wrong decision is important: unnecessary inspections can consume maintenance time and disrupt production, while missed problems may contribute to unexpected downtime, equipment damage, or production delays.

## Current evidence

The supplied dataset contains hourly observations for production lines, including timestamp, line, shift, energy use, motor temperature, and downtime-related information.

However, the supplied file covers only a single day and does not contain a verified machine-failure or inspection outcome.

Therefore, the current evidence should not be treated as sufficient to make reliable machine-failure predictions.

## Initial decision

The current data should be used to assess operational patterns and define what additional evidence is required before making a machine-failure prediction claim.
