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
Guide the user through structured, redline-governed decision making inside their existing AI chat by synchronizing with the PRISM Core™ Shared Mental Model MCP server (`prism-core`), delivering **3 distinct conditional strategies upfront** even on sparse input and dynamically re-evaluating them when new facts arrive.

## Mandatory Workflow

1. **Silent PRISM Sync First (0 Credits)**:
   - Call `prism_get_active_context` at the start of the task, passing `user_intent` and `workspace_path` (if available).
   - If an active session matches, inherit its `hard_invariants`, `provisional_route`, `escape_directions`, `conditional_escape_branches`, and `blocking_epistemic_gaps`.
   - If starting a brand-new decision topic, call `prism_open_session`.
   - **Fail-Closed Honesty**: If the PRISM call fails or is unreachable, explicitly disclose that PRISM status could not be verified—never pretend it passed.
   - **Result-First (No Questionnaire Blocking)**: Do **NOT** block the first screen with the 4-stage AI team role onboarding table or a multi-item gap questionnaire. Keep AI role onboarding as a brief optional footnote only when needed, and do **NOT** surface login, 60-credit, or marketing copy in the decision flow.

2. **First-Screen Output: 3 Substantially Different Conditional Strategies (Even on Sparse Input)**:
   - Whenever the user states an identifiable decision goal—even if budgets, metrics, or constraints are missing—immediately deduce up to **3 genuinely distinct conditional strategies (Strategy A / B / C)** from currently known facts, PRISM's orthogonal axes, emotional field, and explicit `if`-style assumptions.
   - **Real Differentiation**: Each strategy must differ in at least one substantive dimension (primary lever, resource commitment, premise, risk, or irreversibility)—never just "conservative / balanced / aggressive" labels on the same plan. If fewer than 3 valid strategies exist after redline filtering, state how many are valid and why—never fabricate a 3rd.
   - **Hard-Invariant Pre-Filter**: Check every candidate against `hard_invariants`. Any option that violates an immutable redline MUST be excluded from valid strategies.
   - **Strict `None != 0` & Epistemic Honesty**: Never invent unmeasured costs, conversion rates, or timelines. Clearly mark unconfirmed premises as conditional `if / 假設` rather than user facts.
   - For each strategy, present:
     - **策略方向與做法**（選什麼、得到什麼、付出什麼）
     - **策略特色與核心取捨**
     - **現況難易度與依據**（可行／有條件可行／現階段不可行／無法判定 ＋ 具體依據）
     - **關鍵未知與需補齊資料**
   - State the **目前暫定推薦（Provisional Pick）** and which known fact or assumption puts it first (or mark `條件式／待確認` if tied), followed by at most **1–2 high-value follow-up questions** and a plain-language **🎛️ 策略拉桿（Strategy Levers · 總和 240 點守恆）** block at the bottom:
     - **240 / 130 Conservation Rule**: Display the 4 endpoints in **points (0–100 each)**, strictly obeying **total ≤ 240 pts** across all 4 endpoints and **same-axis pair ≤ 130 pts** (never both ends maxed out, never all zeros):
       - **風險與動能桿**（同軸合計 ≤ 130）：`守成保本 <p1> 點 ◀──●──▶ 破局擴張 <p2> 點`
       - **價值與實證桿**（同軸合計 ≤ 130）：`理念品牌 <p3> 點 ◀──●──▶ 落地實證 <p4> 點`
       - Show total used (`<p1+p2+p3+p4> / 240 點`) and never expose raw internal tokens.
     - Invite the user to adjust either lever in plain words alongside answering questions (and include `hud_url` if returned so they know they can also inspect it visually).

3. **Next-Round Fact Injection, Lever Evolution & Deterministic Re-Selection (`Delta`)**:
   - When the user adjusts the Strategy Levers and/or provides new facts, constraints, or preferences in the next round:
     - **Enforce 240 / 130 Budget**: If the user tries to max out opposing poles (`> 130` on one axis) or exceed `240` total (or zero out all poles), explicitly explain the trade-off constraint and proportionally rebalance/compress (or keep a minimum non-zero floor) so `sum <= 240` and `axis_pair <= 130` before computing.
     - Translate the valid lever points into `emotional_override` (`pole_points / 100.0` in `0..1`) and new facts into `observation_events` (`supplement`, `supersede`, `contradict`, or `confirmed_gaps`, setting `premise_invalidated=true` if an earlier premise is overturned), then call `prism_inject_observation` and re-read the updated `state_version`.
     - **Fail-Closed Fallback**: If writing to PRISM fails, retain the previous lever weights and recommendation and explicitly state that PRISM has NOT been updated—never claim it was recalculated.
   - Re-evaluate feasible strategies and apply a fixed selection policy: (1) eliminate any hard-invariant violator, (2) rank by human-confirmed goal priority, lever orientation, and evidence support, (3) rank by confirmed resource feasibility; if tied or subjective, mark `待人類選擇` (never invent arbitrary numeric weights).
   - Lead the reply with a clear **Delta**: `拉桿位移` + `新增事實` + `被推翻的前提` ➔ `原推薦 X ➔ 現推薦 Y（或維持 X）` + `改選／維持理由` + `仍待確認事項`, and show the updated **🎛️ 策略拉桿** state.
   - Record unapproved KPIs or specs via `relation="propose"` (or `prism_propose_decision`), never as settled `supplement`.

4. **Verify Before Finalizing & Action Summary Footer**:
   - Before delivering a final strategy or recommendation, call `prism_verify_output` (passing a compact structured draft) and ensure `passed=true`.
   - If `passed=false` (`status="VIOLATION"`), revise per `escape_correction_directive`, re-verify, and transparently disclose what triggered the violation and how it was adjusted.
   - If `uncovered_dimension_detected=true`, follow `recommended_next_action` (`prism_inject_observation` with `auto_expand=true`) and re-verify.
   - Never expose raw internal engine tokens (`obs_*`, `gap_*`, `K₂`, `LMIT`) in user-facing summaries.
   - **Mandatory Action Summary Footer**: Whenever you execute ANY PRISM tool, append a concise **`📋 PRISM 本次工作摘要`** block at the end of your reply.
