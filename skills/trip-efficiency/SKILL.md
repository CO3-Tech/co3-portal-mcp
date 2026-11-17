---
name: trip-efficiency
description: "How much fuel a truck burned and its consumption in l/100 km over a window. Use for: fuel used; consumption or fuel economy; how far did it drive; is this truck thirsty; compare fuel across periods. Gives no CO₂: for carbon use emissions-report."
metadata:
  tools: get_asset_fuel, get_asset_movement
---

# Trip efficiency

Answers **"how much fuel did it use, and how far did it go?"** for one asset over a window,
from its engine's counters as CO3 received them.

Read `reference/co3-rules.md` first.

## Procedure

1. `get_asset_fuel` with the plate and `from`/`to` (at most 7 days; default the last 24
   hours). It returns `fuelUsedLitres`, `distanceKm`, `litresPer100Km` and
   `stoppedPercentOfWindow`.
2. **Comparing periods:** one call per period, the same length each. Longer than 7 days: split
   it and say so.
3. **"Where did it stand still?":** `get_asset_movement` for the stops in the same window. The
   fuel counters cannot attribute litres to a stop, and the tools do not say whether the engine
   ran while it stood.

## Reading the result

- **Use the figures as given.** Do not recompute l/100 km from positions.
- **Always state the stopped share.** Consumption is fuel over distance. Standing with the
  engine running burns fuel and adds no kilometres, so a high stopped share can raise l/100 km
  for a reason unrelated to driving. The tools do not say whether the engine ran: present it as a
  possibility, not as what happened.
- **No figure, with a note:**
  - a counter going backwards means a reset or a device change;
  - too many positions means the window is too long;
  - no fuel counter means a trailer or no engine data.

  Each means fuel use is not known. It is never zero, and never estimated. Suggest a shorter
  window where the note says so.
- Only permanently monitored trucks with detailed telemetry have counters.

## Output

```
PO12345 — 10–16 Nov (7 days)
  412 L over 1,287 km → 32.0 l/100 km
  Stopped 38% of the window. If the engine ran while it stood, that raises l/100 km;
  CO3's figures do not show whether it did.
```

## Hard rules

- Never convert fuel into CO₂, with any factor. For carbon, hand over to emissions-report.
- Never compare drivers on consumption. Load, route, weather and waiting differ, and the data
  does not separate them.
- Never fill a missing figure with an estimate.

## What this skill cannot answer

- **CO₂.** See emissions-report.
- **Fuel per stop or per tracking.** The counters give a window total.
- **Refuelling events or tank level.** The tool reports fuel used, not tank contents.
- **Assets without detailed telemetry,** and trailers.
