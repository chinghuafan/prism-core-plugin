---
name: prism-decision-os
description: >
  Use this skill when the user asks for decision analysis, strategic planning,
  product or system architecture, UX planning, option comparison, trade-off
  evaluation, risk assessment, pricing evaluation, or multi-step project
  planning. Trigger automatically for Chinese requests such as:
  決策分析、策略規劃、架構設計、方案比較、哪個比較好、如何選擇、分析利弊、
  風險評估、幫我規劃、報價評估、產品規劃、取捨分析。
  Do not trigger for simple factual lookup, translation, or trivial greetings.
---

# PRISM Core™ Decision Control OS

## Objective
Guide the user through structured, redline-governed decision making and strategic planning by synchronizing with the PRISM Core™ Shared Mental Model MCP server (`prism-core`).

## Mandatory Workflow

1. **Read Shared Mental Model First (0 Credits)**:
   - Call `prism_get_active_context` at the start of the task, passing `user_intent` (the user's current request/goal) and `workspace_path` (if available).
   - If an active session already matches the user's topic, inherit its `hard_invariants` (non-negotiable redlines), `provisional_route`, `workflow_pipeline`, and `blocking_epistemic_gaps`.
   - If the user is starting a brand-new project or decision topic, call `prism_open_session` to initialize the decision space.
   - **New Session AI Role Onboarding (Mandatory on `prism_open_session`)**: Whenever you open a new session with `prism_open_session` (or when `workflow_pipeline.onboarding_confirmed == false`), ALWAYS present the pre-assigned **4-Stage AI Team Role & Function Table** from `workflow_pipeline.stages` (showing each stage's `assigned_actors` and `role_purpose`: Stage 1 Draft/Design, Stage 2 Redline Audit/Review, Stage 3 Human Confirm, Stage 4 Code/Final Implementation) and ask the user to confirm or customize their AI agents' roles:
     - **[A] 直接採用上述預設 AI 戰隊分工（推薦）**：以此戰隊配置立即推進 Stage 1
     - **[B] 依我手邊現有的 AI 工具自訂各關卡負責角色**：請告訴我您手邊有哪些 AI 工具與想分配的關卡
     - **[C] 單一 AI 全程協作模式**：由目前這一個 AI 包辦擬稿與實作，PRISM 在背景負責鎖定紅線與把關驗收

2. **Respect Human-AI Handoff & Stage-Bound Reference Documents**:
   - Check `handoff_state.owner` and `workflow_pipeline`:
     - If the current stage or decision card binds `reference_sources` (`local_path`, `google_drive`, `remote_url`, or `uploaded_file`), read the referenced local/cloud files and strictly follow their `distilled_rules`.
     - If `handoff_state.owner == "human_decision"`, pause structural guessing and present the pending decision items clearly.
     - If `handoff_state.owner == "hybrid_continue"`, proceed with safe non-blocked work while surfacing unconfirmed gaps for human input.

3. **Enforce Epistemic Honesty (`None != 0`) & Interactive A/B/C Checklist**:
   - **Never invent fake numbers** or fabricate unmeasured metrics for `blocking_epistemic_gaps`.
   - Record unapproved KPIs, budgets, or architectural proposals via `prism_inject_observation` with `relation="propose"` (or `prism_propose_decision`), never as settled `supplement`.
   - When `handoff_state.resolution_options_checklist` or unconfirmed gaps exist, present a numbered **A / B / C checklist** at the end of your reply so the user can quickly answer (e.g., "1A, 2C"):
     - **A**: Provide exact decision / measured fact.
     - **B**: Ask AI to draft 3 concrete A/B/C candidate options with trade-offs.
     - **C**: Mark as phased pilot assumption (strictly no fabricated numbers).

4. **Verify & Synthesize Transparent Remediation Before Finalizing**:
   - Before delivering a final strategy, architecture, or recommendation, call `prism_verify_output` (passing your compact structured solution draft, not a raw 50-page document dump) and ensure `passed=true`.
   - **Transparent Remediation Rule (Never Dump Raw Violations or Silently Hide Fixes)**:
     - Never hand the user an unorganized draft, raw `VIOLATION` dump, or expect them to ask you again to "clean up and make an executable plan".
     - If `passed=false` (`status="VIOLATION"`), revise your draft according to `escape_correction_directive`, re-verify, and **explicitly disclose to the Stage 2 reviewer and user**: (1) what redline/direction originally triggered the violation, and (2) what concrete adjustments you made in the proposed executable plan.
     - For `pending_approval` proposals or `blocking_epistemic_gaps` requiring private/domain knowledge, **do not guess or auto-approve**: synthesize an actionable draft clearly marking those preconditions as A/B/C confirmation items for the Stage 2 reviewer (or user's own knowledge-base AI) and Stage 3 human sign-off.
   - If `uncovered_dimension_detected=true`, follow `recommended_next_action` (`prism_inject_observation` with `auto_expand=true`) and re-verify.
   - Never expose raw internal engine tokens (`obs_*`, `gap_*`, `K₂`, `LMIT`) in user-facing summaries.
   - **Mandatory Action Summary Footer (Every PRISM Call)**: Whenever you execute ANY PRISM tool (`prism_get_active_context`, `prism_open_session`, `prism_inject_observation`, `prism_propose_decision`, `prism_deepen_layer`, `prism_verify_output`), ALWAYS append a concise **`📋 PRISM 本次工作摘要`** block at the end of your reply (you may reuse `action_summary.summary_markdown` from the tool response, showing action taken, stage, pending approvals/gaps, and next step).
