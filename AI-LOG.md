# AI-LOG.md — IA#3 Three answers to one brief (CSC13008)

Rows refer to the Done means table in README.md (1–8) plus the `empty` row.

## 2026-11-01 — Prediction for version A (reading only)
Tool: Claude Code (Claude Opus 5.5)
Asked for: walk through public/versions/a.js beside brief.md, then write the version A prediction in VERDICTS.md — from reading alone, before any row is run.
Kept: row-by-row prediction with the line numbers; predicted FAIL on row 2 (`forEach(async …)` at a.js:52–56 writes the total before any stock arrives); predicted verdict fix.
Changed: —
Rejected: —
Checked: not yet — no row run at this point; the Results table will confirm or correct it.
Commit: (with the B and C predictions) "Predictions from reading, before running"
Note: before committing this prediction I had already run row 2 on A (evidence/a-row2.png) — it showed `0 đ`, as predicted.

## 2026-11-01 — Predictions for versions B and C (reading only)
Tool: Claude Code (Claude Opus 5.5)
Asked for: write the version B and C predictions in VERDICTS.md from reading b.js and c.js beside brief.md, before running them.
Kept: B — predicted FAIL on row 6 and row 8, both from `fetch('/api/products', { timeout: 5000 })` at b.js:16 (`timeout` is not a fetch option, so nothing stops a hanging request), verdict fix. C — no failing row predicted, verdict keep.
Changed: —
Rejected: —
Checked: not yet — no row run on B or C. Disclosure: while setting up, Claude had run the starter's tsc check once in a scratch copy to confirm the row-8 command works; its output named only b.js, so the B row-8 prediction was not made from reading alone.
Commit: "Predictions from reading, before running"
