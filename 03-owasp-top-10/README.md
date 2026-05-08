# 03 · OWASP Top 10

Per-category notes following the [OWASP Top 10 (2021)](https://owasp.org/Top10/) order.

Each category gets its own folder with:

- `README.md` — what it is, how to spot it, why it matters
- `payloads.md` — payloads I've actually used (in labs)
- `fixes.md` — how to remediate (the AppSec hat)

## Categories

- `a01-broken-access-control/`
- `a02-cryptographic-failures/`
- `a03-injection/` — SQLi, command injection, LDAP, etc.
- `a04-insecure-design/`
- `a05-security-misconfiguration/`
- `a06-vulnerable-components/`
- `a07-auth-failures/`
- `a08-integrity-failures/`
- `a09-logging-failures/`
- `a10-ssrf/`

Plus:

- `xss/` — cross-cutting; tracked separately because it shows up everywhere
- `idor/` — under A01 conceptually; tracked separately for searchability
