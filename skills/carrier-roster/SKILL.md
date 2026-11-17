---
name: carrier-roster
description: "Which carriers share assets with the company, how many assets it can see, and in which view. Use for: which carriers are connected; is carrier X connected or invited; consent status; how many assets do we have or monitor permanently; who is our truck shared with; why can't I see an asset or its position."
metadata:
  tools: list_carriers, count_assets, search_assets, get_asset, get_asset_position, get_asset_sharing
---

# Carrier roster

Answers **"whose assets can we see, how many, and on what terms?"** as the portal's Carriers and
Assets tabs show it.

Read `reference/co3-rules.md` first.

## The distinction that matters

The portal's Assets tab has two views, and they overlap:
- **trackings**: the assets available for the company's trackings;
- **permanent**: the assets the company monitors permanently.

An asset can be in one, both or neither. A planner who takes the larger number as "the fleet we
can watch live" will promise something that fails. Always say which view a number is.

## Procedure

- **How many assets:** `count_assets`. It gives `assets` (each asset once) and, per view, the
  count and the per-status counts. Never add the two views up, and never count search rows.
- **Which carriers:** `list_carriers`, filtered by `search` (name or tax id) or `statuses`. It
  gives the consent status, whether the carrier is on CO3, the consent types and when it
  connected.
- **Which assets, in a view:** `search_assets` with `view` (`trackings`, the default, or
  `permanent`). It matches text as CO3 stores it.
- **Why can't I see this asset:**
  1. `get_asset` with the licence plate. It finds a plate without the country and in any
     spacing, in both views. Not found: the asset is not shared with the company in CO3.
  2. Found, but no position: `get_asset_position`. Its answer says why: not under permanent
     monitoring for the company, no active permanent monitoring subscription (when the
     company's asset subscriptions are on), or no telematics device.
- **Who the company's own asset is shared with:** `get_asset_sharing`. It answers only for assets
  the company owns; for an asset a carrier shares in, only that carrier can see it.

## Reading the result

- `matchingRows` in a search counts a row per company sharing an asset. It is not a number of
  assets.
- The per-status counts (active, inactive, degraded, error, pending) say how well CO3 receives
  each asset's data. They are not about the asset's condition.
- A consent "pending", "expired", "revoked", "rejected" or "cancelled" means no visibility
  through that carrier now, whatever the size of its fleet.
- An asset only in the trackings view can be put on a tracking, but has no permanent live
  position.

## Output

```
Assets CO3 shows for the company: 150 (each once)
  Permanent   40   (32 active, 8 inactive)
  Trackings  120   (101 active, 19 error)
  The views overlap; the total counts each asset once.

Carriers: 12 connected (approved), 2 invited (pending), 1 expired.
```

## Hard rules

- Every count names its view. Never call the trackings count "your live fleet".
- Never derive a count from search results.
- Never invent a consent expiry date.

## What this skill cannot answer

- **When a consent expires.** CO3 does not show the receiving company consent expiry dates.
- **A carrier's whole fleet.** The company sees only what is shared with it.
- **Sharing of an asset the company does not own.** Only its owner sees that.
- **Where an asset is now.** That is one question for `get_asset_position`, and needs no skill.
