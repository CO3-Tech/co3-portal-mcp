# How CO3 grades temperature

The cold chain tools already apply these rules. This file is for explaining a result, never
for re-grading readings yourself.

## What counts as an excursion

- **Against a set point** (when the reefer reports one): only readings **above** the set point
  by more than the company's tolerance count. The CO3 default tolerance is 3 °C. A load running
  too cold is never an excursion.
- **Against a temperature range** (when no set point is reported and the company configured a
  range for the asset): readings outside the range on either side count.
- A breach is an excursion only when it lasts at least the company's minimum duration. The
  CO3 default is 30 minutes. Shorter blips still show in `worst`, but are not excursions.
- Grading uses the company's settings **today**, applied to past readings.
- It grades the air temperature in the unit, not the product.

## What CO3 does not allow for

- **Pull-down.** After a set point is lowered, the cool-down can show as an excursion. The tools
  do not show set point changes; when an excursion starts soon after the reefer or the trip
  started, say it may be a cool-down. Do not remove it.
- **Defrost cycles and door openings** are not filtered out of the readings.

## What "graded" covers

- `gradedHours` is the time CO3 could grade: it excludes time with the reefer off and time
  without readings. Each reading covers at most 60 minutes, so hourly readings still look
  fully covered.
- Compare `gradedHours` with the graded window (the tracking's or the one asked for). The
  difference is time nobody measured.
- A zone that is not graded comes with its reason: no readings, reefer off, no set point and
  no range, and so on. Report the reason as the answer for that zone.

## The wording

| Result | Say |
| --- | --- |
| Excursions | "N excursion(s): from … to …, worst X °C above the set point" |
| No excursion, graded hours ≈ window | "No excursion in the X graded hours from … to …" |
| No excursion, graded hours < window | "No excursion in the X of Y hours CO3 could grade; Z hours were not graded (reefer off / no readings)" |
| Zone not graded | "Zone N was not graded: <reason>" |

Never "stayed in range", "safe", "compliant", "acceptable" or "can be accepted".
