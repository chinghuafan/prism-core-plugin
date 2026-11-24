# PRISM Core™ Plugin & Skills (`prism-core-plugin`)

> **PRISM Core™** 是為多個 AI 代理（Antigravity、Gemini CLI、Claude Code、OpenAI Codex、ChatGPT、Cursor）而建的**可解釋分層幾何決策控制內核**——在 AI 動手給答案前，先展開競爭目標、風險紅線與未量測缺口（`None ≠ 0`），並讓人類透過 Web HUD 掌握最終拍板與驗收主權。

本倉庫為 **純設定檔與自動觸發 Skill 外掛**（含 Remote MCP 路由 `https://prism.stratixsoft.com/mcp/rpc`），不包含後端核心引擎原始碼。

> **誠實狀態說明**：公開本 GitHub 倉庫代表開發者可自行下載或透過腳本掛載設定，**不等於**所有第三方官方 AI 市集已完成上架審查。

---

## 一、3 分鐘首次體驗（Quick Start）

1. **取得免費試用額度（免安裝 Web HUD）**：
   前往 [PRISM Core™ Decision Lab](https://prism.stratixsoft.com/decision-lab?utm_source=github&utm_medium=readme&utm_campaign=sprint_7d)，使用 Google 帳號登入即可獲得 **60 點免費試用額度**（讀取共識與補充事實為 0 點）。
2. **一鍵掛載本機 AI 代理與 Skills**：
   ```bash
   curl -fsSL "https://prism.stratixsoft.com/api/decision-lab/install-plugin.sh" | bash
   ```
   > 經隔離 `$HOME` 環境實測，此腳本可成功建立符號連結至：
   > - Antigravity / Gemini CLI：`~/.gemini/config/plugins/prism-core-plugin`
   > - Claude Code：`~/.claude/skills/`（`prism-decision-os`、`prism-handoff-sync`、`prism-redline-audit`）
   > - OpenAI Codex：`~/.codex/skills/`
3. **重開 AI 對話視窗並實測一個含紅線的真實決策**：
   直接以自然語言輸入（無須背誦 `@prism` 指令）：
   > 「幫我規劃一個新產品上線方案，硬性紅線是不准捏造未實測的預算與轉換率。」
   觀察 AI 自動呼叫 PRISM Core™ 標出待確認缺口並生成幾何圍欄，最後至 Web HUD 完成審視與拍板。

---

## 二、包含的自動化技能（Skills）
- `prism-decision-os`：問題拆解、LMIT 幾何張力場展開、風險評估與方案取捨
- `prism-handoff-sync`：跨 AI 決策空間同步與階段接力交棒（`STAGE_HANDOFF`）
- `prism-redline-audit`：紅線合規審查與認識論誠實把關（`prism_verify_output`）

---

## 三、進階桌面輔助工具（選配）
- 若您使用 macOS 且需要原生的狀態列通知與剪貼簿熱更新，可於完成首次 Web HUD 體驗後前往 [macOS 下載與連線指南](https://stratixsoft.com/prism-app.html)（目前 v0.2.0 為未簽署測試版，選配使用）。
