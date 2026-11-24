---
name: prism-handoff-sync
description: >
  Use this skill when the user wants to continue an active project, resume work
  from another AI agent (ChatGPT, Claude, Gemini, Cursor, Antigravity), check
  current project consensus, or execute an assigned stage in the multi-agent
  workflow pipeline (Stage 1 Draft, Stage 2 Review, Stage 3 Confirm, Stage 4
  Implement). Trigger automatically for Chinese requests such as:
  繼續剛才的專案、接手進度、同步目前共識、去調閱PRISM最新資料、目前決定是什麼、
  輪到你審核、輪到你實作、接力執行。
---

# PRISM Core™ Cross-AI Handoff & Workflow Pipeline Sync

## Objective
Enable zero-copy-paste handoff across heterogeneous AI agents by immediately loading the active session, stage assignment, and stage-bound reference documents from PRISM Core™.

## Mandatory Workflow

1. **Immediate Zero-Cache Context Sync & Signal Receipt (0 Credits)**:
   - Call `prism_get_active_context` immediately (pass `workspace_path` if in a local repo, `user_intent`, and your own `source_agent` identifier such as `"chatgpt"`, `"claude"`, `"antigravity"`, `"cursor"`, or `"gemini"`).
   - Inspect `unacked_signals_for_caller` at the top of the response: if any `GLOBAL_BROADCAST`, `STAGE_MULTICAST`, or `UNICAST` signal is present, honor its `machine_directives` immediately (your call automatically records your receipt in `acked_by`).
   - Inspect `upstream_stage_packets` to read the verified `deliverable_packet` (`artifact_summary`, `key_decisions_made`, `open_questions_for_next_stage`) sealed by the previous stage agent—do not ask the user to copy-paste previous AI drafts.

2. **Check Role-Aware Workflow Pipeline (`workflow_pipeline`)**:
   - Inspect `workflow_pipeline.current_stage_index` and the active stage (`stage_1_draft`, `stage_2_review`, `stage_3_confirm`, `stage_4_implement`).
   - Check `reference_sources` attached to the active stage or decision cards:
     - For `source_type: "local_path"`: read the local file directly if you have filesystem tools.
     - For `source_type: "google_drive" | "remote_url"`: read the cloud link if connected, and strictly enforce `distilled_rules`.
   - Execute the `bundled_tasks` assigned to your role within `hard_invariants`.

3. **Record New Facts & Hand Off via `PRISM-ACP v2.0` State Transitions**:
   - When the user confirms answers to the A/B/C checklist or updates a decision, call `prism_inject_observation` (`relation="supplement"` for new facts, `relation="supersede"` when overturning prior decisions, or `relation="propose"` for unapproved AI suggestions).
   - Upon completing your stage deliverable, call `prism_verify_output` with `stage_action="SUBMIT_STAGE"` (and optional `open_questions_for_next_stage`) so PRISM validates `passed=true`, seals your `deliverable_packet`, advances the pipeline to the next stage, and broadcasts `STAGE_HANDOFF` to the next AI agent.
   - If you are the Stage 2 reviewer and find a redline breach or critical flaw, call `prism_verify_output` (or `prism_inject_observation`) with `stage_action="REQUEST_CHANGES"` and `review_findings` to roll the pipeline back to Stage 1.
   - **Mandatory Action Summary Footer (Every PRISM Call)**: Whenever any PRISM tool runs during handoff or sync, ALWAYS append a concise **`📋 PRISM 本次工作摘要`** block at the end of your reply (reusing `action_summary.summary_markdown`: action taken, current stage, pending items, and next step).
