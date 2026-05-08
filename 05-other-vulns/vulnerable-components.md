# Vulnerable Components

> Course module: Other Common Vulnerabilities → Introduction to Vulnerable Components
> OWASP Top 10: A06 - Vulnerable and Outdated Components

## My version

Most apps don't get pwned through the code I write. They get pwned through the libraries I import that someone else wrote. If those libraries have known CVEs and the version I'm running is old enough to be vulnerable, an attacker can hit me through them.

My code doesn't have to be wrong. The bug just has to live anywhere in the dependency tree.

## A real one I just hit on my own portfolio

While I was building this very notes site, GitHub's Dependabot flagged 4 CVEs on my portfolio repo:

- `fast-uri` — path traversal via percent-encoded dot segments (high)
- `hono` — `bodyLimit()` bypass on chunked / unknown-length requests (moderate)
- `ip-address` — XSS in `Address6` HTML-emitting methods (moderate)
- `hono/jsx` — unvalidated JSX tag names allow HTML injection (moderate)

I didn't install any of these directly. They got pulled in transitively:

```
eslint
  └── @modelcontextprotocol/sdk
        ├── @hono/node-server
        │     └── hono                ← vulnerable
        ├── ajv
        │     └── fast-uri            ← vulnerable
        └── express-rate-limit
              └── ip-address          ← vulnerable
```

ESLint pulled the MCP SDK, which pulled an HTTP framework, a JSON schema parser, and a rate limiter. Each one had its own CVE.

In my case those deps are dev/lint-tree only, so they don't ship to my Vercel deploy. Runtime exposure on luislozoya.com: zero. But on a real engagement, transitive deps in the runtime bundle are a real attack surface.

## How I check for this

Built-in:

```
npm audit                  # scan reading package-lock.json
npm audit --production     # only runtime deps (skip dev tree)
npm outdated               # see what's behind, even without CVEs
```

GitHub side:

- Dependabot alerts — free on public repos, auto-scans
- Dependabot security updates — bot opens PRs with the bumped version
- GitHub Advisory Database — central place to look up a CVE

Other tools worth knowing:

- `osv-scanner` (Google) — cross-ecosystem
- `snyk test` — commercial, more thorough
- OWASP Dependency-Check — for Java / Maven heavy stacks

On a pentest, I look for lockfiles or manifests directly:

- Node: `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`
- Python: `requirements.txt`, `Pipfile.lock`, `poetry.lock`
- Ruby: `Gemfile.lock`
- PHP: `composer.lock`
- Java: `pom.xml`, `gradle.lockfile`
- Go: `go.sum`

Lockfiles pin exact versions. Match those versions against the GitHub Advisory DB or NVD and I have a list of known issues to try.

## How I fix it

Easy path:

```
npm audit fix              # patches via minor/patch bumps
npm audit fix --force      # also accepts breaking major bumps (careful)
```

When the parent hasn't released an update yet, force the safe version with `overrides` in `package.json`:

```json
"overrides": {
  "ip-address": ">=10.1.1"
}
```

If the parent package is abandoned:

- Replace it with a maintained alternative
- Fork and patch yourself
- Accept the risk and document it (then revisit weekly)

## What I look for as an attacker

On the target side:

- Old jQuery / Bootstrap / other public-CDN libs — version often visible in the page source or filename
- Lockfiles or `package.json` accidentally exposed under `/assets/`, `/.git/`, `/uploads/`, etc.
- HTTP response headers that leak versions: `X-Powered-By: Express 4.16.x`, `Server: nginx/1.14.2`
- Server banners and tech fingerprints from earlier recon (see [fingerprinting](../01-recon/fingerprinting-web-technologies.md))

Once I have a name + version, I look it up: snyk.io/vuln, github.com/advisories, nvd.nist.gov. If there's a known CVE for that exact version and a public PoC exists, that's the next thing to try.

## Lab writeups

Add links here as I complete labs touching vulnerable components.
