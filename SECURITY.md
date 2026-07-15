# Security Policy

## Supported Versions

Security updates are provided for the latest stable release of `aiosonic`.

| Version               | Supported |
| --------------------- | --------- |
| Latest stable release | ✅        |
| Older releases        | ❌        |

Users should reproduce the issue with the latest available release before
submitting a security report.

Support for older versions may be provided at the maintainers' discretion.

## Reporting a Vulnerability

Please do not report security vulnerabilities through public GitHub issues,
pull requests, discussions, or other public channels.

Instead, use GitHub's private vulnerability reporting feature:

1. Open the repository's **Security** tab.
2. Select **Advisories**.
3. Select **Report a vulnerability**.

Please include as much of the following information as possible:

- The affected `aiosonic` version
- The Python version and operating system
- A clear description of the vulnerability
- The expected security impact
- Minimal reproduction steps or proof-of-concept code
- Relevant configuration, request, or response data
- Any known mitigations or suggested fixes
- Whether the vulnerability has already been publicly disclosed
- Your preferred name or attribution for a future advisory

Please remove credentials, tokens, cookies, private keys, and other sensitive
information from reproduction material unless they are specifically required
to demonstrate the issue.

## Security Issue Scope

Examples of issues that may qualify as security vulnerabilities include:

- TLS certificate or hostname validation bypasses
- HTTP request smuggling or response-splitting vulnerabilities
- Header, cookie, or CRLF injection
- Authorization header, cookie, or credential leakage
- Sensitive data being forwarded across redirects
- Proxy authentication or proxy-routing bypasses
- Unsafe redirect handling
- Incorrect URL, hostname, or port validation
- WebSocket origin, TLS, or protocol validation vulnerabilities
- Denial-of-service conditions caused by malformed or untrusted input
- Resource exhaustion caused by unbounded parsing, buffering, redirects, or
  connection handling
- Security-relevant inconsistencies between HTTP/1.1, HTTP/2, and WebSocket
  behavior

The following are generally not considered security vulnerabilities unless
they produce a concrete security impact:

- Ordinary connection failures or timeouts
- Performance issues without a denial-of-service scenario
- Bugs that require already-compromised local code or configuration
- Vulnerabilities in unsupported versions that do not affect the latest
  stable release
- Issues that only affect applications which explicitly disable TLS
  verification or other security controls
- General hardening suggestions without a reproducible vulnerability

Ordinary bugs should be reported through the repository's public issue
tracker.

## Response Process

The maintainers will make a reasonable effort to:

1. Acknowledge the report within 7 days.
2. Review the report and attempt to reproduce the issue.
3. Determine its severity, scope, and affected versions.
4. Keep the reporter informed about significant progress.
5. Develop and test a fix when the issue is confirmed.
6. Prepare a security advisory and patched release when appropriate.
7. Coordinate disclosure with the reporter.

Resolution time depends on the complexity, severity, and impact of the issue.

A report may be closed if it cannot be reproduced, does not affect a
supported version, or does not present a meaningful security impact. When
possible, the maintainers will explain the reason for that decision.

## Coordinated Disclosure

Please allow the maintainers reasonable time to investigate and release a fix
before publicly disclosing the vulnerability.

Do not publish proof-of-concept code, exploitation details, or information
that would make the vulnerability easier to exploit before a fix is
available.

Confirmed vulnerabilities may be published as GitHub Security Advisories
after a patched version has been released.

Reporters will be credited in the advisory unless they prefer to remain
anonymous.

## Safe Harbor

Security research performed in good faith and in accordance with this policy
will not be considered malicious activity by the project maintainers.

Researchers are expected to:

- Avoid accessing, modifying, or deleting data that does not belong to them
- Avoid disrupting services or systems
- Avoid privacy violations
- Use only the minimum testing necessary to demonstrate the issue
- Report findings privately and allow reasonable time for remediation
