# Edit Entry — Design Spec

**Date:** 2026-04-15  
**Project:** isufit attendance clock (`index.html`)

## Overview

Add the ability to edit the in/out times of a saved attendance record directly in the records panel. Submitted records are read-only; editing a pending record updates localStorage. Editing a previously-submitted record reverts it to pending so it gets re-submitted with corrected data.

## Row Layout

Each non-submitted row gains a ✏️ edit icon next to the existing ✕ delete icon:

```
| 2026-04-14  |  🟢 09:00   🔴 17:30  |  ✏️  ✕  |
```

Submitted rows retain only the ✕ (or none — they are already read-only today) and do **not** show ✏️.

## Edit Mode

Clicking ✏️ on a row:

1. Replaces the times span with two `<input type="time">` fields pre-filled with current values (empty if `--`).
2. Replaces ✏️ ✕ with ✓ (save) and ✗ (cancel) icons.
3. Cancels any other row currently in edit mode (only one active at a time).

```
| 2026-04-14  |  🟢 [09:00▾]   🔴 [17:30▾]  |  ✓  ✗  |
```

## Save

Clicking ✓:

- Reads both `<input type="time">` values (HH:MM or empty string).
- Writes updated `in` / `out` to the record in `localStorage`.
- If `submitted` was `true`, sets it to `false` (reverts to pending for re-submission).
- Re-renders the row in normal view.
- Calls `updateBadge()` to refresh the pending count.

## Cancel

Clicking ✗ restores the row to its previous display with no changes to localStorage.

## Constraints

- Submitted rows: no ✏️ icon rendered; read-only.
- Only one row in edit mode at a time; opening a second automatically cancels the first.
- Empty time inputs are stored as `''` (same as a missing punch), consistent with existing behaviour.

## Files Changed

- `index.html` — only file in the project; changes are to `renderRecords()` and a new `editRecord(date)` / `saveRecord(date)` / `cancelEdit(date)` function set.
