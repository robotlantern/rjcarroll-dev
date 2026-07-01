# rjcarroll.dev

Marketing site for RJ Carroll's FileMaker / Claris consulting practice — integrations,
modernization & rescue, and migration off FileMaker. Static, dependency-free HTML/CSS,
hosted on GitHub Pages.

## Structure
- `index.html` — single-page site
- `styles.css` — all styles (CSS variables, responsive)
- `DEPLOY.md` — how to point `rjcarroll.dev` at this repo (DNS + Pages custom domain)

## Run locally
```bash
python3 -m http.server 8092 --directory .
# then open http://localhost:8092/
```

## Deploy
Served from the `main` branch root via GitHub Pages. Push to `main` to publish.
See `DEPLOY.md` for the custom-domain (rjcarroll.dev) cutover steps.
