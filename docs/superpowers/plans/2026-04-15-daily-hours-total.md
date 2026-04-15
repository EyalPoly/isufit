# Daily Hours Total Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Show the total hours worked per day (HH:MM) in the records panel whenever both in and out times are present.

**Architecture:** Add a `calcDuration(inTime, outTime)` pure helper function, then call it inside `renderRecords` to append the duration as a text node to the existing `timesSpan`. No other functions need to change — `saveRecord` and `cancelEdit` already call `renderRecords()`, so the duration re-renders automatically after any edit.

**Tech Stack:** Vanilla JS, single `index.html` — no build step, no test framework. Verification is done by opening the file in a browser.

---

### Task 1: Add `calcDuration` helper and wire it into `renderRecords`

**Files:**
- Modify: `index.html` — `<script>` block

- [ ] **Step 1: Add `calcDuration` after the `sleep` function**

Find the line (around line 236):
```js
function sleep(ms) { return new Promise(r => setTimeout(r, ms)); }
```

Add immediately after it:
```js
function calcDuration(inTime, outTime) {
  if (!inTime || !outTime) return '';
  const [inH,  inM]  = inTime.split(':').map(Number);
  const [outH, outM] = outTime.split(':').map(Number);
  const diff = (outH * 60 + outM) - (inH * 60 + inM);
  if (diff <= 0) return '';
  const h = Math.floor(diff / 60);
  const m = diff % 60;
  return String(h).padStart(2, '0') + ':' + String(m).padStart(2, '0');
}
```

- [ ] **Step 2: Append duration to `timesSpan` in `renderRecords`**

Find these two lines inside the `renderRecords` for-loop (they set `timesSpan` content):
```js
    timesSpan.className = 'times';
    timesSpan.id = 'times-' + date;
    timesSpan.textContent = '🟢 ' + (r.in||'--') + ' \u00a0 🔴 ' + (r.out||'--');
```

Replace with:
```js
    timesSpan.className = 'times';
    timesSpan.id = 'times-' + date;
    timesSpan.textContent = '🟢 ' + (r.in||'--') + ' \u00a0 🔴 ' + (r.out||'--');
    const dur = calcDuration(r.in, r.out);
    if (dur) timesSpan.append('\u00a0 \u23f1 ' + dur);
```

Unicode reference: `\u00a0` = non-breaking space, `\u23f1` = ⏱

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: show daily hours total in records panel"
```

---

### Task 2: Browser verification

Open `index.html` in a browser with DevTools console open.

- [ ] **Scenario 1 — Full day, round hours**

  ```js
  localStorage.setItem('isufit_records', JSON.stringify({'2026-04-10':{in:'08:00',out:'18:00',submitted:false}}));
  location.reload();
  ```
  Open records panel. Expected row: `🟢 08:00 🔴 18:00 ⏱ 10:00`

- [ ] **Scenario 2 — Partial hours**

  ```js
  localStorage.setItem('isufit_records', JSON.stringify({'2026-04-10':{in:'08:00',out:'18:30',submitted:false}}));
  location.reload();
  ```
  Expected row: `🟢 08:00 🔴 18:30 ⏱ 10:30`

- [ ] **Scenario 3 — Missing out-time**

  ```js
  localStorage.setItem('isufit_records', JSON.stringify({'2026-04-10':{in:'08:00',out:'',submitted:false}}));
  location.reload();
  ```
  Expected row: `🟢 08:00 🔴 --` — no ⏱ shown.

- [ ] **Scenario 4 — Duration updates after edit**

  Seed a record with `in:'08:00', out:'17:00'` (duration should show `09:00`). Click ✏️, change out to `18:00`, click ✓. Expected: row now shows `⏱ 10:00`.

- [ ] **Final commit**

  ```bash
  git add index.html
  git commit -m "feat: complete daily hours total feature"
  ```
