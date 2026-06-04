# Wayin Double iFrame — Reproduction Test

Simulates a client site that embeds the **wayin-loader** as an outer iframe (iframe #1), which then dynamically injects a second inner iframe (iframe #2) pointing at a Wayin experience.

```
Client page (index.html)
  └── iframe #1  →  wayin-loader.html
                       └── iframe #2  →  https://experience.wayin.com/...
```

## Files

| File | Purpose |
|---|---|
| `index.html` | Simulates the client's product page. Embeds the loader iframe and listens for `postMessage` resize/scroll events. |
| `wayin-loader.html` | The actual wayin-loader. Validates URLs, creates the inner iframe, and reports height back via `postMessage`. |

## Running locally

The loader checks `parentOrigin` against a trusted list that includes `http://localhost:3100`.

```bash
# Install serve once
npm install -g serve

# Serve the folder on the trusted port
serve -p 3100 /path/to/wayin-double-iframe-test
```

Then open `http://localhost:3100` in your browser.

## Running on GitHub Pages

1. Push both files to a GitHub repo and enable Pages (Settings → Pages → Deploy from branch).
2. Your Pages origin will be `https://<username>.github.io` or `https://<username>.github.io/<repo>`.
3. Add that origin to the `trustedOrigins` array in `wayin-loader.html`:

```js
const trustedOrigins = [
    'http://localhost:3100',
    'https://thegoodguys.com.au',
    'https://yourusername.github.io',  // ← add this
];
```

## Configuring the test

Use the **Test Configuration** panel on `index.html` to change values without editing HTML:

| Field | Description |
|---|---|
| Wayin ID | The `id` param passed to the loader (used in postMessage payloads) |
| Loader URL | Path/URL to `wayin-loader.html` |
| Mode | `iframeAttributes` — loader uses `scriptSource` + inner iframe src. `url` — loader injects a single script tag. |
| scriptSource | Must be an `https://*.wayin.com` URL |
| iframeAttributes src | The inner iframe `src` — must be `https://*.wayin.com` |

## What to observe

- **Debug log** (bottom of the page) shows every `postMessage` received from the loader iframe.
- `iframe-resize` messages cause the outer iframe height to auto-adjust.
- `wayin-scroll-top` messages trigger `window.scrollTo` on the client page.
- Open DevTools → Console to see loader-side errors (unauthorized origin, invalid URL, etc.).
