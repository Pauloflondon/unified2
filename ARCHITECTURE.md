# Architecture Overview

Unified 2 follows a **governance-first architecture**.

---

## Core Principles

### 1. Separation of Concerns

Unified 2 separates four distinct layers:

| Layer | Responsibility |
|-------|----------------|
| **Inference** | AI model generates recommendations |
| **Decision** | Policy evaluation determines next steps |
| **Governance** | Human-in-the-loop (HITL) for high-risk actions |
| **Audit** | Immutable event log for regulatory review |

### 2. Deterministic State Transitions

Every decision has a defined lifecycle with explicit state transitions:

- No implicit or silent state changes
- All transitions are validated and recorded
- State machine is deterministic and reproducible

### 3. Human-in-the-Loop by Design

Policies define when human escalation is required:

- Overrides are explicit and logged
- Admins must provide justification for decisions
- Audit trail includes human decision rationale

---

## High-Level Flow

```
Input
  ↓
AI Recommendation
  ↓
Policy Evaluation
  ↓
Decision State
  ↓
(Optional) Human Review
  ↓
Final Status
  ↓
Audit Export
```

---

## Decision States

Unified 2 uses a deterministic state machine:

| State | Description |
|-------|-------------|
| **`ok`** | Action approved automatically (low risk) |
| **`needs_review`** | Action requires human approval (high risk) |
| **`approved`** | Human approved the action |
| **`rejected`** | Human rejected the action |
| **`blocked`** | Action blocked by policy (not allowed) |
| **`error`** | System error during processing |

**Each transition is explicitly validated and recorded.**

---

## Policy Engine

Policies are defined as **code, not documentation**:

- YAML-based policy files
- Allow/deny rules with constraints
- Risk thresholds for HITL triggering
- Versioned and auditable (Git history)

**Example Policy Structure:**

```yaml
policy_name: compliance_screener_balanced
version: 1.0
rules:
  allow:
    - tool: "llm_analyze"
      constraints:
        - no_pii: true
  deny:
    - tool: "shell_exec"
  hitl_triggers:
    - risk_level: "HIGH"
    - pii_detected: true
```

---

## Risk Engine

The risk engine evaluates every AI action:

- **PII Detection** – Identifies personally identifiable information
- **Risk Classification** – Assigns risk levels (LOW, MEDIUM, HIGH, CRITICAL)
- **HITL Triggering** – Escalates high-risk actions to human review

**Risk assessment is rule-based, not ML-based**, ensuring transparency and reproducibility.

---

## HITL Queue

When policies or risk assessments require human oversight:

- Actions are placed in a review queue
- Admins approve or reject with mandatory comments
- Audit events record every decision with timestamps and justifications

**HITL ensures that critical decisions remain under human control.**

---

## Audit Trail

Every action generates an append-only audit log:

| Event Type | Description |
|------------|-------------|
| **`intent_captured`** | AI action submitted |
| **`policy_checked`** | Policy evaluation result |
| **`risk_assessed`** | Risk classification result |
| **`hitl_triggered`** | Human review required |
| **`review_decision`** | Human approval/rejection |

**Audit Guarantees:**

- Timestamped events
- Actor attribution (who made the decision)
- Policy reference (which policy was applied)
- Decision hash chain (tamper detection)

---

## Evidence Export

Audit logs can be exported as ZIP files for regulatory review:

**Contents:**
- `manifest.json` – Metadata + SHA256 checksums
- `audit/audit.jsonl` – Filtered audit events
- `run/run_record.json` – Full run record
- `run/redacted_payload.json` – PII-redacted payload

**Unified 2 is designed to be explainable under audit.**

---

## API Contracts

Unified 2 provides well-defined API contracts:

| Contract | Purpose |
|----------|---------|
| **Governance Events** | Audit event schema |
| **Decision Status** | Run status schema |
| **Audit Bundle** | Evidence export schema |

See [api_contracts/](api_contracts/) for JSON Schema definitions.

---

## Deployment Models

### Self-Hosted (Primary)

**Target:** Enterprises with data residency requirements

**Components:**
- Unified2 Core (Docker / Kubernetes)
- Unified2 Dashboard (Docker / Kubernetes)
- PostgreSQL (customer-managed)

### Managed Service (Future)

**Target:** SMBs without DevOps capacity

**Components:**
- Unified2 Core (NovaPact-hosted)
- Unified2 Dashboard (NovaPact-hosted)
- PostgreSQL (NovaPact-managed)

---

## Technology Stack

| Component | Technology |
|-----------|------------|
| **Backend** | Python 3.11 + FastAPI |
| **Database** | SQLite (MVP) / PostgreSQL (Production) |
| **Frontend** | React 19 + TypeScript + Vite |
| **Audit Log** | Append-only JSONL with SHA256 checksums |
| **Policy Engine** | YAML-based rules (no ML) |
| **Risk Engine** | Rule-based PII detection (regex) |

---

## Scalability

Unified 2 is designed for production-scale deployments:

- **Stateless API** – Horizontal scaling via load balancer
- **Database-backed storage** – PostgreSQL for production
- **Async processing** – Background jobs for evidence export (planned)
- **WebSocket updates** – Real-time queue updates (planned)

---

## Extensibility

Unified 2 provides extension points for custom integrations:

- **Custom policies** – Define organization-specific rules
- **Custom risk models** – Integrate ML-based risk assessment (optional)
- **Custom audit exporters** – Generate PDF summaries, send to SIEM, etc.
- **Integration APIs** – Slack, Jira, ServiceNow (planned)

---

## References

- **GDPR:** https://gdpr.eu/
- **AI Act (EU):** https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai
- **SOX:** https://www.sec.gov/spotlight/sarbanes-oxley.htm
- **ISO 27001:** https://www.iso.org/isoiec-27001-information-security.html

---

© 2026 NovaPact. All rights reserved.
