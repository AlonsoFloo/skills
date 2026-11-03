# Security Checklist

## Input and Untrusted Data

- User input is validated and sanitized at the boundary, then parsed into a
  refined type (Parse, Don't Validate) so the rest of the code never sees raw
  input.
- Data from external sources (APIs, deep links, intents, push payloads, files,
  logs, user content, config files, LLM output) is treated as untrusted.
- External data flows are validated at system boundaries before being used in
  logic, rendering, file paths or commands.
- Sizes and counts are bounded: no unbounded allocation, recursion, regex
  backtracking or loop driven by input.

## Injection

- SQL queries are parameterized, never built by string concatenation or
  interpolation.
- Shell commands do not interpolate untrusted values; arguments are passed as a
  list and variables are quoted.
- File paths built from input are normalized and confined to an allowed
  directory (no `../` traversal, no symlink escape).
- Outputs are encoded for their context (HTML, URL, JavaScript, SQL) to prevent
  XSS and similar injections; WebViews do not load untrusted content with
  JavaScript bridges enabled.
- Deserialization of untrusted data uses a safe format and a fixed schema, never
  arbitrary type resolution.

## Secrets

- Secrets are kept out of code, logs, crash reports, analytics, URLs and version
  control. Gitleaks or an equivalent hook is in place.
- No secret is printed by CI (`set -x`, echoing env, verbose curl).
- Mobile apps do not embed private keys or privileged API tokens: anything in
  the binary is public.
- Tokens are stored in the platform secure store (Keychain, Android Keystore /
  EncryptedSharedPreferences), not in plain preferences or files.

## Authentication and Authorization

- Authentication and authorization are checked where needed, on the server side,
  for every endpoint or action the change adds.
- Access to a resource checks ownership, not only that the user is logged in
  (no IDOR by changing an id).
- Session and token lifetimes, refresh and revocation behave as intended;
  logout clears local credentials.
- Exported Android components, URL schemes and universal links verify the
  caller or the payload before acting.

## Cryptography and Transport

- Network traffic uses TLS; no cleartext exceptions, no disabled certificate
  validation, no trust-all managers.
- Standard, maintained crypto primitives are used with safe parameters; no
  homemade crypto, no MD5/SHA-1 for security, no hardcoded IV or salt.
- Random values for security use a cryptographically secure generator.

## Personal Data and Privacy

- Personal data is collected only when needed, and not logged.
- Sensitive screens and data are excluded from backups, screenshots and
  clipboard where the platform allows.
- Privacy declarations (iOS privacy manifest, Play data safety) still match what
  the code collects.

## Dependencies and Supply Chain

- Dependencies come from trusted sources and have no known vulnerabilities.
- New dependencies are justified, maintained, and pinned to an exact version or
  digest; lockfiles are updated.
- CI actions and container images are pinned by SHA digest, workflows use
  least-privilege `permissions`, and `pull_request_target` never checks out
  untrusted code with secrets.

## Error Handling

- Errors fail closed: an exception in an auth or permission check denies access.
- Error messages and responses do not leak stack traces, internal paths, queries
  or secrets to the caller.
