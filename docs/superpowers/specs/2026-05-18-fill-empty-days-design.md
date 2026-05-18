# Fill Empty Days — Design Spec

**Date:** 2026-05-18

## Overview

Add a button that auto-fills all empty weekdays (Sun–Thu) from the 1st of the current month through today with randomly generated entry/exit times, respecting an 8–10 hour workday constraint.

## UI

A new button `🎲 מלא ימים ריקים` is added below the existing "view records" button (`#btn-view`). Same visual style as existing buttons (`.btn`, `background: #7d5fff` or similar distinct color).

## Behavior

1. Iterate every calendar day from the 1st of the current month through today (inclusive).
2. Skip Friday (`getDay() === 5`) and Saturday (`getDay() === 6`).
3. Skip any date that already has a record in localStorage — only empty days are filled.
4. For each qualifying empty day, generate:
   - **Enter time:** random integer minute in `[450, 570]` (07:30–09:30), converted to `HH:MM`.
   - **Exit time:** random integer minute in `[max(960, enter+480), min(1140, enter+600)]` (intersection of 16:00–19:00 and enter+8h–enter+10h), converted to `HH:MM`.
5. Save all generated records to localStorage (`submitted: false`).
6. Call `renderRecords()` and `updateBadge()`.
7. Show status: `✅ נוצרו N רשומות` (or `אין ימים ריקים למילוי` if N=0).

## Constraint guarantee

| Enter | Valid exit range |
|-------|-----------------|
| 07:30 | 16:00 – 17:30 |
| 08:00 | 16:00 – 18:00 |
| 09:00 | 17:00 – 19:00 |
| 09:30 | 17:30 – 19:00 |

The intersection always has positive width for any enter in [07:30, 09:30], so the algorithm never produces an invalid range.

## Scope

- No holiday detection — user handles holidays manually.
- No date range picker — always fills current month up to today.
- Does not overwrite existing records under any circumstances.