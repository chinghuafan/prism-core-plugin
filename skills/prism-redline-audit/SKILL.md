---
name: prism-redline-audit
description: >
  Use this skill when the user asks to review, audit, verify, or validate a
  proposal, draft, architecture plan, code implementation, marketing copy, or
  customer service reply against project redlines, compliance rules, or
  reference documents. Trigger automatically for Chinese requests such as:
  幫我審核、檢查有沒有違反紅線、驗收這個方案、把關草稿、合規檢查、分析這個產出方案。
---

# PRISM Core™ Redline Audit & Output Verification

## Objective
Audit any human or AI-generated draft against the active project's non-negotiable redlines (`hard_invariants`), four-pole tension budget, epistemic gaps (`None != 0`), and stage-bound reference documents.

## Mandatory Workflow

1. **Fetch Latest Redlines & Reference Basis (0 Credits)**:
   - Call `prism_get_active_context` first to retrieve the latest `hard_invariants`, `active_observations`, `blocking_epistemic_gaps`, and any `reference_sources` (`distilled_rules`) bound to the review stage.

2. **Summarize Before Verification (Avoid Raw 50-Page Dumps)**:
   - Do **not** paste an entire 50-page raw external document directly into `prism_verify_output`.
   - If auditing a long external file, read the file first, distill its core architectural/policy decisions and numeric claims into a structured verification summary (3,000–8,000 characters), and pass that summary to `prism_verify_output`.

3. **Run `prism_verify_output` & Present Transparent Remediation**:
   - Call `prism_verify_output` with the candidate draft.
   - Check the verification report:
     - `hard_invariants_passed`: Did the draft violate any locked redline?
     - `epistemic_honesty_passed`: Did the draft fabricate precise numbers for unmeasured `None` gaps?
     - `factual_grounding_passed`: Does it match settled observations in `active_observations`?
   - If `passed=false` (`status="VIOLATION"`), do **not** merely dump the violation error back to the user or silently alter specs behind their back:
     1. Clearly disclose to the Stage 2 reviewer and human user what violated the redline or lacked grounding.
     2. Propose an actionable, corrected version aligned with `escape_correction_directive` (re-verifying redline compliance) while preserving any private-knowledge/unapproved items as explicit A/B/C decisions for the user or their domain knowledge-base AI to approve.
   - **Mandatory Action Summary Footer (Every PRISM Call)**: Always conclude your audit response with a concise **`📋 PRISM 本次工作摘要`** block (reusing `action_summary.summary_markdown`).
