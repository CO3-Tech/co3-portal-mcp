---
name: emissions-report
description: "CO₂ and emissions of transport as CO3 reports them. Use for: CO₂ of a tracking, trip or shipment; carbon footprint; emissions for a period; ESG, scope 3, GLEC or ISO 14083 figures; convert fuel to CO₂. Holds the rules for a carbon number that survives an audit. Fuel and consumption go to trip-efficiency."
metadata:
  tools: get_tracking_co2, get_tracking_distance, search_trackings
---

# Emissions report

Answers **"what were the emissions?"** with CO3's own CO₂e report, which covers one tracking
at a time.

Read `reference/co3-rules.md` first.

## The rule that matters

**The only carbon figure is CO3's report for a tracking.** Never compute CO₂ from fuel,
distance or an emission factor, not even one the user supplies. If they want to multiply
their own factor, they can, and then it is their number.

## Procedure

1. Find the trackings. Use the reference the user gave, or `search_trackings` (by name,
   carrier, plate, dates or `statuses`). For a period, search by start dates and say how many
   trackings it covers. If many match, say how many and ask before reading them all.
2. `get_tracking_co2` for each. It returns `totalCo2Kg`, `totalDistanceKm`,
   `calculationMethod`, `emissionScope` (well-to-wheel or tank-to-wheel), `primaryDataPercent`
   and per-leg figures.
3. `get_tracking_distance` only when the user asks about kilometres and the CO₂ report has
   none.

## Reading the result

- **Method and scope always travel with the figure.** Say whether it is well-to-wheel or
  tank-to-wheel, and the calculation method, next to the number.
- **`primaryDataPercent`** is the share of the figure based on measured (primary) data. Say
  it. A low share means the figure is mostly modelled.
- **A missing total** (`note`: some legs have no figures yet) means no total exists. Give the
  legs that have figures and say the total is incomplete. Never add up a partial total as if
  it were whole.
- **`status: no data`** means CO3 has not calculated it yet; that is the answer.
- **Not enabled** for the company is the answer. Do not estimate instead.

## Output

```
CO₂e — tracking P2026471713
  1,234 kg CO₂e over 612 km
  Method: <method> · Scope: well-to-wheel · 78% primary data

  Leg 1 Wrocław → Poznań  512 kg  (loaded, road)
  Leg 2 Poznań → Berlin   722 kg  (loaded, road)
```

For several trackings, a table and a sum, with how many trackings are summed and how many had
no figure.

## Hard rules

- No emission factors, ever.
- An unavailable report is not permission to substitute one.
- A sum is of the trackings listed, never "your total footprint".
- No comparison against a target, benchmark or last year. CO3 holds no baseline.
- Fuel and distance are not carbon. For fuel, hand over to trip-efficiency.

## What this skill cannot answer

- **Emissions per asset, per lane or for the whole fleet.** CO3's report is per tracking; an
  asset running without a tracking has none.
- **Scope 1/2/3 classification.** It depends on who owns and contracts the vehicle, which CO3
  does not record.
- **Assurance.** CO3's method is stated with the figure; confirming it for an audit is for the
  user and CO3, not for this answer.
