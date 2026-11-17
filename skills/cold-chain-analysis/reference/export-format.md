# The portal's cold chain exports

The CO3 portal offers three cold chain exports, all as CSV files. None of them holds individual
readings.

## Encoding

UTF-8 with a byte-order mark, a first line `sep=,`, then the header row. Records end with LF.
Cells containing `"`, `,` or a line break are quoted per RFC 4180. A text cell that starts with
`= + - @` is prefixed with `'`. Numbers are raw (`12.5`, not `12,5 °C`).

**Blank metric cells mean "not graded", never 0.**

## 1. Trackings (Cold Chain › trackings table)

File `<prefix>_<date>.csv`, one row per tracking:

| Column | Meaning |
| --- | --- |
| `Tracking` | the tracking's reference |
| `Name` | the tracking's name |
| `Status` | `Excursion`, `Compliant`, or a not-graded reason |
| `Assets monitored` | assets CO3 could grade |
| `Total assets` | assets on the tracking |
| `Excursions` | count; blank when nothing could be graded |
| `Hours out of spec` | hours; blank when nothing could be graded |
| `Worst deviation °C` | signed against a set point, a distance against a range |
| `Worst deviation reference` | `set point`, `below range` or `above range` |

## 2. Assets of one tracking or one asset (breakdown table)

File `cold-chain_<tracking or asset>_<date>.csv`, one row per asset:

| Column | Meaning |
| --- | --- |
| `Asset` | plate, or a short id without one |
| `Country` | plate country |
| `Status` | `Excursion`, `Compliant`, or a not-graded reason |
| `In-spec %` | share of graded time in spec |
| `Excursions` | count |
| `Hours out of spec` | hours |
| `Worst deviation °C`, `Worst deviation reference` | as above |
| `Zones` | number of temperature zones |

## 3. Zones of one asset (asset page, zone table)

File `<prefix>_zones_<date>.csv`, one row per zone:

| Column | Meaning |
| --- | --- |
| `Zone` | zone id |
| `Measured channel` | the best channel the zone reported (return air, supply air, …) |
| `Reference` | `set point`, `range`, or blank when not graded |
| `Range min °C`, `Range max °C` | the range's bounds, for a range-graded zone |
| `In-spec %`, `Excursions`, `Hours out of spec`, `Worst deviation °C` | as above |

## What these exports do not contain

- Individual readings, timestamps of readings, positions or addresses.
- Carrier names. Group by carrier only if the user supplies the mapping.
- Set point values or their changes.

Questions that need those (where excursions happen, ferry crossings, set point changes over time,
temperature curves) cannot be answered from the portal's exports.
