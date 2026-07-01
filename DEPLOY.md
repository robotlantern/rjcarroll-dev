# Deploy / DNS for rjcarroll.dev

**Live:** https://rjcarroll.dev — served from the `main` branch root via GitHub Pages.
`www.rjcarroll.dev` 301-redirects to the bare apex. Push to `main` to publish.

## DNS (Porkbun) — current, applied
| Type | Host | Value |
|------|------|-------|
| A | `@` (apex) | `185.199.108.153` |
| A | `@` (apex) | `185.199.109.153` |
| A | `@` (apex) | `185.199.110.153` |
| A | `@` (apex) | `185.199.111.153` |
| CNAME | `www` | `robotlantern.github.io` |

The GitHub Pages custom domain is `rjcarroll.dev` (see the `CNAME` file). GitHub issues a
single Let's Encrypt certificate covering both `rjcarroll.dev` and `www.rjcarroll.dev`.

## Managing Porkbun DNS (use the API, not the browser)
Porkbun's account setting **"Opt In All Domains"** (porkbun.com → Account → API Access) is **ON**,
so DNS is managed via the API (or Codex's Porkbun MCP). Credentials: `~/.codex/secrets/porkbun.env`
(`PORKBUN_API_KEY` / `PORKBUN_SECRET_API_KEY`).

> **Lesson learned:** the Porkbun *browser* DNS editor hides existing records and silently drops
> a new record that conflicts with one it doesn't show. This domain shipped with a parking
> `ALIAS @ → pixie.porkbun.com` and a wildcard `CNAME *.rjcarroll.dev → pixie.porkbun.com`; an
> `A` record can't coexist with an `ALIAS` at the same name, so apex A-records added in the browser
> never saved. The API (`dns/retrieve`) showed the true zone; deleting the ALIAS + wildcard and
> creating the A-records via `dns/create` fixed it.

```bash
# read the true zone
source ~/.codex/secrets/porkbun.env
curl -s -X POST https://api.porkbun.com/api/json/v3/dns/retrieve/rjcarroll.dev \
  -H 'Content-Type: application/json' \
  -d "{\"apikey\":\"$PORKBUN_API_KEY\",\"secretapikey\":\"$PORKBUN_SECRET_API_KEY\"}"
```

## Verify
```bash
dig +short rjcarroll.dev A            # → the four 185.199.10x.153 addresses
curl -sI https://rjcarroll.dev | head -n1
curl -sI https://www.rjcarroll.dev | head -n1   # → 301 to https://rjcarroll.dev/
```
