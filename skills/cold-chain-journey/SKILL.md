---
name: cold-chain-journey
description: "Whether a refrigerated load kept its temperature over a whole journey or window, as evidence. Use for: did it stay cold the whole way; can I accept this load; HACCP or audit evidence; temperature history of a tracking, a trailer, a day; when did it go out of range. For a reading right now use cold-chain-check."
metadata:
  tools: get_tracking_cold_chain, get_asset_cold_chain
---

# Cold chain journey

Answers **"did this load stay in range?"** for one tracking, or one trailer over a window, as
evidence the user can act on. The decision about the load is theirs.

Read `reference/co3-rules.md`, then `reference/thresholds.md`.

## The rule that matters

**An excursion can be confirmed. Its absence is bounded by what CO3 could grade.** Time with
the reefer off or without readings is time nobody measured. "No excursion" is only ever said
together with the hours it covers.

## Procedure

- **A tracking** (by reference): `get_tracking_cold_chain`. It grades each asset and zone over
  the tracking.
- **A trailer over a window** (by plate, VIN or reference; "yesterday", "between 6 and 18"):
  `get_asset_cold_chain` with `from`/`to`. The window is at most 7 days; for longer, split it
  and say so. The trailer must be permanently monitored by the company.

## Reading the result

- Per asset: a `status` (excursion, no excursion, or not graded with the reason). Per zone:
  `graded`, the excursions with `from`, `to`, duration and `worst` (°C above or below the set
  point or range), the share of time in range, and `gradedHours`.
- Compare `gradedHours` with the window. Say how many hours were not graded. The tools do not
  say when or why an hour was not graded (reefer off or no readings); give the total only.
- CO3 makes no allowance for pull-down, and the tools do not show set point changes. When an
  excursion starts soon after the reefer or the trip started, say it may be a cool-down the user
  can check. Keep it in the list.
- Not enabled for the company: say Cold Chain is not enabled in CO3, and stop.

## Output

```
Trailer REEFER1 — 17 Nov, 06:00 to 18:00 (CET)

One excursion, zone 1:
   11:40 → 12:25, 45 min, worst 4.6 °C above the set point (+2.0 °C)

Graded: 11.2 of 12 hours; 0.8 h could not be graded (reefer off or no readings).
Zone 2: not graded, no readings.

Graded as CO3 does: above the set point by more than the company's tolerance, for at least
the minimum duration.
```

- Lead with excursions, then what was not graded, then the grading basis.
- Give numbers for margins and times of day, with the date and time zone.
- Without excursions, use the wording table in `reference/thresholds.md`.

## Hard rules

- Never say the load is safe, compliant, acceptable or may be accepted. Give the evidence; the
  user decides.
- Never say "stayed in range for the whole journey" when any hour was not graded.
- Never re-grade readings yourself or apply a band of your own. CO3's grading is the grading.
- A load running colder than its set point is not an excursion in CO3; do not call it one.

## What this skill cannot answer

- **The product's temperature.** CO3 grades the air in the unit.
- **A window longer than 7 days for a trailer in one call.** Split it.
- **Trailers not permanently monitored by the company.** Their readings are not visible here.
- **Every raw reading.** CO3's portal tools give the grading, not a reading-by-reading
  export.
