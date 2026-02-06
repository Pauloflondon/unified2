# Unified 2 – Demo Flow

This demo illustrates how Unified 2 governs an AI-assisted decision.

---

## Demo Scenario

A sensitive document is submitted for automated classification.

The document contains personally identifiable information (PII), which triggers Unified 2's governance controls.

---

## Step-by-Step Flow

### Step 1: Input Document Submitted

**Actor:** Operator (AI agent or human)

**Action:** Submit document for classification

**System Response:**
- Document is received
- Intent is captured: "Analyze document for compliance"
- Audit event: `intent_captured`

---

### Step 2: AI Generates Recommendation

**Actor:** AI Model (LLM)

**Action:** Analyze document and generate classification

**System Response:**
- AI produces recommendation: "Document is compliant"
- Recommendation is passed to Policy Engine

---

### Step 3: Policy Evaluation Triggers `needs_review`

**Actor:** Policy Engine

**Action:** Evaluate recommendation against organizational policies

**System Response:**
- Policy check: Document contains PII (email, name)
- Risk assessment: HIGH (PII detected)
- Decision state: `needs_review` (human approval required)
- Audit events: `policy_checked`, `risk_assessed`, `hitl_triggered`

---

### Step 4: Human Reviewer Approves Decision

**Actor:** Admin (Human)

**Action:** Review document and approve classification

**System Response:**
- Admin views document details in dashboard
- Admin reviews audit events (Intent → Policy → Risk → HITL)
- Admin approves with comment: "PII reviewed and acceptable for compliance check"
- Decision state: `approved`
- Audit event: `review_decision` (action: approve)

---

### Step 5: Audit Bundle Exported

**Actor:** Auditor (Human)

**Action:** Export evidence for regulatory review

**System Response:**
- Audit bundle is generated as ZIP file
- Contents:
  - `manifest.json` – Metadata + SHA256 checksums
  - `audit/audit.jsonl` – Filtered audit events
  - `run/run_record.json` – Full run record
  - `run/redacted_payload.json` – PII-redacted payload
- Auditor downloads ZIP for regulatory submission

---

## Key Question

**"Can you show this decision to your regulator?"**

**With Unified 2, the answer is: Yes.**

---

## What the Audit Bundle Proves

| Evidence | Proof |
|----------|-------|
| **Intent** | What the AI agent intended to do |
| **Policy Check** | Which policy was applied and why |
| **Risk Assessment** | Risk level and detected issues (e.g., PII) |
| **Human Decision** | Who approved/rejected and why (with comment) |
| **Timestamps** | When each step occurred |
| **Checksums** | SHA256 hashes to detect tampering |

---

## Alternative Scenarios

### Scenario A: Low-Risk Action (Auto-Approved)

If the document contains **no PII** and is classified as **low risk**:

- Policy check: `ok` (no human review required)
- Decision state: `ok` (auto-approved)
- No HITL required

### Scenario B: High-Risk Action (Rejected)

If the admin determines the document is **too risky**:

- Admin reviews document
- Admin rejects with comment: "PII risk too high for automated classification"
- Decision state: `rejected`
- Audit event: `review_decision` (action: reject)

### Scenario C: Blocked Action (Policy Violation)

If the AI agent attempts a **prohibited action** (e.g., shell execution):

- Policy check: `blocked` (action not allowed)
- Decision state: `blocked`
- No HITL required (action is denied immediately)

---

## Demo Environment

For a live demo environment, please contact the maintainers.

**Contact:** novapackt@web.de

---

## Screenshots

See [screenshots/](screenshots/) for visual examples:

- `dashboard_overview.png` – Review queue with pending items
- `audit_timeline.png` – Grouped audit events (Intent → Policy → Risk → HITL → Review)
- `decision_detail.png` – Full run record with redacted payload

---

## Next Steps

1. **Evaluation License** – 90 days, non-production use, 1,000 runs/month
2. **Technical Deep Dive** – Architecture review, security assessment, integration planning
3. **Pilot Deployment** – 6-12 months, limited production, single business unit

**Contact:** novapackt@web.de

---

© 2026 NovaPact. All rights reserved.
