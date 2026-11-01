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

## 2026-11-01 — Review of the original a.js, b.js, c.js (entry #1 for the review)
Tool: Claude Code v2.1.316 (Claude Opus 5.5), fresh session after `/clear`
Asked for: the Step 2 review prompt, verbatim in ASSISTANT-REVIEW.md: review the three versions against brief.md and the Done-means table, reading only brief.md, README.md and the version files, listing line + row + what a user would see, changing no file.
Kept: the full answer, pasted unedited into ASSISTANT-REVIEW.md. Claims: A — a.js:51–56 total written before stock arrives (row 2), a.js:52–55 uncaught rejection if a stock request fails (row 7, conditional); B — b.js:16 `timeout` is not a fetch option (row 6, row 8), b.js:51–56 confusing status if a stock request fails (conditional); C — c.js:54/58–61 one failed stock request wipes the cards (conditional); A/B/C — possible row-8 warnings, unverified.
Changed: — (nothing yet; each claim will be checked by a row in Step 4)
Rejected: — (to be filled after Step 4 if a claim turns out not real)
Checked: not yet — the assistant ran no row and no tsc; it said every claim came from reading alone. I will check each claim with a real row in Step 4.
Process note: the first attempt was interrupted; `/export` failed (ENAMETOOLONG) because the prompt was passed as the filename, so the answer was copied from the terminal instead.
Commit: "Assistant review of the original files (unedited)"

## 2026-11-01 — Results table, verdicts and one sentences for A, B, C
Tool: Claude Code (Claude Opus 5.5)
Asked for: fill the Results tables in VERDICTS.md from my runs (screenshots a-row2, b-row6, b-row8 + my confirmation that every other row passed), find the responsible lines, and draft the verdict, prediction-vs-result and one sentence for each version.
Kept: rows read off my screenshots (A row 2 `0 đ`; B row 6 504 at 30.28 s; B row 8 TS2353 at b.js:16:51; tsc shows no error in a.js or c.js); lines a.js:51–56 and b.js:16; verdicts A fix, B fix, C keep.
Changed: Claude first wrote "the value never changed" (A row 2) and "no error after 5 s" (B row 6), which the screenshots alone do not show — reworded to what the images show. Rows I only confirmed as "pass" are written as the expected Done-means result, not with measured numbers.
Rejected: —
Checked: rows 1–8 + empty on A, B, C, by me in the browser and tsc in the terminal; the one sentences were drafted by Claude with my agreement and will be used as-is in Step 5.
Commit: "Results of the Done-means table + evidence"
