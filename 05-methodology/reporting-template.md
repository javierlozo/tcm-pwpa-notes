# Finding Report — Template

> Replace this template with one finding per file. Filename pattern: `YYYY-MM-DD-short-slug.md`.

## Title

Short, descriptive. Names the vulnerability and the location.

## Severity

`Critical | High | Medium | Low | Informational` — with CVSS 3.1 vector.

## Affected

- **Asset:** `https://target.tld/path`
- **Parameter / endpoint:** `id` (GET)
- **Authentication:** `unauthenticated | user | admin`

## Summary

One paragraph. What the vulnerability is, in plain English. What an attacker can do with it. Why it matters for this business.

## Steps to Reproduce

1. Navigate to …
2. Send the following request …
3. Observe …

Include the raw request/response. Sanitize headers (cookies, tokens) before publishing.

```http
GET /api/users/1 HTTP/1.1
Host: target.tld
Cookie: session=REDACTED

```

## Proof of Concept

Screenshot, video, or curl one-liner. The reader should be able to reproduce in under a minute.

## Impact

What's the worst case? Tie it to business outcomes (data exposure, auth bypass, privilege escalation, etc.).

## Remediation

Concrete fix, not a generic "validate input." Code-level if possible.

## References

- CWE
- OWASP
- Vendor advisory if applicable
