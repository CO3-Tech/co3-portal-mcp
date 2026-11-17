---
name: dwell-and-detention
description: "Where an asset's time went: how long it stood still, when and where. Use for: how long did the truck wait at the site or dock; where did it stop; check a detention or waiting-time invoice; why was a stop missed or slow; reconstruct an asset's day; which trucks are at our depot."
metadata:
  tools: get_asset_movement, get_tracking, get_tracking_events, find_assets_at_locations
---

# Dwell and detention

Answers **"how long did it stand still, and where?"** for one asset over a window, or along one
tracking's stops.

Read `reference/co3-rules.md` first.

## Procedure

**An asset over a window** ("how long did PO12345 wait yesterday"):
1. `get_asset_movement` with the plate and `from`/`to` (at most 7 days; default the last 24
   hours). It returns total moving and stopped minutes and every stop of at least
   `minStopMinutes` (default 5) with start, end, duration and where it began.
2. Lower `minStopMinutes` only when the user asks about short stops.

**Along a tracking** ("how long did it wait at stop 2 of ORDER-…", "why was stop 1 missed"):
1. `get_tracking` for the stops: address, planned window, actual arrival and departure, and its
   assets.
2. For what happened before or at a stop, `get_asset_movement` with the tracking's asset over
   that stretch (for example from the trip's start to the end of the stop's window). It works for
   any window up to 7 days, however long the tracking.
3. `get_tracking_events` gives a tracking's latest arrivals, departures and stops first, 30 by
   default and at most 200 with `limit`. On a long tracking the early ones are cut off; then use
   step 2.
4. Compare: arrival against the window, time at the stop (arrival to departure), and the stopped
   periods before the window.

**Assets at the company's locations now** ("which trucks are at the Poznań depot"):
`find_assets_at_locations`, optionally with the location or plate. It needs asset search
enabled in CO3.

## Reading the result

- A stop has a **start position**, not an address. Along a tracking, CO3 links the arrival
  and departure to the stop it geofences; over a window, it does not. Say which you are giving.
- Stopped minutes are the asset standing still. They are not loading, queuing or a break;
  the data cannot tell those apart.
- A window with no movement at all is one long stop. Say the asset did not move, not "X
  hours of waiting".
- No events in a window means CO3 received none. Do not report zero waiting.

## Output

```
PO12345 — 16 Nov, 06:00–18:00 (CET)
Moving 6 h 10 min · stopped 5 h 50 min

Stops of 5 min or more:
  07:42–09:58  2 h 16 min   started at 52.401, 16.925
  12:10–12:55  45 min       started at 52.229, 21.012
```

Along a tracking:

```
Stop 2 Poznań — window 08:00–10:00
  Arrived 09:41 (in the window) · left 12:03 · 2 h 22 min at the stop
```

Times are local where the asset was, labelled with the zone once; say UTC when the place is
unclear.

## Hard rules

- Never call a period "detention". Detention is a contract term with a free period and a
  rate. Report the standing time; the user applies their contract.
- Never say why the asset stood still. A late start, a long stop or a slow drive is an
  observation; at most say it is "consistent with" something.
- Do not compare drivers or assets on standing time. A long wait is usually the site's, and
  the data cannot tell.
- Do not name a customer or site from coordinates. Give the coordinates or CO3's stop name.

## What this skill cannot answer

- **Whether the wait was agreed or billable.** A booked slot and a delay look the same.
- **Fleet-wide rankings** ("which truck waited longest last week"). Movement is read one asset
  at a time.
- **Fuel burned while standing.** Hand over to trip-efficiency.
- **Driving time against the regulations.** Hand over to driver-hours-check.
