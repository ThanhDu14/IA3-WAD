# VERDICTS — IA#3 · Three answers to one brief

Rows refer to the *Done means* table in `README.md` (rows 1–8) plus the `empty` row.
Expected stock value with the starter data: `12.360.000 đ`.

---

## Version A

### Prediction (from reading alone, before running)

Drafted with Claude Code (Claude Opus 5.5) from reading `a.js` beside `brief.md`; no row had been run yet (see AI-LOG.md).

| Row | Expect | Why (from the code) |
|---|---|---|
| 1 | PASS | `a.js:27–34` maps the array in order and puts all cards in with one `replaceChildren`; price via `formatVnd` (`a.js:10`); status cleared at `a.js:48`. |
| 2 | **FAIL** | `a.js:52–55` uses `products.forEach(async …)`. `forEach` does not wait for the async callbacks, so `a.js:56` writes `total` while it is still `0` and nothing writes it again. Expect `0 đ` instead of `12.360.000 đ`. |
| 3 | PASS | Name goes in with `textContent` (`a.js:30`); no `innerHTML` in the file. |
| 4 | PASS | `Loading products…` is set at `a.js:44` before the fetch at `a.js:46`; `slow` answers in 3 s, inside the 5 s timeout. |
| 5 | PASS | `a.js:15` throws on a non-2xx status, so the `500` goes to `catch` (`a.js:57–60`): cards cleared, message, Retry shown. Retry calls `load()`, which hides the button (`a.js:42`); `busy` is reset in `finally` (`a.js:62`). |
| 6 | PASS | `AbortSignal.timeout(5000)` at `a.js:14` aborts the hanging request after 5 s; the rejection lands in the same `catch`. |
| 7 | PASS | No `console.error`. A failing `getStock` inside the `forEach` (`a.js:53`) would be an uncaught rejection outside the `try`, but in rows 1–6 every stock id exists, so I don't expect it to happen. |
| 8 | PASS | Every element has a JSDoc `@type` cast (`a.js:4–8`, `a.js:28`); with `"strict": false` the possibly-null `querySelector('.name')` is not flagged. |
| empty | PASS | `[]` → `a.js:48` writes `No products yet.`; `total` stays `0` → `0 đ` (it passes here for the same reason row 2 fails). |
| parallel | PASS | `forEach` starts every `getStock` call immediately, so the `/api/stock/*` requests should start together — a fix must keep that. |

**Predicted verdict:** fix — decided by row 2.

### Results
| Row | PASS/FAIL | What I saw (screen / Network / Console / terminal) | Evidence |
|---|---|---|---|
| 1 | PASS | `normal`: 8 cards in API order (T-shirt, Hoodie, Áo sơ mi…, Cap, Tote bag, Socks, Jacket, Scarf), prices like `180.000 đ`, no status text above the cards. | `evidence/a-row2.png` |
| 2 | **FAIL** | `normal`, Disable cache on: Stock value reads **`0 đ`** (expected `12.360.000 đ`) even though all 8 `/api/stock/*` requests returned `200` (Network, initiator `a.js:20`); screenshot taken after Finish 1.76 s, value still `0 đ`. | `evidence/a-row2.png` |
| 3 | PASS | Last card reads `Scarf <i>limited</i>` with the brackets visible, not italic. | `evidence/a-row2.png` |
| 4 | PASS | `slow`: `Loading products…` for about 3 s, then 8 cards, status cleared. | — |
| 5 | PASS | `error`: `Could not load products.` + Retry, no cards. Switched to `normal`, Retry: 8 cards, message and button gone. | — |
| 6 | PASS | `hang`: `Could not load products.` + Retry after about 5 s. | — |
| 7 | PASS | Console during rows 1–6: no `Uncaught …`, nothing printed by the version. | — |
| 8 | PASS | `tsc -p jsconfig.json --noEmit --checkJs`: "Found 3 errors in 2 files", only `panel.js:11`, `panel.js:30` (starter) and `b.js:16`. No error points at `a.js`. | `evidence/b-row8.png` (same run) |
| empty | PASS | `empty`: `No products yet.` and `0 đ`. | — |

- Line(s) responsible: `a.js:51–56` — `products.forEach(async (p) => …)` does not wait for the async callbacks, so line 56 writes `formatVnd(total)` while `total` is still `0`; the stock answers arrive later and nothing writes the total again.
- Verdict: **fix** — decided by row 2.
- Prediction vs result: predicted FAIL on row 2 only; the table confirmed exactly that (row 2 `0 đ`, every other row PASS). Row `empty` shows `0 đ` correctly only because the bug's result happens to be the right answer there.
- The one sentence for the assistant: "Row 2 fails on version A — in mode `normal` the Stock value shows `0 đ` instead of `12.360.000 đ` although all 8 `/api/stock/*` requests return 200, because `products.forEach(async …)` at a.js:51–56 writes the total before any stock arrives; make the total wait for every stock answer while keeping the 8 requests started together (e.g. `Promise.all`, no `await` in a loop), `textContent` only and no `console.error`."

---

## Version B

### Prediction (from reading alone, before running)

Drafted with Claude Code (Claude Opus 5.5) from reading `b.js` beside `brief.md`; version B had not been run yet (see AI-LOG.md).

| Row | Expect | Why (from the code) |
|---|---|---|
| 1 | PASS | `b.js:47` appends `products.map(createCard)` in API order; price via `toVnd` (`b.js:10–12`); status cleared at `b.js:48`. |
| 2 | PASS | `b.js:51` waits for `Promise.all` of every stock request before `b.js:52–53` adds up and writes the total. |
| 3 | PASS | Name goes in with `textContent` (`b.js:31`); no `innerHTML` in the file. |
| 4 | PASS | `Loading products…` at `b.js:44` before the fetch; `slow` answers in 3 s and nothing stops it early. |
| 5 | PASS | `b.js:17` throws on the `500`; `catch` (`b.js:54–56`) shows the message and Retry; the list was already emptied at `b.js:42`, so no cards. Retry re-runs `showCatalogue`, which hides the button and clears the list again. |
| 6 | **FAIL** | `b.js:16` passes `{ timeout: 5000 }` to `fetch`, but `timeout` is not a `fetch` option — it is silently ignored, nothing aborts the request. Expect `Loading products…` for ~30 s until the server's `504`, then the error message. |
| 7 | PASS | No `console.error`, and every rejection is inside the `try`. (The `504` at ~30 s in `hang` is the browser's own network line, not an `Uncaught`.) |
| 8 | **FAIL** | Same line `b.js:16`: `@ts-check` knows `RequestInit` has no `timeout` property, so the object literal should be underlined. |
| empty | PASS | `[]` → `b.js:48` writes `No products yet.`; `Promise.all([])` resolves at once → total `0` → `0 đ`. |
| parallel | PASS | `products.map((p) => loadStock(p.id))` starts every request before `Promise.all` waits. |

**Predicted verdict:** fix — decided by row 6 (row 8 points at the same line). Row 6 is the one only the `hang` condition shows.

### Results
| Row | PASS/FAIL | What I saw (screen / Network / Console / terminal) | Evidence |
|---|---|---|---|
| 1 | PASS | `normal`: 8 cards in API order, prices like `180.000 đ`, status line empty. | — |
| 2 | PASS | `normal`: Stock value `12.360.000 đ`. | — |
| 3 | PASS | Last card reads `Scarf <i>limited</i>`, brackets visible, not italic. | — |
| 4 | PASS | `slow`: `Loading products…` for about 3 s, then 8 cards, status cleared. | — |
| 5 | PASS | `error`: `Could not load products.` + Retry, no cards. Switched to `normal`, Retry: 8 cards, message and button gone. | — |
| 6 | **FAIL** | `hang`: `Could not load products.` + Retry appeared only when the server gave up, not at 5 s. Network: `products` **504 Gateway Timeout**, initiator `b.js:16 (fetch)`, **Finish: 30.28 s**. | `evidence/b-row6.png` |
| 7 | PASS | Console during rows 1–6: no `Uncaught …`, nothing printed by the version. | — |
| 8 | **FAIL** | `tsc`: `public/versions/b.js:16:51 - error TS2353: Object literal may only specify known properties, and 'timeout' does not exist in type 'RequestInit'.` | `evidence/b-row8.png` |
| empty | PASS | `empty`: `No products yet.` and `0 đ`. | — |

- Line(s) responsible: `b.js:16` — `fetch('/api/products', { timeout: 5000 })`: `fetch` has no `timeout` option, so it is ignored and nothing aborts the request (row 6); the same unknown property is what tsc reports (row 8).
- Verdict: **fix** — decided by row 6 (row 8 points at the same line).
- Prediction vs result: predicted FAIL on rows 6 and 8; the table confirmed both, every other row PASS. (Row-8 prediction was not from reading alone — see AI-LOG.)
- The one sentence for the assistant: "Rows 6 and 8 fail on version B — in mode `hang` `Could not load products.` and Retry appear only after the server's 504 at 30.28 s instead of after 5 s, and tsc reports `b.js:16:51 TS2353 'timeout' does not exist in type 'RequestInit'`, because `fetch` has no `timeout` option; abort the products request after 5 s with a real signal (e.g. `AbortSignal.timeout(5000)`) and change nothing else — keep the stock requests in parallel with `Promise.all`, `textContent` only and no `console.error`."

---

## Version C

### Prediction (from reading alone, before running)

Drafted with Claude Code (Claude Opus 5.5) from reading `c.js` beside `brief.md`; version C had not been run yet (see AI-LOG.md).

| Row | Expect | Why (from the code) |
|---|---|---|
| 1 | PASS | `c.js:30–37` builds the cards in a `for…of` over the array (API order) into a fragment and puts them in with one `replaceChildren`; price via `money` (`c.js:12`); status cleared at `c.js:51`. |
| 2 | PASS | `c.js:54` waits for `Promise.all`; the `forEach` at `c.js:56` is synchronous (no `async`), so the total is complete before `c.js:57` writes it. |
| 3 | PASS | Name goes in with `textContent` (`c.js:33`); no `innerHTML` in the file. |
| 4 | PASS | `Loading products…` at `c.js:47` before the fetch; `slow` (3 s) is inside the 5 s timeout. |
| 5 | PASS | `c.js:17` throws on the `500`; `catch` (`c.js:58–61`) empties the list, shows the message and Retry. Retry calls `start`, which hides the button (`c.js:45`). |
| 6 | PASS | `AbortSignal.timeout(5000)` at `c.js:16` aborts the hanging request after 5 s; the rejection lands in the same `catch`. |
| 7 | PASS | No `console.error`, and every `await` is inside the `try`. |
| 8 | PASS | Every element has a JSDoc `@type` cast (`c.js:5–9`, `c.js:31`); `fetch` gets only a real option (`signal`). |
| empty | PASS | `[]` → `c.js:51` writes `No products yet.`; `Promise.all([])` → total `0` → `0 đ`. |
| parallel | PASS | `products.map((p) => fetchStock(p.id))` at `c.js:54` starts every request before waiting. |

**Predicted verdict:** keep — I expect no row to fail; the table has to confirm it.

### Results
| Row | PASS/FAIL | What I saw (screen / Network / Console / terminal) | Evidence |
|---|---|---|---|
| 1 | PASS | `normal`: 8 cards in API order, prices like `180.000 đ`, status line empty. | — |
| 2 | PASS | `normal`: Stock value `12.360.000 đ`. | — |
| 3 | PASS | Last card reads `Scarf <i>limited</i>`, brackets visible, not italic. | — |
| 4 | PASS | `slow`: `Loading products…` for about 3 s, then 8 cards, status cleared. | — |
| 5 | PASS | `error`: `Could not load products.` + Retry, no cards. Switched to `normal`, Retry: 8 cards, message and button gone. | — |
| 6 | PASS | `hang`: `Could not load products.` + Retry after about 5 s. | — |
| 7 | PASS | Console during rows 1–6: no `Uncaught …`, nothing printed by the version. | — |
| 8 | PASS | Same `tsc` run: no error points at `c.js` (only `panel.js` ×2 and `b.js:16`). | `evidence/b-row8.png` (same run) |
| empty | PASS | `empty`: `No products yet.` and `0 đ`. | — |

- Line(s) responsible: none — no row failed.
- Verdict: **keep** — every row (1–8 + empty) passed.
- Prediction vs result: predicted keep with no failing row; the table confirmed it.
- The one sentence for the assistant: none needed — version C is kept as it is.
