---
name: delivery-status
description: "Where a delivery is and whether it will be late. Use for: where is my load or tracking; has it arrived; is it late; when will it get there; ETA; missed window; which deliveries are late today. Not for temperature (cold-chain-check, cold-chain-journey), driver hours (driver-hours-check) or why a stop took long (dwell-and-detention)."
metadata:
  tools: search_trackings, get_tracking, get_tracking_eta, investigate_tracking
---

# Delivery status

Answers **"where is it and will it be late?"** for the company's trackings in CO3.

Read `reference/co3-rules.md` first.

## Procedure

**One tracking the user names** (a reference such as `ORDER-2024…`):

1. `investigate_tracking`. It returns details, stops, the latest events, CO3's ETA and the
   assets' last positions in one call. Use it before calling the single tools.
2. If the user asks only about arrival, `get_tracking_eta` is enough.

**A tracking the user describes but does not name** ("the Warsaw delivery", "the one with
PO12345"): `search_trackings` with what they gave (name, reference, carrier, licence plate,
dates). One match: continue as above. Several: list them (reference, first and last stop,
planned times) and ask which.

**"Which deliveries are late today?"**

1. `search_trackings` with `statuses: ["ongoing"]`: under way now, whenever they started. Add
   start dates only when the user means trips starting today. Narrow by carrier, licence plate
   or name if the user gave one.
2. Check at most **10** of them. For each, use `get_tracking_eta` when the company is the
   tracking's shipper. Otherwise use `get_tracking`, which gives every stop's planned window,
   actual arrival and CO3's punctuality.
3. Say how many matched and how many were not checked, and offer to narrow by carrier, licence
   plate or name.
4. Report the late ones first: a missed stop, a stop CO3 marks late, or an ETA after the
   window's end.

## Reading the result

- **Stop status.** `visited` is done. `missed` means the window passed without the arrival CO3
  expects, and it leads the answer. Every stop still ahead carries CO3's `eta`. A stop without
  one carries a `reason`.
- **The ETA.** `eta` is CO3's arrival time. `remainingDrivingMinutes` and `remainingDistanceKm`
  are what is left to that stop. Compare `eta` with `plannedFrom`/`plannedTo`: after
  `plannedTo` is late; inside the window is on time.
- **`calculatedAt`** is when CO3 computed it. Always say it. CO3 recalculates on its own
  schedule, so asking again does not refresh it.
- **`driver`** on a stop means the ETA accounts for the driver's remaining drive time and
  planned breaks. Say so once when the ETA looks long for the distance, because it explains why.
- **No ETA at all** on a finished tracking (every stop visited) is normal, not a problem.
- **"CO3 shows the ETA only to the tracking's shipper"**: say so, and give what the company can
  see from `get_tracking`: stops, actual arrivals and punctuality.
- **Punctuality** from `get_tracking` is CO3's verdict for each visited stop. Report it as
  CO3's.

## Output

```
ORDER-2024… — running late

Stop 1 Wrocław MISSED: the window 08:00–10:00 passed before arrival.

Next, stop 2 Poznań
   ETA         tomorrow 07:20 (window 06:00–08:00)
   Remaining   738 km, about 9 h 30 min of driving
   Asset       PO12345 (PL)
Then stop 3 Berlin, ETA tomorrow 13:05 (window 12:00–16:00)
   ETA calculated 6 minutes ago; it includes the driver's required breaks.
```

For a healthy tracking, one line per stop is enough. For "late today", a short table:
reference, next stop, ETA or arrival against the window, and how late.

## Hard rules

- A missed stop is the headline. The ETA comes second.
- Never present an ETA as live. Say "ETA …, calculated … ago".
- Never print UUIDs. Name assets by licence plate.
- A tracking that is not found: "I can't find a tracking with that reference in this company in
  CO3". Never "your delivery is missing".

## What this skill cannot answer

- **Why a stop was missed.** The data gives the fact, not the cause. Hand over to
  dwell-and-detention, which shows where the asset stood still before the window.
- **An ETA for a carrier.** CO3 shows it to the shipper only; the carrier gets actual arrivals
  and punctuality.
- **Checking more than 10 trackings in one answer.** Narrow the search or go in batches.
- **Searching by stop or customer.** The tracking search takes a name, reference, carrier,
  licence plate, status and dates.
