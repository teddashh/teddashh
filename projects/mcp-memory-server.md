<a id="english"></a>

[← Public GitHub portfolio](./README.md) · [Ted's profile](../README.md) · [GitHub repository](https://github.com/teddashh/mcp-memory-server) · [Repository README](https://github.com/teddashh/mcp-memory-server#readme) · **English** · [繁體中文](#traditional-chinese)

# MCP Memory Server

> A structured memory interface for AI agents, combining workspace-local recall with optional cross-workspace search.

## Positioning and snapshot

MCP Memory Server is a Python developer-infrastructure project for AI clients that speak the Model Context Protocol. It models memory as decisions, resolved issues, open questions, and knowledge—not as one undifferentiated transcript—and exposes operations for capture, recall, search, salience, audit reporting, and system status.

The strongest way to position the current repository is as an **early, inspectable MCP memory server and architecture prototype**. Its local and Oracle adapters, tool contracts, CJK-aware estimator, and two transports are implemented. Some of the broader four-layer system described in the README depends on database files, schemas, and synchronization jobs that are not included in this public repository.

| Snapshot | Audited repository reality |
|---|---|
| Audience | Developers and operators using Claude Code, Codex, Gemini CLI, or another MCP client |
| Package | <code>mcp-memory-server</code> version <code>0.1.0</code>; Python 3.11+ |
| Runtime | FastMCP over stdio or streamable HTTP on <code>127.0.0.1:8787/mcp</code> |
| Registered surface | **18 MCP tools** in the current server code |
| Storage reached by code | Per-workspace SQLite; optional Oracle AI Database; optional OpenAI embeddings for Oracle vector queries |
| Repository size | 9 tracked files; 841-line main server module; no tests, migrations, or schema DDL |
| Audit basis | Default branch <code>master</code>, commit <code>780e3b11d24eb44c5a04cbc65bee68119f912f24</code>, reviewed 2026-07-11 |

## Problem

AI coding sessions often restart without durable context. A later agent may know neither why an architectural choice was made nor whether an incident was already solved. Saving the whole transcript is not enough: decisions, unresolved questions, and reusable knowledge have different retrieval and lifecycle needs.

This project addresses three related problems:

1. **Structured continuity.** Important work should be stored under an explicit semantic type, with workspace and tags, instead of disappearing into chat history.
2. **Useful retrieval.** Frequently accessed memories should surface ahead of stale noise, while cross-workspace search should remain possible when a cloud index is configured.
3. **Multilingual context control.** A character-count heuristic tuned for ASCII substantially underestimates Chinese, Japanese, and Korean text, so compaction decisions need a CJK-aware estimate.

The design also aims to keep the memory interface client-neutral. MCP clients call the same tools while storage remains under the operator's control.

## Experience and capabilities

### Capture and organize

Five write-oriented tools cover fast capture and explicit record types:

- <code>memory_store</code> routes text to a decision, resolved issue, question, or knowledge item.
- <code>memory_record_decision</code> preserves what was chosen, why, how, and tags.
- <code>memory_record_resolved</code> records a problem and its resolution.
- <code>memory_record_question</code> stores context and priority from 1 to 3.
- <code>memory_record_knowledge</code> creates a knowledge item that an external workflow can later sync.

The automatic classifier is intentionally small: it checks English phrases such as “decided,” “fixed,” and “should we,” plus a question mark. Explicit record tools are the reliable path for Chinese text or nuanced classification.

### Recall, deletion, and salience

<code>memory_list</code>, <code>memory_get</code>, and <code>memory_get_summary</code> provide workspace-local recall. <code>memory_delete</code> performs a hard delete by ID. A successful <code>memory_get</code> attempts to increment access count, update the last-access timestamp, and multiply salience by 1.05, capped at 2.0.

<code>memory_reinforce</code> provides a deliberate 1.20 multiplier, also capped at 2.0. The <code>--decay</code> command multiplies records that have been stale for at least 30 days by 0.95. Scheduling that command is left to the operator.

### Search and reporting

Search behavior changes with the available infrastructure:

- Without Oracle, the server walks configured workspace databases and performs text search over local <code>knowledge_items</code>, then sorts the collected hits by salience.
- With Oracle but no embedding, it performs parameterized text search across cloud knowledge and orders by salience and recency.
- With Oracle and an OpenAI embedding provider, it uses Oracle <code>VECTOR_DISTANCE(..., COSINE)</code> and orders by vector distance.

Four audit/report tools query Oracle tables for audit entries, aggregate statistics, daily reports, and agent activity. Three administration tools report status, inspect session-file size, and summarize cloud workspaces. The “compact” tool currently measures and recommends action; it does not rewrite the session file itself.

### The 18 code-registered tools

| Group | Tools |
|---|---|
| Write (5) | <code>memory_store</code>, <code>memory_record_decision</code>, <code>memory_record_resolved</code>, <code>memory_record_question</code>, <code>memory_record_knowledge</code> |
| Read (3) | <code>memory_list</code>, <code>memory_get</code>, <code>memory_get_summary</code> |
| Delete (1) | <code>memory_delete</code> |
| Search (1) | <code>memory_search</code> |
| Salience (1) | <code>memory_reinforce</code> |
| Audit trail (4) | <code>memory_audit_search</code>, <code>memory_audit_stats</code>, <code>memory_daily_report</code>, <code>memory_activity_log</code> |
| Administration (3) | <code>memory_status</code>, <code>memory_compact</code>, <code>memory_oracle_summary</code> |

## Architecture and data flow

~~~mermaid
flowchart LR
    C["MCP clients<br/>Claude Code · Codex · Gemini"] --> T["FastMCP tool layer<br/>stdio or localhost HTTP"]
    T --> W["Workspace detection<br/>env · cwd · workspace map"]
    W --> S["Existing per-workspace SQLite<br/>four structured memory tables"]
    T --> M["session.md inspection<br/>CJK-aware size estimate"]
    T --> O["Optional Oracle adapter<br/>cross-workspace knowledge + audit"]
    T -. "optional query embedding" .-> E["OpenAI embeddings"]
    E --> O
    O --> V["Oracle vector or text search"]
~~~

### Local write flow

1. The MCP client calls a typed tool.
2. The server resolves the workspace from <code>MEMORY_WORKSPACE</code>, <code>INIT_CWD</code> / current working directory, or a JSON workspace map.
3. It opens an existing <code>.claude-memory/memory.db</code> for that workspace.
4. It writes to <code>decisions</code>, <code>resolved</code>, <code>open_questions</code>, or <code>knowledge_items</code>, using an eight-character UUID prefix as the record ID.

### Search flow

1. <code>memory_search</code> first asks for an Oracle connection.
2. If Oracle is disabled, it searches mapped local knowledge databases.
3. If Oracle is enabled, the server tries to create an embedding when the OpenAI extra and API key are available.
4. An embedding selects cosine vector search; otherwise the server falls back to Oracle text search.

### Documented layers versus this repository

The README presents a useful four-layer model: session Markdown, local SQLite, Git-readable Markdown, and a cloud database. In the audited public code:

- The session layer is read for a health check and token estimate.
- SQLite reads and writes are implemented, but database creation and schema migration are not.
- The Git/Markdown synchronization layer is architectural documentation; no exporter or synchronization job is present here.
- Oracle queries are implemented, but the documented 18-table schema, provisioning scripts, and sync pipeline are not included.

That distinction matters for both installation expectations and an accurate portfolio description.

## Key design and security choices

### Structured memory rather than transcript storage

Separate tables preserve the meaning of a record and allow purpose-specific fields: rationale for decisions, priority for questions, and sync state for knowledge. This is a stronger domain model than a flat key/value cache, at the cost of requiring a compatible schema.

### CJK-aware estimation

The estimator assigns approximately 0.25 token per ordinary ASCII character, 1.5 per CJK, kana, Hangul, or full-width character, and 2.0 per high-range emoji. <code>memory_compact</code> recommends compaction above 100 lines or 3,000 estimated tokens. This is a deterministic heuristic, not a tokenizer, but it directly addresses multilingual undercounting.

### Local-first intent, optional cloud reach

The HTTP mode binds to loopback rather than all interfaces, reducing accidental network exposure. Oracle is optional at runtime, and environment variables can override the Oracle password and OpenAI API key. However, the package still installs <code>oracledb</code> as a required dependency.

### Secrets and access control

The interactive setup writes Oracle credentials, wallet password, and embedding API key to <code>~/.mcp-memory/config.json</code>. It neither uses hidden password input nor explicitly tightens file permissions. The server constructor does not configure MCP authentication. Loopback binding is a useful boundary, but operators should still protect the host account, restrict the config file, prefer environment or secret-manager injection, and add an authenticated reverse proxy before any remote exposure.

### Failure behavior

Embedding failures silently fall back to text search, and several salience updates tolerate older schemas by suppressing column errors. This keeps some reads available during partial configuration, but can hide drift. Operational deployments would benefit from explicit health checks, migrations, and observable error reporting.

## Quick start and use

### Install the package

~~~bash
git clone https://github.com/teddashh/mcp-memory-server.git
cd mcp-memory-server

python3 -m venv .venv
source .venv/bin/activate
pip install -e .

# Optional: installs the OpenAI SDK used for query embeddings
pip install -e '.[embeddings]'
~~~

The editable install and HTTP startup were smoke-tested successfully during this review. The server can be launched as:

~~~bash
python -m mcp_memory            # MCP over stdio
python -m mcp_memory --http     # streamable HTTP at http://127.0.0.1:8787/mcp
python -m mcp_memory --decay    # one salience-decay pass

# Equivalent installed console entry point
mcp-memory
~~~

### Configure a client

For a stdio client, point the MCP entry at the repository:

~~~json
{
  "mcpServers": {
    "memory": {
      "command": "python",
      "args": ["-X", "utf8", "-m", "mcp_memory"],
      "cwd": "/absolute/path/to/mcp-memory-server"
    }
  }
}
~~~

### Provision the missing local prerequisites

Running <code>python setup_config.py</code> collects workspace paths, optional Oracle credentials, and an embedding provider. It does **not** create <code>workspace-map.json</code>, initialize <code>memory.db</code>, or create the expected tables. A fresh installation therefore needs compatible workspace metadata and SQLite schemas supplied from outside this repository before write tools work.

The Oracle path likewise assumes an existing wallet, user, and compatible tables. Treat the README's schema table as a contract/design reference, not an included migration.

## Current scope, risks, and license

| Area | Current status and consequence |
|---|---|
| Tool count | The README badge and prose say 19 tools. The audited FastMCP registry contains 18; the category counts also add up to 18. This page reports the code-registered number. |
| Turnkey local use | The README describes a SQLite default, but a fresh home directory has no workspace map, database, or schema. <code>memory_store</code> raises <code>FileNotFoundError</code> until those assets exist. |
| Four-layer completeness | Session inspection, SQLite access, and Oracle queries exist. Markdown export, scheduled sync, cloud DDL, and provisioning are outside this repo. |
| Configuration contract | The README's sample uses a generic <code>cloud_db</code> object, while the implementation reads an <code>oracle</code> object. PostgreSQL, Supabase, MySQL, and local embeddings are described as planned, not implemented backends here. |
| Classification | Automatic classification recognizes English cue phrases, not the Chinese cue words implied by the translated explanation. |
| Salience details | Code caps reinforcement at 2.0, but decay does not enforce the README's stated 0.01 floor. Oracle vector results are ranked by cosine distance, not salience. |
| Compaction | The tool reports size and tells the agent what to extract; it does not summarize, persist records, or trim the file automatically. |
| Quality controls | No automated test suite, CI workflow, dependency lock, release, or migration framework is committed. |
| Production security | Plaintext local secrets and unauthenticated MCP construction require additional hardening before shared or remote deployment. |
| License | The README states “MIT” and links its badge to <code>LICENSE</code>, but **the repository contains no LICENSE file and GitHub detects no license**. It is publicly readable, but formal reuse terms are currently incomplete. Adding the intended MIT license file should precede labeling it unambiguously open source. |

## Source and documentation links

- [GitHub repository](https://github.com/teddashh/mcp-memory-server)
- [Repository README and architecture notes](https://github.com/teddashh/mcp-memory-server#readme)
- [Registered MCP tools and transports](https://github.com/teddashh/mcp-memory-server/blob/master/mcp_memory/server.py)
- [SQLite and Oracle adapters](https://github.com/teddashh/mcp-memory-server/blob/master/mcp_memory/db.py)
- [Configuration loader](https://github.com/teddashh/mcp-memory-server/blob/master/mcp_memory/config.py)
- [Package metadata and dependencies](https://github.com/teddashh/mcp-memory-server/blob/master/pyproject.toml)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

[← Previous: Claude Idea Review Skill](./claude-idea-review-skill.md) · [Public GitHub portfolio](./README.md) · [Next: Homograph Attack Awareness Demo →](./idn-homograph-example.md)

---

<a id="traditional-chinese"></a>

[← GitHub 公開作品集](./README.md#traditional-chinese) · [Ted 個人頁](../README.zh-TW.md) · [GitHub 原始碼](https://github.com/teddashh/mcp-memory-server) · [Repo README](https://github.com/teddashh/mcp-memory-server#readme) · [English](#english) · **繁體中文**

# MCP Memory Server

> 一套給 AI Agent 使用的結構化記憶介面：先做好專案內的本機回想，再選擇是否加入跨專案搜尋。

## 作品定位與快照

MCP Memory Server 是一個以 Python 實作的 AI 開發者基礎設施作品，服務任何支援 Model Context Protocol 的 client。它不把「記憶」當成一整段無差別的對話，而是拆成決策、已解問題、未解問題與知識，再提供寫入、回想、搜尋、重要性、稽核報表與系統狀態工具。

對目前 repo 最準確的定位，是一個**可檢查、仍在早期階段的 MCP 記憶伺服器與架構原型**。本機／Oracle adapter、tool contract、CJK token 估算與兩種 transport 已有程式碼；README 描述的完整四層系統，仍依賴這個公開 repo 沒有附上的資料庫檔案、schema 與同步工作。

| 快照 | 實際稽核結果 |
|---|---|
| 目標使用者 | 使用 Claude Code、Codex、Gemini CLI 或其他 MCP client 的開發者與系統維運者 |
| 套件 | <code>mcp-memory-server</code> <code>0.1.0</code>；Python 3.11+ |
| 執行方式 | FastMCP stdio，或 <code>127.0.0.1:8787/mcp</code> 上的 streamable HTTP |
| 實際註冊介面 | 目前 server code 共 **18 個 MCP tools** |
| 程式可連接的儲存 | 各 workspace 的 SQLite；選配 Oracle AI Database；Oracle vector query 可選配 OpenAI embeddings |
| Repo 規模 | 9 個 tracked files；主要 server module 841 行；沒有 tests、migration 或 schema DDL |
| 稽核基準 | 預設分支 <code>master</code>、commit <code>780e3b11d24eb44c5a04cbc65bee68119f912f24</code>，檢查日期 2026-07-11 |

## 要解決的問題

AI coding session 常常在重新開始後失去長期脈絡。下一個 Agent 可能不知道某個架構為什麼這樣選，也不知道某個 incident 是否早已解完。單純保存整份 transcript 還不夠：決策、未解問題與可重用知識，本來就有不同的欄位、查詢方式與生命週期。

這個作品同時處理三個問題：

1. **結構化延續。** 重要工作要帶著明確語意類型、workspace 與 tags 被保存，而不是消失在聊天紀錄裡。
2. **有用的回想。** 常用記憶應排在過時雜訊之前；設定雲端 index 後，也應能跨 workspace 搜尋。
3. **多語 context 控制。** 以 ASCII 為前提的字元估算法會嚴重低估中、日、韓文字，compact 判斷需要 CJK-aware estimator。

整體設計也試圖讓記憶不綁定單一 AI 供應商：client 都透過 MCP 呼叫相同工具，而資料仍由操作者掌握。

## 使用體驗與能力

### 捕捉與整理

五個寫入工具涵蓋快速記錄與明確類型：

- <code>memory_store</code> 把文字導向 decision、resolved、question 或 knowledge。
- <code>memory_record_decision</code> 保存選了什麼、為什麼、怎麼做與 tags。
- <code>memory_record_resolved</code> 保存問題與解法。
- <code>memory_record_question</code> 保存脈絡與 1 到 3 的優先序。
- <code>memory_record_knowledge</code> 建立知識項目，供外部流程之後同步。

自動分類器刻意做得很小：它檢查 “decided”、“fixed”、“should we” 等英文片語與問號。中文內容或較細緻的分類，應直接使用明確的 record tool。

### 回想、刪除與 salience

<code>memory_list</code>、<code>memory_get</code>、<code>memory_get_summary</code> 處理 workspace 本機回想；<code>memory_delete</code> 依 ID 直接刪除。成功執行 <code>memory_get</code> 時，系統會嘗試增加 access count、更新最後存取時間，並把 salience 乘上 1.05，上限 2.0。

<code>memory_reinforce</code> 提供手動 1.20 倍強化，同樣封頂 2.0。<code>--decay</code> 則把至少 30 天未存取的資料乘上 0.95；何時排程執行，由操作者自行處理。

### 搜尋與報表

搜尋會依現有基礎設施走不同路徑：

- 沒有 Oracle 時，server 逐一讀取 workspace map 裡的本機資料庫，針對 <code>knowledge_items</code> 做文字搜尋，再以 salience 排序。
- 有 Oracle、沒有 embedding 時，在雲端 knowledge 上做參數化文字搜尋，以 salience 與時間排序。
- 有 Oracle、OpenAI embedding provider 與 API key 時，用 Oracle <code>VECTOR_DISTANCE(..., COSINE)</code>，依 vector distance 排序。

四個 audit/report tools 會查 Oracle 的稽核紀錄、統計、每日報表與 Agent 活動；三個 admin tools 則顯示狀態、檢查 session 檔案大小、彙總雲端 workspace。目前的「compact」工具只量測並提出建議，不會自己重寫 session 檔。

### 程式實際註冊的 18 個 tools

| 類別 | Tools |
|---|---|
| 寫入（5） | <code>memory_store</code>、<code>memory_record_decision</code>、<code>memory_record_resolved</code>、<code>memory_record_question</code>、<code>memory_record_knowledge</code> |
| 讀取（3） | <code>memory_list</code>、<code>memory_get</code>、<code>memory_get_summary</code> |
| 刪除（1） | <code>memory_delete</code> |
| 搜尋（1） | <code>memory_search</code> |
| Salience（1） | <code>memory_reinforce</code> |
| 稽核軌跡（4） | <code>memory_audit_search</code>、<code>memory_audit_stats</code>、<code>memory_daily_report</code>、<code>memory_activity_log</code> |
| 管理（3） | <code>memory_status</code>、<code>memory_compact</code>、<code>memory_oracle_summary</code> |

## 架構與資料流

~~~mermaid
flowchart LR
    C["MCP clients<br/>Claude Code · Codex · Gemini"] --> T["FastMCP tool layer<br/>stdio 或本機 HTTP"]
    T --> W["Workspace 偵測<br/>env · cwd · workspace map"]
    W --> S["既有 workspace SQLite<br/>四種結構化記憶表"]
    T --> M["session.md 檢查<br/>CJK-aware 大小估算"]
    T --> O["選配 Oracle adapter<br/>跨專案知識與稽核"]
    T -. "選配 query embedding" .-> E["OpenAI embeddings"]
    E --> O
    O --> V["Oracle vector／文字搜尋"]
~~~

### 本機寫入流程

1. MCP client 呼叫一個明確 tool。
2. Server 從 <code>MEMORY_WORKSPACE</code>、<code>INIT_CWD</code>／目前目錄或 JSON workspace map 判定 workspace。
3. 它打開該專案既有的 <code>.claude-memory/memory.db</code>。
4. 它寫入 <code>decisions</code>、<code>resolved</code>、<code>open_questions</code> 或 <code>knowledge_items</code>，並使用 UUID 的前八個字元當 record ID。

### 搜尋流程

1. <code>memory_search</code> 先嘗試取得 Oracle connection。
2. Oracle 關閉時，改搜 mapped local knowledge databases。
3. Oracle 開啟時，如果 OpenAI extra 與 API key 可用，就先產生 query embedding。
4. 有 embedding 就走 cosine vector search；否則 fallback 到 Oracle 文字搜尋。

### 文件中的四層，與這個 repo 已實作的範圍

README 提出一個很清楚的四層模型：session Markdown、本機 SQLite、Git 可讀 Markdown、雲端資料庫。實際檢查公開程式碼後：

- Session layer 會被讀取，用來做 health check 與 token estimate。
- SQLite 的讀寫已實作，但 repo 沒有建立資料庫或 migration schema。
- Git／Markdown 同步層目前是架構文件；這裡沒有 exporter 或 sync job。
- Oracle query 已實作，但文件中的 18-table schema、provisioning scripts 與 sync pipeline 沒有附在 repo 內。

這個差異會直接影響安裝預期，也應在作品介紹裡說清楚。

## 關鍵設計與安全選擇

### 結構化記憶，而不是只保存 transcript

不同 table 能保留不同語意：decision 有 rationale、question 有 priority、knowledge 有 sync state。這比單純 key/value cache 更有領域模型，但代價是必須先準備相容 schema。

### CJK-aware 估算

Estimator 對一般 ASCII 字元約計 0.25 token；CJK、kana、Hangul 與全形字元計 1.5；高碼位 emoji 計 2.0。<code>memory_compact</code> 在超過 100 行或 3,000 estimated tokens 時提出 compact 建議。它是 deterministic heuristic，不是真正 tokenizer，但確實直接處理多語低估問題。

### 本機優先，雲端選配

HTTP mode 只綁 loopback，沒有直接暴露到所有網卡，能降低意外對外開放的風險。Oracle 在 runtime 可關閉，Oracle password 與 OpenAI API key 也能由環境變數覆寫；不過 package 安裝時仍會把 <code>oracledb</code> 當必要 dependency。

### Secret 與存取控制

互動設定會把 Oracle credentials、wallet password 與 embedding API key 寫進 <code>~/.mcp-memory/config.json</code>，輸入密碼時沒有隱藏，也沒有主動收緊檔案權限。Server constructor 沒有設定 MCP authentication。Loopback 是有用的邊界，但操作者仍應保護主機帳號、限制 config 權限、優先用環境變數或 secret manager；若要遠端使用，前面還需要 authenticated proxy。

### 失敗處理

Embedding 失敗時會安靜 fallback 到文字搜尋；部分 salience update 遇到舊 schema 欄位不存在時也會忽略錯誤。這能讓部分讀取在不完整設定下繼續，但也可能遮住 schema drift。正式維運需要 migration、明確 health check 與可觀測錯誤。

## 快速開始與使用

### 安裝套件

~~~bash
git clone https://github.com/teddashh/mcp-memory-server.git
cd mcp-memory-server

python3 -m venv .venv
source .venv/bin/activate
pip install -e .

# 選配：安裝 query embedding 需要的 OpenAI SDK
pip install -e '.[embeddings]'
~~~

本次檢查已實際 smoke-test editable install 與 HTTP 啟動。可用以下方式執行：

~~~bash
python -m mcp_memory            # MCP stdio
python -m mcp_memory --http     # http://127.0.0.1:8787/mcp
python -m mcp_memory --decay    # 執行一次 salience decay

# 安裝後的等價 console entry point
mcp-memory
~~~

### 設定 client

Stdio client 可把 MCP entry 指向 repo：

~~~json
{
  "mcpServers": {
    "memory": {
      "command": "python",
      "args": ["-X", "utf8", "-m", "mcp_memory"],
      "cwd": "/absolute/path/to/mcp-memory-server"
    }
  }
}
~~~

### 先補齊 repo 未提供的本機前置資料

<code>python setup_config.py</code> 會詢問 workspace path、選配 Oracle credentials 與 embedding provider；它**不會**建立 <code>workspace-map.json</code>、初始化 <code>memory.db</code> 或建立預期資料表。因此全新安裝若沒有從外部提供相容的 workspace metadata 與 SQLite schema，寫入工具還不能運作。

Oracle 路徑也假設 wallet、user 與相容資料表都已經存在。README 裡的 schema 表格應視為 contract／設計參考，不是 repo 內附 migration。

## 目前範圍、風險與授權

| 面向 | 目前狀態與影響 |
|---|---|
| Tool 數量 | README badge 與文字寫 19；實際 FastMCP registry 有 18 個，文件分類加總也是 18。本頁以程式註冊結果為準。 |
| 本機即裝即用 | README 描述 SQLite default；但全新 home 沒有 workspace map、database 或 schema。補齊前，<code>memory_store</code> 會得到 <code>FileNotFoundError</code>。 |
| 四層完整度 | Session 檢查、SQLite access 與 Oracle query 已有；Markdown export、scheduled sync、cloud DDL 與 provisioning 不在這個 repo。 |
| 設定 contract | README sample 使用通用 <code>cloud_db</code> object，實作實際讀取 <code>oracle</code> object。PostgreSQL、Supabase、MySQL 與 local embeddings 都是規劃項目，不是本 repo 已完成 backend。 |
| 自動分類 | Classifier 只認英文提示詞，沒有實作中文說明暗示的中文提示詞。 |
| Salience 細節 | 程式把強化封頂在 2.0，但 decay 沒有實作 README 所寫的 0.01 floor；Oracle vector result 依 cosine distance，而不是 salience 排序。 |
| Compact | Tool 只回報大小並告訴 Agent 應提取什麼，不會自動摘要、保存或裁切檔案。 |
| 品質控制 | Repo 沒有 automated tests、CI workflow、dependency lock、release 或 migration framework。 |
| 正式環境安全 | 本機明文 secret 與未設定 authentication 的 MCP construction，在共用或遠端部署前仍需強化。 |
| 授權 | README 寫「MIT」，badge 也連到 <code>LICENSE</code>，但**repo 實際沒有 LICENSE 檔，GitHub 也偵測不到 license**。目前原始碼公開可讀，正式重用條款卻不完整；應先補上預期的 MIT license，才適合毫無保留地稱為開源。 |

## 原始碼與文件連結

- [GitHub repository](https://github.com/teddashh/mcp-memory-server)
- [README 與架構說明](https://github.com/teddashh/mcp-memory-server#readme)
- [MCP tools 與 transport 實作](https://github.com/teddashh/mcp-memory-server/blob/master/mcp_memory/server.py)
- [SQLite／Oracle adapters](https://github.com/teddashh/mcp-memory-server/blob/master/mcp_memory/db.py)
- [設定載入](https://github.com/teddashh/mcp-memory-server/blob/master/mcp_memory/config.py)
- [套件 metadata 與 dependencies](https://github.com/teddashh/mcp-memory-server/blob/master/pyproject.toml)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

[← 上一篇：Claude Idea Review Skill](./claude-idea-review-skill.md#traditional-chinese) · [GitHub 公開作品集](./README.md#traditional-chinese) · [下一篇：同形異義字攻擊示範 →](./idn-homograph-example.md#traditional-chinese)
