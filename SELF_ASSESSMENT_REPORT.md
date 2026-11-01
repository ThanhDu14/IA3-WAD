# SELF_ASSESSMENT_REPORT — IA#3 · Three answers to one brief (CSC13008)

Student: Thanhdu14 · MSSV: 24120288 · Repo: https://github.com/ThanhDu14/IA3-WAD
Final check run on 2026-11-01 (results below).

## Scores

| # | Criterion | Max | I claim | Evidence |
|---|---|---|---|---|
| 1 | VERDICTS.md + evidence/ | 30 | 23 | `VERDICTS.md`: prediction table and 27-row Results table (A, B, C × rows 1–8 + `empty`), responsible lines, verdicts (A fix, B fix, C keep), prediction vs result, one sentence per fix. All 3 FAILs have a screenshot with the page and DevTools or the terminal: `evidence/a-row2.png` (A row 2, `0 đ`), `evidence/b-row6.png` (B row 6, 504 after 30.28 s), `evidence/b-row8.png` (B row 8, TS2353 at `b.js:16:51`). Rows that only fail under `hang` (B row 6) were found. Deductions I expect: see *What I did not manage*. |
| 2 | Every version passes every row | 25 | 25 | Final files: commits `40c1f2b` (Fix A), `e75e75b` (Fix B); C unchanged. Final run on the original `server.mjs`: rows 1–7 + `empty` PASS on A, B and C; `tsc -p jsconfig.json --noEmit --checkJs` reports nothing in `public/versions/*` (only the 2 starter errors in `panel.js`). Parallel check: the 8 `/api/stock/*` requests start within 3 ms of each other on A, B and C, all before the first one finishes. No `innerHTML`, no `console.error`, no `await` in a loop. I also reran A and B in the browser myself before each fix commit. |
| 3 | ASSISTANT-REVIEW.md | 15 | 12 | `ASSISTANT-REVIEW.md`: tool and model, prompt, the answer pasted unedited (fresh session after `/clear`, done before any verdict was given to the assistant; commit `8d6045d`). Claim table (10 claims: 7 confirmed, 3 not real), "no problem found" checks, and a table of missed failing rows (none). The 3 claims no row could test were checked with a real run (one `/api/stock` request blocked), not by reading. |
| 4 | CSR-SSR.md | 10 | 8 | `CSR-SSR.md`: answers built from what I saw in Firefox. Source of `/ssr` has the data, `?v=a` has none. Scarf line quoted from `/ssr`'s source (`Scarf &lt;i&gt;limited&lt;/i&gt;`). JS off: `?v=a` shows no products, `/ssr` still shows products and the stock value. Request counts: `?v=a` 15, `/ssr` 3. |
| 5 | AI-LOG.md | 10 | 8 | `AI-LOG.md`: 8 entries, each added in the same commit as the work it describes (predictions, review, results, claim check, Fix A, Fix B, CSR-SSR). Each has Asked for / Kept / Changed / Rejected / Checked / Commit, and records where the assistant's text went beyond what I saw and was corrected. |
| 6 | Scope + commits | 10 | 10 | `git log`: `starter` (only the 10 starter files) → `Add CLAUDE.md` → predictions → assistant review → results + evidence → claim check → Fix A (only `a.js`) → Fix B (only `b.js`) → AI-LOG → CSR-SSR. `git diff --stat <starter> -- . ':!*.md' ':!evidence'` shows only `public/versions/a.js` and `public/versions/b.js`. Test scripts kept outside the repo in `../IA3-tools/`. No attribution lines in commit messages. |
| | **Total** | **100** | **86** | |

## What I did not manage

- **Many PASS rows have no measured numbers.** For A rows 4–7 + `empty`, B rows 1–5, 7 + `empty`, and C rows 1–7 + `empty`, I only confirmed "pass". The *What I saw* cells give the expected Done-means result, not times or values I wrote down (for example, the second at which C showed the error in `hang`).
- **Two predictions were not made from reading alone.** I ran A row 2 before committing the predictions, and the B row-8 prediction came after I had seen a `tsc` output. Both are disclosed in AI-LOG.md.
- **The assistant wrote more than the guide intended.** It drafted the prediction tables and the one sentences for A and B (with my agreement), and the Q5 sentence in CSR-SSR.md. I reviewed them but did not write them myself.
- **Claims 3, 8 and 10 were checked by Claude's script, not by me in the browser.** The run was real (headless Chrome, `/api/stock/scarf` blocked), but I did not repeat it myself.
- **The B row-6 screenshot was taken after the 30 s 504, not while the page was still loading at second 7,** and *Disable cache* was not ticked in that run.
- **CSR-SSR Q4 has request counts but no Finish times.**

## What I would do differently

- Write down a number for every row while running it (time, value, count), not only for the failing ones, so every *What I saw* cell is my own measurement.
- Commit the predictions before running anything, and keep the setup run of `tsc` out of the prediction step.
- Write the one sentence for each version myself first, then only ask the assistant to check it for ambiguity.
- For conditions with no server mode (a failing stock request), run the check in DevTools (*Block request URL*) myself and take a screenshot, rather than relying on a script.
- Take timed screenshots during the wait (`hang` at second 7), not only at the end.
