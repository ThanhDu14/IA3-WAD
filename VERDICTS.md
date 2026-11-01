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
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| 6 | | | |
| 7 | | | |
| 8 | | | |
| empty | | | |

- Line(s) responsible:
- Verdict: keep / fix / reject — decided by row
- Prediction vs result:
- The one sentence for the assistant:

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
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| 6 | | | |
| 7 | | | |
| 8 | | | |
| empty | | | |

- Line(s) responsible:
- Verdict: keep / fix / reject — decided by row
- Prediction vs result:
- The one sentence for the assistant:

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
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| 6 | | | |
| 7 | | | |
| 8 | | | |
| empty | | | |

- Line(s) responsible:
- Verdict: keep / fix / reject — decided by row
- Prediction vs result:
- The one sentence for the assistant:
