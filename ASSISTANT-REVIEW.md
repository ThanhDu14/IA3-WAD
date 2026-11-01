# ASSISTANT-REVIEW — IA#3

## Tool & model
Claude Code v2.1.316 · Claude Opus 5.5 (Claude Pro), fresh session after `/clear`, 2026-11-01 12:28.
The assistant had not been told any verdict. It was asked to read only `brief.md`, `README.md` and the three version files.

## Prompt
```
Review public/versions/a.js, b.js and c.js against brief.md and the "Done means" table in README.md.
Read ONLY brief.md, README.md and the three version files — do not open VERDICTS.md or any other notes.
For each version list every problem you claim, with: the line number, the brief item or Done-means row
it breaks, and what a user would see. Say "no problem found" for anything you checked that is fine.
Do not change any file.
```

## Answer (unedited)
Terminal transcript copied verbatim from the session (the first attempt was interrupted, and `/export` failed because of the filename; the answer starts at "● Review of a.js, b.js, c.js").

```text
 ▐▛███▛█   Claude Code v2.1.316
▝▜██████▀  Opus 5.5 · Claude Pro
 ▝▝   ▝▝   ~/IA3


❯ /clear                                                                        

❯ Review public/versions/a.js, b.js and c.js against brief.md and the "Done     
  means" table in README.md.                                                    
  Read ONLY brief.md, README.md and the three version files — do not open       
  VERDICTS.md or any other notes.                                               
  For each version list every problem you claim, with: the line number, the     
  brief item or Done-means row                                                  
  it breaks, and what a user would see. Say "no problem found" for anything you 
  checked that is fine.                                                         
  Do not change any file.                                                       

  Ran 1 shell command (ctrl+o to expand)
  ⎿  Interrupted · What should Claude do instead?

❯ /export Review public/versions/a.js, b.js and c.js against brief.md and the   
"Done means" table in README.md.                                                
Read ONLY brief.md, README.md and the three version files — do not open         
VERDICTS.md or any other notes.                                                 
For each version list every problem you claim, with: the line number, the brief 
item or Done-means row                                                          
it breaks, and what a user would see. Say "no problem found" for anything you   
checked that is fine.                                                           
Do not change any file.                                                         
  ⎿  Failed to export conversation: ENAMETOOLONG: name too long, open
     '/home/thanhdu/IA3/Review public/versions/a.js, b.js and c.js against
     brief.md and the "Done means" table in README.md.
     Read ONLY brief.md, README.md and the three version files — do not open
     VERDICTS.md or any other notes.
     For each version list every problem you claim, with: the line number, the
     brief item or Done-means row
     it breaks, and what a user would see. Say "no problem found" for anything
     you checked that is fine.
     Do not change any file.'

❯ Review public/versions/a.js, b.js and c.js against brief.md and the "Done     
  means" table in README.md.                                                    
  Read ONLY brief.md, README.md and the three version files — do not open       
  VERDICTS.md or any other notes.                                               
  For each version list every problem you claim, with: the line number, the     
  brief item or Done-means row                                                  
  it breaks, and what a user would see. Say "no problem found" for anything you 
  checked that is fine.                                                         
  Do not change any file.                                                       

● Review of a.js, b.js, c.js against brief.md and the README "Done means" table

  I read only brief.md, README.md and the three version files. I did not run the
  server or tsc and I did not change any file, so every claim below comes from
  reading the code alone.

  Version A (public/versions/a.js)

  Problems claimed

  1. a.js:51-56: breaks brief item 5 and row 2. products.forEach(async (p) => …)
     starts the stock requests, but forEach does not wait for the async
     callbacks. Line 56, stockValue.textContent = formatVnd(total), runs right
     away while total is still 0. What a user sees: in normal mode the stock
     value reads 0 đ and never changes to 12.360.000 đ. The requests do go out
     in parallel; the total is simply written before any of them return.
  2. a.js:52-55: could break row 7 (only if a stock request fails). A rejected
     getStock inside the forEach callback is not caught by the try/catch at line
     57, because that block has already finished. What a user would see:
     Uncaught (in promise) Error: GET /api/stock/… failed in the Console. This
     only happens if some /api/stock/:id returns non-2xx, and I did not check
     server.mjs to see whether any product id does.
  3. Row 8: not checked. Possible checker complaints, depending on how strict
     jsconfig.json is (I did not read it):
     - untyped parameters (n, id, products)
     - firstElementChild and querySelector('.name') could be null (lines 28,
       30, 31)

     The same applies to B and C. Run tsc to find out.

  No problem found
  - Row 1: cards use map in API order, data-id is set and price is formatted.
  - Row 3: the name goes in with textContent (line 30).
  - Row 4: Loading products… shows, then clears.
  - Row 5: the catch clears the list, shows the message and shows Retry. Retry
    hides the button and reloads.
  - Row 6: AbortSignal.timeout(5000) (line 14).
  - empty: shows No products yet. and 0 đ.
  - No console.error.

  Version B (public/versions/b.js)

  Problems claimed

  1. b.js:16: breaks brief item 4 and row 6. fetch('/api/products', { timeout: 
     5000 }) does nothing, because fetch has no timeout option and quietly
     ignores it. What a user sees: in hang mode, Loading products… stays for
     about 30 seconds until the server sends 504. Only then do Could not load 
     products. and Retry appear, not after 5 seconds.
  2. b.js:16: probably breaks row 8. timeout is not a property of RequestInit,
     so // @ts-check should underline it with a message like "Object literal may
     only specify known properties". The same possible null/implicit-any
     warnings as A also apply.
  3. b.js:51-56: minor, only if a stock request fails. The cards stay on the
     page, but the status changes to Could not load products. and Retry appears,
     which is confusing because the products did load. This does not happen in
     any of the five modes unless a stock id returns 404.

  No problem found
  - Row 1: catalogue.replaceChildren() runs at line 42 before append at line 47,
    so cards do not pile up on Retry.
  - Row 2: Promise.all keeps the requests parallel and gives the correct total.
  - Row 3: uses textContent.
  - Row 4: Loading shows, then clears.
  - Row 5: the list is cleared at the start, so no cards show in error. Retry
    works.
  - Row 7: no console.error.
  - empty: Promise.all([]) gives 0 đ, and the status reads No products yet.

  Version C (public/versions/c.js)

  Problems claimed

  1. Row 8: not checked. It may get the same possible strict-mode warnings as A
     (untyped value/id/products; possible null at lines 31, 33, 34). Nothing
     specific to C.
  2. c.js:54 with 58-61: only if a stock request fails. A single failed
     /api/stock/:id rejects the whole Promise.all. The catch then removes all 
     the cards and shows Could not load products., even though the products
     loaded fine. This does not happen in any of the five modes unless a stock
     id returns 404 (not checked against server.mjs).

  No problem found
  - Row 1: fragment in API order, data-id set, price formatted.
  - Row 2: Promise.all keeps the requests parallel and gives the correct total.
  - Row 3: uses textContent.
  - Row 4: Loading shows, then clears.
  - Row 5: the catch clears the list and shows Retry, and Retry restarts from
    step 1.
  - Row 6: AbortSignal.timeout(5000).
  - Row 7: no console.error.
  - empty: No products yet. and 0 đ.

  Summary of claims

  ┌─────────┬─────────┬─────────────────┬───────────────────────────────────┐
  │ Version │  Line   │  Row / brief    │               Claim               │
  │         │         │      item       │                                   │
  ├─────────┼─────────┼─────────────────┼───────────────────────────────────┤
  │ A       │ 51–56   │ Row 2, item 5   │ Total written before stocks       │
  │         │         │                 │ arrive, so it shows 0 đ           │
  ├─────────┼─────────┼─────────────────┼───────────────────────────────────┤
  │ A       │ 52–55   │ Row 7           │ A failed stock request would      │
  │         │         │ (conditional)   │ print Uncaught (in promise)       │
  ├─────────┼─────────┼─────────────────┼───────────────────────────────────┤
  │ B       │ 16      │ Row 6, item 4   │ No real timeout, so it shows      │
  │         │         │                 │ Loading for about 30 s            │
  ├─────────┼─────────┼─────────────────┼───────────────────────────────────┤
  │         │         │                 │ timeout is not a valid fetch      │
  │ B       │ 16      │ Row 8           │ option, so the type checker       │
  │         │         │                 │ should flag it                    │
  ├─────────┼─────────┼─────────────────┼───────────────────────────────────┤
  │ C       │ 54,     │ — (conditional) │ A failed stock request would wipe │
  │         │ 58–61   │                 │  the cards                        │
  ├─────────┼─────────┼─────────────────┼───────────────────────────────────┤
  │         │         │ Row 8           │ Possible null / implicit-any      │
  │ A/B/C   │ various │ (unverified)    │ warnings, depending on            │
  │         │         │                 │ jsconfig.json                     │
  └─────────┴─────────┴─────────────────┴───────────────────────────────────┘

  ---

  About /export: it failed because the text after /export is used as the
  filename, so your whole prompt became the name. Run it with a short name and
  put it outside the repo so no extra file ends up committed, for example
  /export ../IA3-ref/review-session.txt. Then copy the answer above into
  ASSISTANT-REVIEW.md unedited, and add AI-LOG entry #1.

✻ Worked for 39s · done 12:28 PM
```

## Checking each claim

Each claim above, checked against my runs in `VERDICTS.md` (Results tables) and the screenshots in `evidence/`.

### Problems the review claimed

| # | Claim | Version:line | Row/run that checked it | Confirmed / Not real |
|---|---|---|---|---|
| 1 | Total is written before the stock answers arrive, so Stock value shows `0 đ` instead of `12.360.000 đ` (row 2, brief item 5) | A `a.js:51–56` | A row 2, mode `normal` — Stock value `0 đ` while all 8 `/api/stock/*` returned 200 (`evidence/a-row2.png`) | **Confirmed** |
| 2 | The stock requests in A still go out in parallel; only the total is wrong | A `a.js:51–55` | A row 2 run — Network waterfall in `evidence/a-row2.png`: the 8 stock requests start together | **Confirmed** |
| 3 | A failing `/api/stock/:id` would print `Uncaught (in promise)` (row 7, conditional) | A `a.js:52–55` | Extra run (no Done-means row covers it): mode `normal`, `/api/stock/scarf` blocked — Console: `Uncaught … Failed to fetch` at `getStock (a.js:20)`; page shows 8 cards, status empty, Stock value `0 đ`, no Retry | **Confirmed** |
| 4 | Possible row-8 errors (untyped params, possibly-null `firstElementChild` / `querySelector`) | A `a.js:28, 30, 31` | Row 8, `tsc -p jsconfig.json --noEmit --checkJs` — no error points at `a.js` (`evidence/b-row8.png`) | **Not real** |
| 5 | `fetch` has no `timeout` option, so in `hang` Loading stays ~30 s until the 504, not 5 s (row 6, brief item 4) | B `b.js:16` | B row 6, mode `hang` — message + Retry only after 504, Finish 30.28 s (`evidence/b-row6.png`) | **Confirmed** |
| 6 | `timeout` is not a property of `RequestInit`, so the type check flags it (row 8) | B `b.js:16` | B row 8, tsc — `b.js:16:51 error TS2353 … 'timeout' does not exist in type 'RequestInit'` (`evidence/b-row8.png`) | **Confirmed** |
| 7 | Same possible null / implicit-any row-8 warnings as A | B (various) | Row 8, tsc — the only `b.js` error is TS2353 at line 16 (`evidence/b-row8.png`) | **Not real** |
| 8 | If a stock request fails, the cards stay but the status says `Could not load products.` + Retry (confusing; conditional, no row) | B `b.js:51–56` | Extra run: mode `normal`, `/api/stock/scarf` blocked — 8 cards stay, but status reads `Could not load products.` and Retry is shown, Stock value `—`; no `Uncaught` | **Confirmed** |
| 9 | Possible row-8 null / implicit-any warnings | C `c.js:31, 33, 34` | Row 8, tsc — no error points at `c.js` (`evidence/b-row8.png`) | **Not real** |
| 10 | One failed `/api/stock/:id` rejects the whole `Promise.all` and the `catch` wipes all cards (conditional, no row) | C `c.js:54, 58–61` | Extra run: mode `normal`, `/api/stock/scarf` blocked — **0 cards**, `Could not load products.` + Retry, Stock value `—`; no `Uncaught` | **Confirmed** |

### What the review said was fine ("no problem found")

| Version | Rows the review called fine | Rows that checked it | Result |
|---|---|---|---|
| A | 1, 3, 4, 5, 6, empty, no `console.error` (7) | A rows 1, 3, 4, 5, 6, 7, empty — all PASS | **Confirmed** |
| B | 1, 2, 3, 4, 5, 7, empty | B rows 1, 2, 3, 4, 5, 7, empty — all PASS | **Confirmed** |
| C | 1, 2, 3, 4, 5, 6, 7, empty | C rows 1–7, empty — all PASS; row 8 also PASS | **Confirmed** |

Note: the review left row 8 "not checked" for all three versions and only guessed. Its one specific row-8 claim (B, `b.js:16`) was right; its generic guesses for A, B and C were not real.

### Failing rows my table found that the review MISSED

| Version | Row | What I saw | Evidence | Did the review catch it? |
|---|---|---|---|---|
| — | — | none — every failing row in my table (A row 2, B row 6, B row 8) was named by the review, at the right line | — | — |

### Extra run for claims 3, 8, 10 (stock request fails)

No server mode makes a `/api/stock/:id` request fail, so no Done-means row tests these claims. They were checked with an extra run instead of by reading:
mode `normal`, the `/api/stock/scarf` request blocked (same as DevTools → Network → **Block request URL**), page read 2.5 s after load.
Run by Claude Code with headless Chrome (`../IA3-tools/block-stock.mjs`, outside the repo; screenshots `../IA3-tools/<v>-stock-blocked.png`), on a second copy of the original `server.mjs` on port 5051.

| Version | Cards | Status | Stock value | Retry | Console (from the version) |
|---|---|---|---|---|---|
| A | 8 | (empty) | `0 đ` | hidden | `Uncaught … Failed to fetch` at `getStock (a.js:20)` |
| B | 8 | `Could not load products.` | `—` | shown | nothing |
| C | 0 | `Could not load products.` | `—` | shown | nothing |

(The other red lines, `ERR_BLOCKED_BY_CLIENT` and a 404 for `favicon.ico`, are network lines the browser prints itself.)

All three claims are confirmed. None of them changes a verdict: they are outside the Done-means table, and the grading runs only the five server modes.
