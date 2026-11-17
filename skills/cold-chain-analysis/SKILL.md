---
name: cold-chain-analysis
description: "Patterns across many trackings or trailers from a cold chain CSV exported from the CO3 portal. Use for: which trackings or trailers have the most excursions or hours out of spec; how much is not graded; is it improving between two exports; summarise this export. Needs the file. For one tracking or trailer use cold-chain-journey."
---

# Cold chain analysis

Analyses a cold chain export the user downloaded from the CO3 portal, to find patterns across
trackings, assets or zones. It reads no live data.

Read `reference/co3-rules.md`, then `reference/export-format.md`.

## Never read the export into the conversation

Exports can have thousands of rows. Load the file with code: Python with pandas or the `csv`
module, or what the environment has. Skip the `sep=,` line and the byte-order mark. Read the
header and decide which of the three exports it is. Then compute. Show only results, never
pages of rows.

If the environment cannot run code, say so. A file this size cannot be analysed by reading it.

## Three rules that decide whether the analysis is worth anything

1. **Blank is not zero.** A blank metric cell is "not graded". Count graded and not-graded rows
   separately, and say how many were not graded before any average.
2. **Graded share bounds every conclusion.** "Compliant" covers only the time CO3 could grade.
   Always report what share of rows (and, for trackings, of assets) was graded.
3. **Hours beat counts.** One long excursion matters more than three blips. Rank by hours out of
   spec, and show the count next to it.

## What to look for

- The trackings or assets with the most hours out of spec, and their worst deviation.
- How excursion hours spread: a few assets with most of them, or spread thin.
- Not-graded rows by reason (from `Status`). Many "no readings" rows on the same assets is a data
  problem worth naming, not a cold chain result.
- Set point vs range grading (zones export), and how far range-graded zones sit outside.
- **Two exports of different periods:** compare like with like (same export type) and give both
  graded shares. If coverage changed, say the comparison is limited by it.

## Reporting

- Lead with the answer to the question, then the numbers behind it, then the coverage.
- Name assets by plate and trackings by reference, as the export has them.
- Offer the computed table as a CSV the user can download. Never publish or upload it
  anywhere.

## Hard rules

- Never say a load, a carrier or a period was safe or compliant. Report what CO3 graded.
- Never compute a cause ("this carrier's reefers are faulty"). Report the pattern; at most
  "consistent with".
- Never invent values for blank cells.

## What this skill cannot answer

- **Where or when excursions happen** (sites, routes, sea crossings, time of day). The portal
  exports have no readings, positions or times.
- **Set point changes or pull-down.** Not in the exports.
- **Per-carrier results** unless the user provides which tracking or asset belongs to which
  carrier.
- **Anything live.** For now, use cold-chain-check.
