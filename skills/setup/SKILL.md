---
name: prism-setup
description: >
  Onboarding and connection verification guide for the PRISM Core™ Plugin. Use
  when the user first installs the PRISM plugin, asks how to connect PRISM MCP,
  or wants a quick demonstration of automatic decision space synchronization.
---

# Welcome to PRISM Core™ Shared Mental Model OS

## Onboarding Steps

1. **Verify MCP Connection**:
   - Call `prism_get_active_context` with `user_intent="Verify PRISM MCP connection"` to confirm your account is connected.
   - If authentication is required, complete the Google OAuth flow or paste your `prism_tok_...` Bearer token from the Web HUD (`🔑 方案點數帳本 & AI 連線設定`).

2. **Open the Human Steering Web HUD**:
   - You can view and steer all active decision projects in the clean 3-card Web HUD:
     `https://prism.stratixsoft.com/decision-lab?view=product`
   - In the Web HUD, you can adjust trade-off sliders, check off A/B/C decision items, configure the 4-stage multi-AI workflow pipeline, and attach local paths or Google Drive reference documents to each stage.

3. **Natural Triggering (No `@prism` Required)**:
   - Once installed, you do not need to type `@prism`. Simply ask naturally:
     - *"幫我規劃新產品的定價與架構方案"* (Auto-triggers `prism-decision-os`)
     - *"繼續剛才在 Claude / ChatGPT 討論的專案"* (Auto-triggers `prism-handoff-sync`)
     - *"幫我審核這份方案有沒有踩到紅線"* (Auto-triggers `prism-redline-audit`)
