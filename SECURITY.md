# Security & Governance Model

Unified 2 is designed for use in security- and compliance-sensitive environments.

---

## Threat Model (High Level)

Unified 2 addresses the following threats:

| Threat | Description | Mitigation |
|--------|-------------|------------|
| **Unauthorized decision changes** | Attacker modifies decision status | Deterministic state machine + audit log |
| **Undocumented overrides** | Admin bypasses policy without justification | Mandatory comments + audit events |
| **Non-deterministic AI behavior** | AI produces unpredictable results | Policy enforcement + risk classification |
| **Missing audit evidence** | No proof of decision-making process | Append-only audit log + evidence export |

---

## Security Principles

### 1. Explicit Policy Enforcement

Policies are **code, not documentation**:

- YAML-based policy files (versioned in Git)
- Allow/deny rules with constraints
- No implicit policy decisions

### 2. Deterministic State Machine

Every decision follows a defined lifecycle:

- No silent state changes
- All transitions are validated and recorded
- State machine is reproducible

### 3. Mandatory Audit Logging

Every action generates audit events:

- Append-only JSONL log
- SHA256 checksums for tamper detection
- Timestamped events with actor attribution

### 4. Separation of Inference and Decision Layers

AI inference is separated from decision-making:

- AI generates recommendations (inference layer)
- Policies evaluate recommendations (decision layer)
- Humans approve/reject (governance layer)

---

## Secret Handling

**Critical:** No secrets are stored in this repository.

### Runtime Secrets

- API keys (OpenAI, LLM providers) are provided via environment variables
- Database credentials are provided via environment variables
- `.env` files are never shipped or committed

### API Key Management

- Keys are rotated regularly (every 30-90 days)
- Keys are stored in secrets managers (AWS Secrets Manager, HashiCorp Vault)
- Keys are hashed (SHA256) before storage (planned for production)

### CI/CD Security

- Secret scanning (Gitleaks, TruffleHog) in CI pipeline
- No secrets in Docker images or deployment artifacts
- Environment-specific secrets (dev, pilot, production)

---

## Audit Guarantees

### What Unified 2 Guarantees

| Guarantee | Mechanism |
|-----------|-----------|
| **Every action is logged** | Append-only JSONL |
| **Logs are tamper-evident** | SHA256 checksums |
| **PII is redacted in exports** | Key-based redaction |
| **HITL decisions are recorded** | Audit events |
| **Policies are versioned** | Git history |

### What Unified 2 Does NOT Guarantee

| Non-Guarantee | Reason |
|---------------|--------|
| **Logs cannot be deleted** | Admins with file system access can delete logs (mitigated by backups) |
| **Logs are cryptographically signed** | Not implemented in MVP (planned for production) |
| **PII detection is 100% accurate** | Rule-based detection has false negatives (ML-based detection planned) |
| **Evidence exports are encrypted** | Not implemented in MVP (planned for production) |

---

## Compliance Support

Unified 2 supports (but does not guarantee) compliance with:

| Regulation | Support |
|------------|---------|
| **GDPR** | PII detection + redaction + audit trails |
| **AI Act (EU)** | Explainability + human oversight + audit logs |
| **SOX** | Audit trails for financial decisions |
| **ISO 27001** | Information security controls |

**Note:** Customers remain responsible for their own compliance. Unified 2 provides tools, not legal guarantees.

---

## What Unified 2 Does NOT Claim

- ❌ No guarantee of legal compliance
- ❌ No automatic regulatory approval
- ❌ No replacement of internal controls

**Unified 2 provides technical evidence, not legal judgment.**

---

## Incident Response

### Security Incident Types

| Incident | Response |
|----------|----------|
| **API key compromise** | Rotate all keys immediately, review audit logs |
| **Audit log tampering** | Verify checksums, restore from backup |
| **PII exposure** | Notify data protection officer, comply with GDPR breach notification (72 hours) |
| **Unauthorized access** | Revoke access, review RBAC configuration |

### Contact

**Security Issues:** security@nova-pact.com

**Response Time:** 24 hours (best-effort for MVP, SLA-backed for Enterprise)

---

## Vulnerability Disclosure

**Report security issues to:** security@nova-pact.com

**Do not disclose vulnerabilities publicly before coordinated remediation.**

**Response Time:** 24 hours (acknowledgment), 7 days (initial assessment)

---

## Known Limitations (MVP)

| Limitation | Impact | Mitigation (Production) |
|------------|--------|-------------------------|
| **No rate limiting** | API abuse possible | Implement rate limiting (per-key, per-IP) |
| **No key expiration** | Stolen keys remain valid | Implement automatic key rotation |
| **No cryptographic signing** | Audit logs can be forged | Implement digital signatures (RSA, ECDSA) |
| **No encryption of exports** | Evidence exports can be intercepted | Implement GPG encryption |

---

## Security Roadmap

### Short-Term (Q1-Q2 2026)

- 🔄 Rate limiting (per-key, per-IP)
- 🔄 API key expiration + automatic rotation
- 🔄 Secrets manager integration (AWS Secrets Manager, HashiCorp Vault)

### Long-Term (H2 2026)

- 🔄 Cryptographic signing of audit logs (RSA, ECDSA)
- 🔄 Encryption of evidence exports (GPG, S3 encryption)
- 🔄 Distributed ledger for audit logs (blockchain, planned for enterprise)
- 🔄 ML-based PII detection (context-aware, multi-language)

---

## References

- **OWASP Top 10:** https://owasp.org/www-project-top-ten/
- **NIST Cybersecurity Framework:** https://www.nist.gov/cyberframework
- **GDPR:** https://gdpr.eu/
- **AI Act (EU):** https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai
- **SOX:** https://www.sec.gov/spotlight/sarbanes-oxley.htm

---

© 2026 NovaPact. All rights reserved.
