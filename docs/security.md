# Security Hardening

In-repo changes: security headers (`public/_headers`), `/.well-known/security.txt`,
and a canonical domain (`amanthakur.online`) in `sitemap.xml`, `robots.txt`, and
`index.html`. The DNS records below are **manual action items** — they must be added
at your DNS provider (registrar or Netlify DNS) and cannot be shipped in this repo.

## DNS records to add (zone: amanthakur.online)

No MX record exists and none is needed — `amanthakur.online` does not receive mail
(you use a Gmail address). Publishing `-all` SPF and `p=reject` DMARC ensures nobody
can ever spoof `@amanthakur.online`.

| Name | Type | Value |
|---|---|---|
| `@` | TXT | `v=spf1 -all` |
| `_dmarc` | TXT | `v=DMARC1; p=reject; sp=reject; adkim=s; aspf=s` |
| `@` | CAA | `0 issue "letsencrypt.org"` |

Notes:

- **SPF**: `-all` hard-fails all senders. Keep it — there is no legitimate mail from
  this domain.
- **DMARC**: `p=reject` tells receivers to drop unauthenticated mail. `sp=reject`
  covers subdomains. Optionally add `; rua=mailto:099amanthakur1@gmail.com` to receive
  aggregate reports (Google aggregates DMARC reports from any address; not required).
- **CAA**: Netlify provisions certificates via Let's Encrypt, so
  `0 issue "letsencrypt.org"` is required for auto-renewal to keep working. Do NOT add
  a restrictive CAA that omits Let's Encrypt. For a stricter lock-down you may append
  Netlify's account URI:
  `0 issue "letsencrypt.org;accounturi=https://acme-v02.api.letsencrypt.org/acme/acct/54403714"`
- Records must propagate before Let's Encrypt can validate; allow up to a few hours.

## Canonical domain

`amanthakur.online` is canonical. All SEO references (sitemap, robots, canonical,
Open Graph, JSON-LD) now point there.

Action item: make `amanthakur.dev` 301-redirect to `amanthakur.online` so search
engines collapse to one domain. If both domains point at the same Netlify site, set
`amanthakur.online` as the primary domain in **Site configuration → Domain management**
— Netlify then 301-redirects the alias (`amanthakur.dev`) to it automatically.

## Security headers

Defined in `public/_headers` (Netlify format, applied to every response):

- `Content-Security-Policy` — locked to self-hosted assets + Google Fonts;
  blocks inline script, iframes, and mixed content.
- `X-Frame-Options: DENY` / `frame-ancestors 'none'` — no framing.
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Permissions-Policy` — disables camera, mic, geolocation, payment, USB, and
  tracking cohorts.
- `Strict-Transport-Security` — matches Netlify's default HSTS.

## Vulnerability disclosure

`/.well-known/security.txt` is served with contact info for reporting issues.
