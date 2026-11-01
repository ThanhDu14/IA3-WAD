# CSR vs SSR — the same catalogue, built in two places

Pages compared: `http://localhost:5050/?v=a` (client-side rendering: the browser builds the cards from JSON) and `http://localhost:5050/ssr` (server-side rendering: `renderSsrPage` in `server.mjs` builds the HTML).
Browser: Firefox, server mode `normal`. Everything below is what I saw on my own runs.

## 1. Where are the product names and the stock value in the page source?

- **`/ssr`** — View page source (`Ctrl+U`) already contains the product data: every product is a `<li class="card">` with its name and price written into the HTML, and the stock value is filled in by the server (`server.mjs:99`, `<output id="stock-value">${vnd(total)}</output>`).
- **`/?v=a`** — the source has **no product data**: no names, no prices. The cards and the stock value are built later by `versions/a.js` after it fetches `/api/products` and `/api/stock/:id` as JSON.

## 2. Why does the scarf read `&lt;i&gt;` in `/ssr`'s source?

What I found in `/ssr`'s source:

```html
<li class="card" data-id="scarf">
        <h2 class="name">Scarf &lt;i&gt;limited&lt;/i&gt;</h2>
        <p class="price">160.000 đ</p>
      </li>
```

The server passes the name through `escapeHtml` (`server.mjs:67`, used at `server.mjs:81`) before putting it into the HTML, so `<` and `>` become `&lt;` and `&gt;`. The browser then shows them as the characters `<i>`, not as an italic tag — the page reads `Scarf <i>limited</i>`.

What it shares with `textContent`: both treat the product name as **text, never as markup**. On the server, escaping does it while building the HTML string; in the browser, `textContent` does it by setting a text node instead of parsing HTML. Either way the angle brackets stay visible (row 3) and data from the API cannot inject tags or scripts.

## 3. JavaScript disabled — what is left of each page?

- **`/?v=a`**: no products are shown at all — the cards are only ever created by JavaScript.
- **`/ssr`**: the products are still there and **the stock value is still shown** — the HTML arrived complete from the server, so nothing needs to run in the browser.

## 4. How many requests before the stock value appears? Which page is seen first?

Network tab, Disable cache, JavaScript on, one reload each:

- **`/?v=a`: 15 requests.** Besides the HTML, CSS and scripts (`panel.js`, `versions/a.js`), the page has to make 1 `/api/products` request and then 8 `/api/stock/:id` requests (one per product, see the waterfall in `evidence/a-row2.png`) before it can show the stock value. These come in a chain: HTML → JS → products JSON → 8 stock requests → total.
- **`/ssr`: 3 requests.** The first HTML response already contains the cards and the stock value, so nothing else is needed before they appear.

A search engine, or a user on a slow phone, sees `/ssr` first: its content is in the first response. On `/?v=a` they get an empty page until the scripts have downloaded and run and the 9 API requests have come back — and a crawler that does not run JavaScript never sees the products at all (question 3).

## 5. One sentence

Render on the server when the content must be there on first load — for search engines, slow phones or JavaScript turned off, as `/ssr` still showed products and stock value with JS off — and render in the browser when the page is interactive and changes after load (loading state, Retry, data refreshed without a full reload), as the version pages do.
