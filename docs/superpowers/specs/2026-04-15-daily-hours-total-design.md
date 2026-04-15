# Daily Hours Total — Design Spec

**Date:** 2026-04-15  
**Project:** isufit attendance clock (`index.html`)

## Overview

Show the total hours worked per day in the records panel. When a record has both an in-time and an out-time, append the duration in `HH:MM` format to the times span.

## Display

```
| 2026-04-14  |  🟢 08:00  🔴 18:30  ⏱ 10:30  |  ✏️  ✕  |
```

- Duration is shown only when both `in` and `out` are non-empty and `out > in`.
- When either time is missing, or `out ≤ in` (data error), no duration is shown.

## Logic

**New helper: `calcDuration(inTime, outTime)`**

- Returns `''` if either argument is empty/falsy.
- Parses both strings as minutes-since-midnight.
- Returns `''` if the difference is ≤ 0.
- Returns a `HH:MM` string otherwise (e.g., 630 min → `'10:30'`).

**`renderRecords` update**

After setting `timesSpan.textContent`, call `calcDuration(r.in, r.out)`. If the result is non-empty, append `'  ⏱ ' + duration` as a text node to `timesSpan`.

No changes needed to `editRecord`, `saveRecord`, or `cancelEdit` — they all call `renderRecords()` after any state change, so the duration re-calculates automatically.

## Files Changed

- `index.html` — add `calcDuration` helper; update `renderRecords` to append duration.
