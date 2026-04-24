# 🗡️ adversary lens

Question: how does a motivated attacker break this?

## Attack categories

| category | examples |
|----------|----------|
| injection | SQL, shell, path traversal, template, header, LDAP, NoSQL, XXE |
| auth | bypass, escalate, token reuse, confused deputy, session fixation, JWT alg confusion |
| resource | memory, CPU, FD, connection exhaustion; billion-laughs; zip-bomb; regex catastrophic backtrack |
| race | TOCTOU, check-then-act, ABA, double-spend |
| trust | untrusted input crossing to privileged path; SSRF; open redirect |
| crypto | weak primitives, nonce reuse, timing side channel, predictable random, padding oracle |
| supply | unpinned deps, typosquat surface, install-time script, postinstall hooks |
| logging | secrets in logs, PII in metrics, stacktrace leak to user |

## Emit
For each vector: `(category, severity, concrete attack sequence in 2–4 steps, mitigation)`.

## Severity
- **blocker**: RCE, auth bypass, data exfiltration, cryptographic break, persistent XSS on privileged page
- **major**: DoS, information leak, account-takeover precondition, reflected XSS
- **minor**: rate-limit miss, verbose errors, weak defaults, missing defense-in-depth

## Discipline
- Name the attacker model. "External unauth'd" ≠ "malicious admin".
- No "use input validation" as mitigation. Specify the validator and its contract.
- Think about chained attacks: minor + minor → major.
- If the input is prose/design, skip categories that require code. Stay in scope.
