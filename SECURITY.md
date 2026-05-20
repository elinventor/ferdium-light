# Security Policy

## Supported Versions

| Version | Supported |
| ------- | --------- |
| 1.x     | Yes       |

## Reporting a Vulnerability

For **non-critical** issues (UI bugs, minor information leaks): open a GitHub issue.

For **critical** vulnerabilities (remote code execution, sandbox escape, credential
exposure): open a [GitHub Security Advisory](https://docs.github.com/en/code-security/security-advisories)
on this repository so the report is kept private until a fix is released.

Vulnerabilities that originate in Electron, Chromium, or a third-party recipe should
be reported upstream to those projects.

---

## Supply Chain Security

This project takes the following measures to reduce the risk of dependency-based
supply chain attacks (similar to the 2024 XZ/liblzma incident and recurring npm
package hijacks):

### Lockfile enforcement

`frozen-lockfile = true` is set in `.npmrc`. Running `pnpm install` will **fail**
if `pnpm-lock.yaml` is out of sync with `package.json`. This prevents silent
substitution of dependency versions during developer installs or CI runs.

### Exact version pinning

`save-exact = true` is set in `.npmrc`. All direct dependencies use pinned versions
(no `^` or `~` ranges). Version upgrades require an explicit change to `package.json`
and a reviewed lockfile update.

### Automated vulnerability scanning

`pnpm audit --audit-level=high` runs as a mandatory step in every CI build before
any compilation or publishing. A build with high-severity advisories in the dependency
tree will fail the workflow.

`audit-level = high` in `.npmrc` also surfaces advisories during local installs.

### No postinstall privilege escalation

`unsafe-perm` has been removed from `.npmrc`. Postinstall scripts run without
elevated privileges, limiting the blast radius of a compromised package that attempts
to exfiltrate data or install malware via lifecycle hooks.

### No telemetry

This fork contains no error-reporting or telemetry integrations. No data from the
application is transmitted to third-party servers at runtime.

### Reviewing dependency changes

When updating dependencies:
1. Run `pnpm audit` after updating and resolve any findings.
2. Review the diff of `pnpm-lock.yaml` — unexpected new packages or changed hashes
   should be investigated before merging.
3. Prefer packages with published provenance attestations.
