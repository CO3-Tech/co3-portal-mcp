---
name: driver-hours-check
description: "How much driving time a truck's driver has left before a break, today and this week, as CO3 computes it. Use for: can the driver finish the route or take another drop; how long until the next break; close to or out of hours; tachograph or EU 561 limits. Not for where it stopped or how long it waited (dwell-and-detention)."
metadata:
  tools: get_asset_drive_time, get_tracking_eta
---

# Driver hours check

Answers **"how much longer can it drive?"** for a truck, with CO3's figures. It never answers
"may the driver continue?"; the driver and their tachograph decide that.

Read `reference/co3-rules.md`, then `reference/eu-limits.md`.

## Procedure

1. `get_asset_drive_time` with the plate (or VIN, reference). It returns, per driver record:
   `workingState`, `runsOutFirst` (the capacity that runs out first), the `capacities` (until
   the next break, today, this week, with remaining and limit minutes), `source` and
   `updatedAt`.
2. **"Can it make the next stop of ORDER-…?"** Also call `get_tracking_eta` for that
   tracking. Its ETA already includes the driver's breaks (its `driver` block and planned
   breaks). Report the ETA and its breaks; do not add your own break arithmetic.

## Reading the result

- **Lead with `runsOutFirst`.** It is usually the next break, not the daily limit.
- **Source.** A tachograph source is the tachograph's figures as CO3 received them. "Estimated
  from driving" is CO3's inference from movement: say so, and under 30 minutes left say the
  tachograph must be checked.
- **`updatedAt`**: say how old the figures are. Old figures are not now.
- **"not known yet"** for a capacity means CO3 is waiting for data. It is not zero.
- **A trailer** has no driver hours; the tool says so. Report that and stop.
- **No driver hours** for an asset means CO3 has not received driving data. Do not substitute
  ignition or positions; that is not a drivers' hours calculation.
- **Several records** for one asset (two drivers, two cards) are reported separately, as
  CO3 gives them.

## Output

```
PO12345 — figures from the tachograph, updated 8 min ago
  Until the next break   35 min left (of 4 h 30 min)   ← runs out first
  Today                  3 h 10 min left (of 9 h)
  This week              21 h left (of 56 h)
  Working state: driving

These are CO3's figures from the data it received, not permission to drive; the driver's
tachograph is what counts.
```

## Hard rules

- Never say the driver is "clear", "legal", "allowed" or "fine" to continue, even when a lot of
  time is left.
- Never round in the driver's favour: 74 minutes is "about 1 h 10 min", never "about 1 h 15
  min".
- Never compute driving time from positions or ignition.
- Always end with CO3's caution that the figures are not permission.

## What this skill cannot answer

- **Who is driving**, a driver's name or card, or whether the driver changed. CO3's portal
  figures are per asset.
- **A driver's hours across assets.** The figures belong to this asset's data.
- **Rest periods and working-time limits.** CO3's portal figures cover driving time only.
- **Whether a planned route is legal.** Give the remaining time and the ETA with its breaks;
  the planner decides.
