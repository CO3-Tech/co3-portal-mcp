---
name: cold-chain-check
description: "Whether refrigerated trailers are holding temperature now, and which trackings need cold chain attention. Use for: is the reefer working; temperature check; which trailers are too warm right now; any cold chain issues; door opened away from a stop. For a full journey or audit evidence use cold-chain-journey."
metadata:
  tools: get_reefer_fleet, find_cold_chain_issues, investigate_asset
---

# Cold chain check

Answers **"is anything out of range right now?"**, for the reefer fleet or one trailer. It also
answers **"which trackings need attention?"**.

Read `reference/co3-rules.md` first.

## Two different questions

| The user asks about | Tool | What it is |
| --- | --- | --- |
| Trailers now ("is it cold", "which are too warm") | `get_reefer_fleet` | the latest reading per reefer: temperature, set point, difference, on/off, mode, moving, time of reading |
| One trailer's temperature now | `get_reefer_fleet` | the same reading, found by its licence plate in the list |
| One trailer now, with everything else about it | `investigate_asset` | details, last position, today's movement, and its reefer reading when the company has Cold Chain |
| Trackings ("any cold chain issues", "door openings") | `find_cold_chain_issues` | CO3's grading per tracking: excursions, ungraded zones, unauthorized door openings |

A live reading is not a grade, and a grade is not a live reading. Say which one the answer is.

## Procedure

- **Fleet now:** `get_reefer_fleet`. It lists the warmest against the set point first.
- **One trailer now:** `get_reefer_fleet`, and find the trailer by its licence plate (raise
  `limit` up to 500 for a large fleet). Use `investigate_asset` when the user also wants where it
  is and what it did today. Its reefer section is missing when the company has no Cold Chain;
  `get_reefer_fleet` says so instead. If the user wants the history, hand over to
  cold-chain-journey.
- **Issues across trackings:** `find_cold_chain_issues` (by default only trackings needing
  attention). With `onlyProblems: false` it also lists trackings with no readings at all.

## Reading the result

- **The difference** is the reading minus the set point. Positive is warmer. What counts as out
  of range is the company's tolerance, and the tools do not return it. Say "more than 3 °C above
  the set point (CO3's default tolerance; your company may have set another)" rather than
  "out of range".
- **The time of reading** matters. An old reading is not "now"; say how old it is.
- **Reefer off** is a state to report, not a failure to diagnose.
- **No reading** for a trailer is not a broken reefer. It means CO3 received none.
- In the issues list: an **ungraded zone** comes with its reason, and a **door opening away
  from a stop** is CO3's flag.
- Not enabled for the company: say Cold Chain is not enabled in CO3, and stop.

## Output

```
Reefers now — 3 of 41 more than 3 °C above their set point (CO3's default tolerance)

REEFER1   +7.1 °C (set +2.0, diff +5.1)   on   read 4 min ago, moving
REEFER2   −14.0 °C (set −18.0, diff +4.0) on   read 12 min ago, stopped
REEFER3   +6.0 °C (set +2.0, diff +4.0)   on   read 2 h ago (old reading)

38 others within 3 °C of their set point. 2 reefers off. 1 sent no reading.
```

## Hard rules

- Never say a load is safe or spoiled. Report readings and CO3's flags.
- Never judge a reading against a band of your own when CO3 has a set point.
- A reading colder than its set point is not an excursion in CO3.

## What this skill cannot answer

- **Whether a load stayed in range over a journey.** Use cold-chain-journey.
- **Product temperature.** CO3 reads the air in the unit.
- **Why a reefer is warm** (door, defrost, fault). Report what CO3 shows; the cause is for the
  user to find.
