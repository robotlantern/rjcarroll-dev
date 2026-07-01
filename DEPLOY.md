# Deploy / DNS cutover for rjcarroll.dev

The site is live on GitHub Pages at the project URL as soon as Pages is enabled:
**https://robotlantern.github.io/rjcarroll-dev/**

To serve it at **https://rjcarroll.dev**, two things are needed: (1) DNS records at Porkbun,
and (2) the custom domain set on the Pages site. DNS is a live-domain change, so it is left
here as a ready-to-apply step rather than done unattended.

## Current DNS (as of cutover prep — parked, safe to replace)
- `rjcarroll.dev` A → `44.227.76.166`, `44.227.65.245`  (Porkbun parking)
- `www.rjcarroll.dev` CNAME → `pixie.porkbun.com`  (Porkbun parking)
- No MX / email records — nothing to preserve.

## Step 1 — Porkbun DNS records to set
Replace the parking records with GitHub Pages records:

**Apex `rjcarroll.dev` — four A records (delete the two parking A records first):**
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
Optional IPv6 (AAAA):
```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

**`www` subdomain — CNAME (replace the parking CNAME):**
```
www.rjcarroll.dev  CNAME  robotlantern.github.io.
```

Porkbun writes go through the guarded MCP wrapper (see
`~/work/admin/domains/agent-domain-dns-runbook.md`): snapshot first, switch to the
write ("muddy") wrapper for the change window, apply minimal records, then revert to read-only.

## Step 2 — set the Pages custom domain
After DNS is in place:
```bash
gh api -X PUT repos/robotlantern/rjcarroll-dev/pages -f cname=rjcarroll.dev
```
This also writes a `CNAME` file to the repo. Once the certificate provisions (can take a few
minutes to an hour), enable HTTPS enforcement:
```bash
gh api -X PUT repos/robotlantern/rjcarroll-dev/pages -F https_enforced=true
```

## Verify
```bash
dig +short rjcarroll.dev A          # expect the four 185.199.10x.153 addresses
curl -sI https://rjcarroll.dev | head -n1
```
