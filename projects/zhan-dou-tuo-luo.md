<a id="english"></a>

[← Public GitHub portfolio](./README.md) · [Ted's profile](../README.md) · [GitHub repository](https://github.com/teddashh/zhan-dou-tuo-luo) · [Guide index](https://github.com/teddashh/zhan-dou-tuo-luo#readme) · [Recommended builds](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/recommended-models.md) · [Training guide](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/techniques.md) · **English** · [繁體中文](#traditional-chinese)

# AI Beyblade X Field Guide

> A Traditional Chinese strategy notebook that turns scattered Beyblade X discussion into score-aware deck building, buying, practice, and match review.

## Positioning and snapshot

AI Beyblade X Field Guide—titled **AI 教你打陀螺** in the repository—is a content and knowledge-design project rather than a software application. It curates several AI Sister discussions, adds model/build notes and a training plan, and reframes the beginner question “Which Beyblade is strongest?” as a rules-and-resource problem.

Its distinctive idea is simple: a top that spins the longest is not automatically the best competitive choice. If a spin finish is usually worth 1 point, an over or burst finish 2, an Xtreme finish 3, and the match ends at 4, then role coverage and expected scoring matter more than a permanent single-item ranking.

| Snapshot | Audited repository reality |
|---|---|
| Audience | Traditional Chinese readers, new players, parents planning purchases, and players beginning 3v3 competition |
| Format | Git-native Markdown field guide; no build, runtime, account, or hosted application |
| Content map | README hub, four source-summary notes, one build/meta guide, and one practice/review guide |
| Repository size | 7 tracked Markdown files; 421 lines / 15,217 bytes |
| Evidence map | 6 unique external source URLs are linked across the guide |
| AI scope | Describes ways an AI agent can analyze rules, inventory, matchups, and reviews; contains no agent code, prompt engine, model call, or dataset |
| Audit basis | Default branch <code>main</code>, commit <code>ce33c834e06b4d3a2ebe698cb0fcdf1fe9be19f5</code>, reviewed 2026-07-11 |

## Problem

New players face an information problem before they face a mechanical one. Product videos reward spectacular collisions, forums discuss changing combinations, different regions sell different versions, and event rules can restrict parts or scoring. A simple shopping list quickly becomes stale and may teach the wrong lesson.

The repository replaces the “best top” question with four better questions:

1. What finish types and point values does this event use?
2. Is the format 1v1, 3v3, or a store-specific variation?
3. Which role does each combination serve—high-value attack, stable scoring, or counterplay?
4. Is a loss caused by the build, the matchup, the launch, the stadium, or the rule set?

That shift makes the guide useful even as individual parts move in and out of the metagame.

## Experience and capabilities

### A guided reading path

The root README acts as a concise table of contents. Four short notes preserve the argument of one forum post and three essays:

- the original “strongest version and highest-win-rate tactics” question;
- why point efficiency can matter more than spin duration;
- how a limited allowance should fund a complete deck rather than one dramatic purchase; and
- how to treat 3v3 as resource allocation under a scoring system.

Two longer documents then translate those arguments into action: a model/build guide and a launch/training guide.

### A score-aware 3v3 model

The guide organizes a deck into three jobs:

- **Scoring attacker:** accepts greater self-KO risk in exchange for 2- or 3-point finishes.
- **Stable core:** controls the center, limits unforced exits, and tries to collect lower-risk points.
- **Counter or closer:** targets a common local matchup or safely finishes the score line.

That model creates a common vocabulary for comparing combinations. It also prompts players to adjust risk based on whether they are leading or trailing, rather than launching every attack at maximum power.

### Buying and build guidance

The recommendation document covers six primary product families and their tactical roles:

| Family | Role described in the guide | Example direction |
|---|---|---|
| Wizard Rod | Stable center-control / stamina core | 3-60 Ball, 9-60 Hexa, or 9-60 Free Ball |
| Phoenix Wing | High-value attack | 1-60 Rush or 5-60 Point |
| Dran Buster | High-pressure attack | 3-60 Taper |
| Aero Pegasus | Balanced attack with more endurance | 7-60 Level or 3-60 Rush / Level |
| Cobalt Dragoon | Left-spin counter option | 7-60 Elevate or a Level direction |
| Silver Wolf | Stable substitute / endurance slot | 9-60 Ball or 9-60 Under Needle |

It gives three example decks—stable beginner, attack-led, and counter-oriented—then separates casual buying from competitive buying. US editions are positioned as accessible entry points; Takara Tomy / Japanese-system parts are suggested for players who need alignment with the competition and testing environment described by the guide.

A “Spring 2026 meta” section adds Shark Scale, Hover Wyvern, Blast / Pegasus Blast, Cobalt Dragoon, and Wizard Rod directions while repeatedly warning readers to check current restrictions and local legality.

### Practice as a measurable loop

The technique guide breaks launching into three foundational skills:

- flat launch for repeatable center occupation;
- angled launch for attack path and entry-angle control; and
- reduced-power launch at 70%, 80%, and 90% to avoid uncontrolled self-KOs.

The daily routine asks for 20 flat launches, 20 angled launches, 10 attack-path attempts, 10 simulated matchups, and a short review. A six-question pre-match checklist captures format, target score, restrictions, part-duplication rules, stadium conditions, and the job of each top. The post-match template records date, place, rules, deck, common opponent, loss pattern, primary cause, and the next adjustment.

### Where an AI assistant fits

The repository's AI concept is analytical rather than automated. A player can give an assistant:

- the current scoring and legality rules;
- owned blades, ratchets, and bits;
- common local opponent combinations;
- stadium and launch observations; and
- match results.

The assistant can then propose a matchup table, identify duplicated parts, challenge role coverage, create a practice menu, and classify the main loss cause. The repository explains this workflow but does not implement or call an AI service.

## Architecture and data flow

~~~mermaid
flowchart LR
    S["Linked discussions<br/>AI Sister · BeyBase · BeyCase"] --> N["Four source-summary notes"]
    N --> I["README reading map"]
    I --> B["Recommended models<br/>roles · example 3v3 decks · seasonal meta"]
    I --> T["Technique guide<br/>launch drills · checklist · review template"]
    R["Current local rules<br/>owned parts · opponent habits"] --> P["Player or external AI assistant"]
    B --> P
    T --> P
    P --> D["Choose 3v3 roles"]
    D --> M["Practice and match"]
    M --> Q["Record one primary loss cause"]
    Q --> P
~~~

This is an editorial data flow, not an application runtime. External discussions are summarized into short source notes; those notes support the recommendation and technique documents; the reader combines the framework with current local rules and observations; match notes feed the next decision.

The repository deliberately stores plain Markdown. GitHub renders it directly, links preserve the trail back to source material, and future updates can change one document without rebuilding an application.

## Key design and security choices

### Optimize a framework, not a permanent tier list

The stable layer is the decision model—point value, role coverage, risk, and counterplay. Named parts and a seasonal meta are treated as a replaceable layer. This separation is the right response to a game whose releases and restriction lists change.

### Make budget part of strategy

The guide speaks to parents and younger players as well as competitors. It frames a purchase as one slot in a three-top system and warns against spending the entire budget on a single attack product. That changes the recommendation from “buy the winner” to “build a testable, complementary set.”

### Turn embodied skill into observable data

Flat, angled, and reduced-power launches are not left as vague advice. Repetition counts and a single-primary-cause review make improvement measurable without sensors or special software.

### Keep sources visible

Each summary begins with its source URL, and the recommendation document adds the two comparison pages used for seasonal combinations. This is better than presenting AI-generated prose as source-free authority. It remains a lightweight citation scheme: individual claims do not carry dates, quotes, or evidence grades.

### Minimal technical attack surface

There is no executable code, dependency installation, form, network client, or credential handling. The main risks are informational and rights-related rather than runtime security: stale rules, incorrect purchase advice, source accuracy, trademark references, and unclear reuse permission.

## Quick start and use

### Read locally

~~~bash
git clone https://github.com/teddashh/zhan-dou-tuo-luo.git
cd zhan-dou-tuo-luo

# Start with README.md in GitHub, a Markdown viewer, or an editor.
~~~

No package installation or build step is required. The recommended sequence is:

1. Read the [guide index](https://github.com/teddashh/zhan-dou-tuo-luo#readme) for the scoring and 3v3 thesis.
2. Read the four source summaries to understand the reasoning behind it.
3. Use [recommended-models.md](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/recommended-models.md) to assign roles to parts you can actually obtain.
4. Use [techniques.md](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/techniques.md) to run repeatable launch drills.
5. Check the organizer's current rules before buying or entering.

### Use the notebook with an AI assistant

The following input structure operationalizes the workflow without pretending the repository already contains an agent:

~~~text
Event format and target score:
Current restriction / part-duplication rules:
My available blades, ratchets, and bits:
Common local opponent builds:
Stadium and launcher:
My last 10 results and finish types:

Build a legal 3v3 deck.
Give each combination one role.
Explain the expected scoring path and biggest counter.
Then design one 20-minute practice session and one match-review table.
State every rule or meta assumption that I must verify.
~~~

## Current scope, risks, and license

| Area | Current status and consequence |
|---|---|
| Product scope | A seven-file Traditional Chinese knowledge project. It is not a game, simulator, parts database, live tier list, or tournament-management app. |
| AI implementation | “AI” describes how an external assistant can reason over the notes. No model, prompt runner, API integration, agent, evaluation, or generated matchup matrix is committed. |
| Time sensitivity | The recommendation page is explicitly framed around Spring 2026. Releases, bans, local event formats, and regional part availability can invalidate named builds. |
| Rules evidence | Point values are described as “usually,” and the guide tells readers to verify the event. It does not link a primary official rules document or version each rule claim. |
| Build consistency | The text says some 3v3 formats prohibit repeated parts, but the “stable beginner” example uses Ball on both Wizard Rod and Silver Wolf. That deck may be illegal under the very no-duplication format described elsewhere and should be corrected or labeled as a practice inventory. |
| Recommendation evidence | Builds are editorial summaries, not results from a committed match dataset, simulation, controlled test, or reproducible ranking method. |
| Source rights | The repository links the discussions it summarizes but does not state the reuse license of those sources. Attribution does not by itself grant derivative-content permission. |
| Physical safety | The guide discusses aggressive launches and practice volume but contains no equipment, spectator-distance, age, or eye-protection safety section. Players should follow manufacturer and venue guidance. |
| Delivery | There is no live rendered site beyond GitHub's Markdown view, and no release, tag, CI workflow, or automated link check. |
| License | **The repository has no LICENSE file or license declaration, and GitHub detects no license.** It is publicly readable but not presently licensed for copying, modification, or redistribution. Add an explicit license and verify source-content permissions before describing it unambiguously as open source. |

## Source and documentation links

### Repository documents

- [GitHub repository and guide index](https://github.com/teddashh/zhan-dou-tuo-luo)
- [Forum-question summary](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/01-forum-248.md)
- [Point-efficiency note](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/02-blog-602.md)
- [Budget and deck-building note](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/03-blog-603.md)
- [Resource-allocation note](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/04-blog-604.md)
- [Recommended models and 3v3 examples](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/recommended-models.md)
- [Techniques, drills, and review template](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/techniques.md)

### External sources linked by the repository

- [AI Sister forum post 248](https://ai-sister.com/zh-TW/forum/post/248)
- [AI Sister blog 602](https://ai-sister.com/zh-TW/blog/602)
- [AI Sister blog 603](https://ai-sister.com/zh-TW/blog/603)
- [AI Sister blog 604](https://ai-sister.com/zh-TW/blog/604)
- [BeyBase combination article](https://beybase.com/top-5-best-beyblade-x-combos-up-to-ux-13-bx-39/)
- [BeyCase Spring 2026 combination list](https://www.beycase.com/post/best-top-5-beyblade-x-combo-list-for-spring-2026)

---

[← Previous: Homograph Attack Awareness Demo](./idn-homograph-example.md) · [Public GitHub portfolio →](./README.md)

---

<a id="traditional-chinese"></a>

[← GitHub 公開作品集](./README.md#traditional-chinese) · [Ted 個人頁](../README.zh-TW.md) · [GitHub 原始碼](https://github.com/teddashh/zhan-dou-tuo-luo) · [指南首頁](https://github.com/teddashh/zhan-dou-tuo-luo#readme) · [推薦配置](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/recommended-models.md) · [訓練指南](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/techniques.md) · [English](#english) · **繁體中文**

# AI 教你打陀螺

> 把分散的 Beyblade X 討論整理成一份繁中 field guide：從得分、3v3 配置與購買，一路走到訓練和賽後復盤。

## 作品定位與快照

AI 教你打陀螺（英文作品名：**AI Beyblade X Field Guide**）是一個內容與知識設計作品，不是 software application。它整理數篇 AI Sister 討論，補上型號／配置筆記與訓練菜單，再把新手常問的「哪顆最強？」重新定義成規則與資源配置問題。

核心觀念很直接：轉最久，不代表競技上一定最好。如果 spin finish 通常 1 分、over／burst 2 分、Xtreme finish 3 分，而比賽先到 4 分，那麼角色覆蓋與預期得分效率，就比永久單品排行榜更重要。

| 快照 | 實際稽核結果 |
|---|---|
| 目標讀者 | 繁中讀者、新手、規劃購買的家長，以及剛開始接觸 3v3 的玩家 |
| 形式 | Git-native Markdown field guide；沒有 build、runtime、帳號或 hosted app |
| 內容地圖 | README hub、四篇來源摘要、一篇配置／meta guide、一篇練習／復盤 guide |
| Repo 規模 | 7 個 Markdown files；共 421 行／15,217 bytes |
| 來源地圖 | 全站共連到 6 個不重複 external source URLs |
| AI 範圍 | 說明 AI Agent 如何分析規則、庫存、對位與賽後結果；沒有 agent code、prompt engine、model call 或 dataset |
| 稽核基準 | 預設分支 <code>main</code>、commit <code>ce33c834e06b4d3a2ebe698cb0fcdf1fe9be19f5</code>，檢查日期 2026-07-11 |

## 要解決的問題

新手在面對機械技巧之前，先遇到資訊問題。產品影片獎勵華麗碰撞、論壇討論不斷變動的組合、各地販售版本不同、活動規則又可能限制零件或計分。單純購物清單很快就過期，也可能教錯重點。

Repo 把「最強陀螺」換成四個更好的問題：

1. 這個活動有哪些 finish types，各拿幾分？
2. 賽制是 1v1、3v3，還是店家自訂？
3. 每個組合扮演什麼角色——搶大分、穩定得分，還是反制？
4. 一場輸掉，到底是 build、matchup、launch、stadium 還是 rules 造成？

即使單一零件進出 meta，這組思考方式仍然能留下來。

## 使用體驗與能力

### 有順序的閱讀路徑

Root README 是精簡目錄。四篇短筆記保留一則 forum 問題與三篇文章的論證：

- 原始「最強版本與最高勝率戰術」問題；
- 為什麼得分效率可能比續轉更重要；
- 零用錢有限時，為什麼應先組完整牌組，而不是只買一顆戲劇性單品；
- 怎麼把 3v3 視為計分規則下的資源配置。

接著由兩份較長文件落地：型號／配置 guide，以及發射／訓練 guide。

### 以得分為核心的 3v3 model

Guide 把牌組分成三個工作：

- **搶分攻擊手：** 接受較高自爆風險，交換 2 或 3 分 finish。
- **穩定核心：** 控中心、減少無謂出場，試著穩定拿低風險分數。
- **反制／收尾：** 針對當地常見 matchup，或安全補完最後比分。

這個模型讓不同組合有共同比較語言，也要求玩家依領先／落後調整風險，不是每次都最大力攻擊。

### 購買與配置指南

推薦文件涵蓋六個主要產品家族及其戰術角色：

| 系列 | 文件中的角色 | 例示方向 |
|---|---|---|
| Wizard Rod | 穩定中心控盤／持久核心 | 3-60 Ball、9-60 Hexa 或 9-60 Free Ball |
| Phoenix Wing | 高報酬攻擊 | 1-60 Rush 或 5-60 Point |
| Dran Buster | 高壓攻擊 | 3-60 Taper |
| Aero Pegasus | 有較多續航的平衡攻擊 | 7-60 Level 或 3-60 Rush／Level |
| Cobalt Dragoon | 左迴反制選項 | 7-60 Elevate 或 Level 方向 |
| Silver Wolf | 穩定替補／持久位置 | 9-60 Ball 或 9-60 Under Needle |

文件提供入門穩定、攻擊主導、反制三套 example decks，也把休閒購買與競賽購買分開：美版被定位成容易入坑；Takara Tomy／日版則推薦給需要對齊文件所描述競賽與測試環境的玩家。

「2026 春季 meta」另補 Shark Scale、Hover Wyvern、Blast／Pegasus Blast、Cobalt Dragoon 與 Wizard Rod 方向，同時反覆提醒讀者檢查當下限制與本地合法性。

### 把練習變成可量測 loop

技巧 guide 把發射拆成三個基本能力：

- 平射：練可重複的中心落點；
- 斜射：控制攻擊路線與進場角度；
- 70%、80%、90% 控力：避免失控自爆。

日常菜單包含平射 20 次、斜射 20 次、攻擊路線 10 次、對位模擬 10 局與短復盤。六題賽前 checklist 會記錄賽制、目標分、限制、零件重複規則、場地狀況與三顆功能；賽後模板則記日期、地點、規則、牌組、常見對手、輸法、主因與下次調整。

### AI assistant 放在哪裡

Repo 的 AI 概念是分析流程，不是 automation。玩家可以提供：

- 目前計分與合法性規則；
- 手上的 blades、ratchets、bits；
- 當地常見對手組合；
- stadium 與 launch 觀察；
- match results。

Assistant 接著可以產生 matchup table、檢查零件重複、挑戰角色覆蓋、設計練習菜單，並分類主要敗因。Repo 說明了這種用法，但沒有實作或呼叫 AI service。

## 架構與資料流

~~~mermaid
flowchart LR
    S["外部討論<br/>AI Sister · BeyBase · BeyCase"] --> N["四份來源摘要"]
    N --> I["README 閱讀地圖"]
    I --> B["推薦型號<br/>角色 · 3v3 examples · 季度 meta"]
    I --> T["技巧 guide<br/>發射練習 · checklist · 復盤模板"]
    R["當地規則<br/>手上零件 · 對手習慣"] --> P["玩家或外部 AI assistant"]
    B --> P
    T --> P
    P --> D["決定 3v3 角色"]
    D --> M["練習與比賽"]
    M --> Q["記錄一個主要敗因"]
    Q --> P
~~~

這是 editorial data flow，不是 application runtime。外部討論先被整理成短摘要；摘要支持推薦與技巧文件；讀者再把 framework 與當下規則、觀察結合；賽後筆記則回到下一輪決策。

Repo 刻意使用 plain Markdown。GitHub 可直接 render，source links 保留回溯路徑，未來也能只改一份文件，不必 rebuild app。

## 關鍵設計與安全選擇

### 最佳化 framework，不做永久 tier list

穩定層是 decision model：point value、role coverage、risk 與 counterplay。Named parts 與 seasonal meta 則是可替換層。面對不斷新增產品與更新限制表的遊戲，這種分層很合理。

### 把預算放進戰術

Guide 同時對家長、年輕玩家與競賽玩家說話。它把每次購買視為三顆系統中的一個 slot，提醒不要把全部預算押在單一攻擊產品。Recommendation 因此從「買冠軍」變成「組一套互補、可測的牌組」。

### 把身體技巧轉成可觀察資料

平射、斜射、控力不是抽象建議，而是有重複次數，復盤時也要求只選一個 primary cause。沒有 sensor 或特殊軟體，仍能形成可量測進步。

### 保留來源

每份摘要開頭都有 source URL，推薦文件也補上兩個季度組合比較頁。這比把 AI prose 當成沒有來源的權威好。不過仍屬輕量 citation：每個 claim 沒有各自 date、quote 或 evidence grade。

### 技術 attack surface 極小

沒有 executable code、dependency install、form、network client 或 credentials。主要風險不是 runtime security，而是 information 與 rights：過期規則、錯誤購買建議、來源準確度、商標引用，以及不明確的重用權。

## 快速開始與使用

### 在本機閱讀

~~~bash
git clone https://github.com/teddashh/zhan-dou-tuo-luo.git
cd zhan-dou-tuo-luo

# 用 GitHub、Markdown viewer 或 editor 從 README.md 開始。
~~~

不需要 package install 或 build。建議順序：

1. 先讀[指南首頁](https://github.com/teddashh/zhan-dou-tuo-luo#readme)，理解計分與 3v3 主張。
2. 讀四份來源摘要，掌握背後推理。
3. 用 [recommended-models.md](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/recommended-models.md) 替實際買得到的零件分配角色。
4. 用 [techniques.md](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/techniques.md) 執行可重複發射練習。
5. 購買或參賽前，再檢查 organizer 的當下規則。

### 和 AI assistant 一起使用

以下 input structure 能把 workflow 落地，又不會假裝 repo 已經包含 agent：

~~~text
活動賽制與目標分：
目前限制／零件重複規則：
我手上的 blades、ratchets、bits：
當地常見對手 builds：
Stadium 與 launcher：
最近 10 局結果與 finish types：

請組一套合法 3v3。
每顆只能分配一個主要角色。
說明預期得分路徑與最大 counter。
再設計一份 20 分鐘練習與一張賽後復盤表。
列出所有我必須另外確認的規則或 meta 假設。
~~~

## 目前範圍、風險與授權

| 面向 | 目前狀態與影響 |
|---|---|
| 產品範圍 | 七份繁中 Markdown 的知識作品；不是 game、simulator、parts database、live tier list 或 tournament app。 |
| AI 實作 | 「AI」是外部 assistant 如何讀筆記做推理；repo 沒有 model、prompt runner、API integration、agent、evaluation 或 generated matchup matrix。 |
| 時效 | Recommendation page 明確以 2026 春季為範圍。新品、ban、地方賽制與零件供應都可能讓 named build 過期。 |
| 規則證據 | 分數使用「通常」描述，文件也要求讀者查活動規則；repo 沒有連到 primary official rules，也沒有替每個規則 claim 標版本。 |
| 配置一致性 | 文字說有些 3v3 禁止重複零件，但「入門穩定型」同時讓 Wizard Rod 與 Silver Wolf 使用 Ball。這套在 no-duplication format 可能不合法，應修正或標成練習庫存。 |
| 推薦證據 | Builds 是 editorial summaries，不是 committed match dataset、simulation、controlled test 或 reproducible ranking method 的結果。 |
| 來源權利 | Repo 連回被摘要的討論，但沒有說明那些 source 的 reuse license。Attribution 本身不等於取得 derivative-content permission。 |
| 實體安全 | Guide 談高攻擊發射與練習量，但沒有 equipment、spectator distance、age 或 eye-protection safety section；應依 manufacturer 與場地方針。 |
| 交付 | 除 GitHub Markdown view 外沒有 live rendered site，也沒有 release、tag、CI 或 automated link check。 |
| 授權 | **Repo 沒有 LICENSE 檔或 license declaration，GitHub 也偵測不到 license。** 目前公開可讀，但沒有授權複製、修改或散布；在明確稱為開源前，應補 license 並確認 source-content permission。 |

## 原始碼與文件連結

### Repo 文件

- [GitHub repository 與指南首頁](https://github.com/teddashh/zhan-dou-tuo-luo)
- [Forum 問題摘要](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/01-forum-248.md)
- [得分效率筆記](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/02-blog-602.md)
- [預算與牌組筆記](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/03-blog-603.md)
- [資源配置筆記](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/04-blog-604.md)
- [推薦型號與 3v3 examples](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/recommended-models.md)
- [技巧、訓練與復盤模板](https://github.com/teddashh/zhan-dou-tuo-luo/blob/main/docs/techniques.md)

### Repo 連結的外部來源

- [AI Sister forum post 248](https://ai-sister.com/zh-TW/forum/post/248)
- [AI Sister blog 602](https://ai-sister.com/zh-TW/blog/602)
- [AI Sister blog 603](https://ai-sister.com/zh-TW/blog/603)
- [AI Sister blog 604](https://ai-sister.com/zh-TW/blog/604)
- [BeyBase 組合文章](https://beybase.com/top-5-best-beyblade-x-combos-up-to-ux-13-bx-39/)
- [BeyCase 2026 春季組合](https://www.beycase.com/post/best-top-5-beyblade-x-combo-list-for-spring-2026)

---

[← 上一篇：同形異義字攻擊示範](./idn-homograph-example.md#traditional-chinese) · [回到 GitHub 公開作品集 →](./README.md#traditional-chinese)
