<a id="english"></a>

[← Public GitHub portfolio](./README.md) · [Ted's profile](../README.md) · [GitHub repository](https://github.com/teddashh/idn-homograph-example) · [Live demo](https://project-9ogsa.vercel.app) · [Repository README](https://github.com/teddashh/idn-homograph-example#readme) · **English** · [繁體中文](#traditional-chinese)

# IDN Homograph Attack Awareness Demo

> A bilingual, one-page security lesson that makes a nearly invisible Unicode-domain substitution visible.

## Positioning and snapshot

This project is a deliberately small security-awareness experience for colleagues, onboarding groups, and non-specialist audiences. Instead of beginning with a definition, it presents two URLs that look almost identical, asks the learner to choose the fake one, and then explains why a different Unicode code point can produce a deceptive domain.

It is best positioned as an **interactive teaching artifact**, not a phishing simulator or training-management platform. The entire experience is one static HTML document with embedded styling and a tiny language-toggle script. It needs no application server, account, database, analytics, or build pipeline.

| Snapshot | Audited repository reality |
|---|---|
| Audience | Employees, SOC colleagues, security-awareness presenters, and bilingual Chinese/English teams |
| Delivery | One responsive static page deployed on Vercel or opened locally |
| Stack | HTML5, embedded CSS, vanilla JavaScript, Vercel configuration, Google Fonts |
| Content | Interactive URL pair, seven-step attack chain, five Latin/Cyrillic comparisons, attack patterns, and defenses |
| Repository size | 3 tracked files; <code>index.html</code> is 1,052 lines / 37,149 bytes |
| Live verification | The Vercel page returned HTTP 200 and matched the repository HTML byte-for-byte by SHA-256 on 2026-07-11 |
| Audit basis | Default branch <code>main</code>, commit <code>8b0443c8848a14c7c7e4498d540430a26f09067b</code> |

## Problem

Conventional phishing guidance often says “inspect the URL,” but that advice is incomplete when two characters from different writing systems render almost identically. The learner may be looking carefully and still miss the substitution.

The page demonstrates the concrete difference between:

- the legitimate Latin-letter domain <code>fpcusa.com</code>; and
- <code>fpcusа.com</code>, whose final “a” is Cyrillic <code>U+0430</code> rather than Latin <code>U+0061</code>.

The latter encodes as <code>xn--fpcus-8ve.com</code>. The lesson turns that technical fact into a memorable sequence: see the ambiguity, inspect the address representation, understand Punycode, connect it to a realistic attack chain, and leave with defensive actions.

## Experience and capabilities

### A fast teaching hook

The page opens in Traditional Chinese and offers an English switch. The main experiment puts the legitimate and lookalike links side by side with the answer visible as “Real” and “Fake.” A presenter can hide or de-emphasize the badges verbally, ask the group to decide, and then use the warning box to reveal the Punycode representation.

### A seven-stage threat narrative

The content follows an attack from hotel-partner targeting through impact:

1. spear-phish a Booking.com partner and place an information stealer;
2. steal Extranet credentials, cookies, or tokens;
3. trade initial access;
4. obtain reservation and guest information;
5. impersonate the hotel through a trusted channel;
6. place an IDN lookalike link in the message; and
7. capture payment details or induce a transfer.

This broader sequence is an important teaching choice: the URL trick is not shown as an isolated magic trick, but as one component in credential theft, trusted-context abuse, and social engineering.

### Explain the underlying characters

A three-column comparison table shows five confusable pairs:

| Latin | Cyrillic | Visual lesson |
|---|---|---|
| <code>a · U+0061</code> | <code>а · U+0430</code> | Different code points can share a glyph |
| <code>c · U+0063</code> | <code>с · U+0441</code> | Visual inspection alone can fail |
| <code>e · U+0065</code> | <code>е · U+0435</code> | Unicode script identity matters |
| <code>o · U+006F</code> | <code>о · U+043E</code> | IDN display policy becomes a defense |
| <code>p · U+0070</code> | <code>р · U+0440</code> | Familiar brand strings can be imitated |

### Move from awareness to action

The final sections cover phishing and smishing use, credential-harvesting pages, account takeover, and defensive practices. Individual guidance includes opening the known app or bookmark instead of following a message link, checking the registrable domain, enabling phishing-resistant MFA, and keeping the browser current. Organization-level guidance includes defensive registration, DMARC/SPF/DKIM, DNS monitoring, threat intelligence, simulations, newly registered domain monitoring, and DNSTwist.

### Presentation and language behavior

- The language buttons toggle a class on <code>body</code>, update the active button, and change the document's <code>lang</code> attribute between <code>zh-TW</code> and <code>en</code>.
- Embedded CSS provides the dark visual system, responsive behavior below 640 px, motion, cards, attack-chain timeline, and character table.
- The content is entirely client-side; the repository contains no form submission, cookies, local storage, fetch request, or analytics code.

## Architecture and data flow

~~~mermaid
flowchart LR
    U["Learner / presenter"] --> V["Vercel static delivery<br/>or local HTTP server"]
    V --> H["index.html"]
    H --> C["Embedded CSS<br/>layout · responsive design · motion"]
    H --> J["Vanilla JS<br/>Chinese / English toggle"]
    H --> X["External assets<br/>Google Fonts · remote logo"]
    H --> L["Outbound lesson links<br/>real domain · lookalike domain · BBC source"]
    V --> R["vercel.json headers<br/>nosniff · DENY framing · no-referrer · noindex"]
~~~

There is no server-side data flow. Vercel returns the same HTML, CSS, and JavaScript for every visitor. The only interaction changes which pre-rendered language blocks are visible. Clicking a source or demonstration URL leaves the site and opens an external origin in a new tab.

The deployment configuration adds clean URLs and four response headers to every route:

- <code>X-Content-Type-Options: nosniff</code>
- <code>X-Frame-Options: DENY</code>
- <code>Referrer-Policy: no-referrer</code>
- <code>X-Robots-Tag: noindex, nofollow</code>

The document also includes a matching robots meta tag. The current Vercel response adds HSTS.

## Key design and security choices

### A static page lowers operational friction

With no package manager or framework, a facilitator can double-click the file, run a one-line local server, or deploy it to any static host. The lack of a backend also means the project itself collects no credentials or learner data.

### Bilingual content is authored, not machine-translated at runtime

Both language versions live in the HTML and switch instantly. The core copy and interaction do not depend on a translation service, while each version can use natural security vocabulary. The tradeoff is duplicated copy that must be kept synchronized; remote fonts and the logo still depend on the network.

### Browser defenses become part of the lesson

The README explicitly treats a 404, warning page, or Punycode display as an expected observation. That is pedagogically useful: a browser that refuses to render a suspicious IDN as friendly Unicode is demonstrating its security policy.

### Outbound-link isolation

External links use <code>target="_blank"</code> with <code>rel="noopener"</code>, and the deployment suppresses the referrer. Framing is denied. These are sound safeguards for a static awareness page.

### The live fake-domain link needs stronger containment

The lesson directly links to the lookalike domain; it does not show an interstitial or prevent navigation. The repository does not document ownership or continuing control of that domain. A domain that is harmless or unresolved today could be registered or repointed later. A production training version should use a controlled domain, intercept the click locally, or render the Punycode as non-clickable text.

### Third-party assets and policy gaps

Fonts load from Google, and the brand image is hotlinked from <code>somochem.com</code>. That introduces availability, privacy, and content-control dependencies even though the app has no backend. There is no Content-Security-Policy, Subresource Integrity, automated accessibility check, or test suite. Self-hosting approved assets and setting a narrow CSP would make an internal deployment easier to govern.

## Quick start and use

### Preview locally

~~~bash
git clone https://github.com/teddashh/idn-homograph-example.git
cd idn-homograph-example

python3 -m http.server 8000
# Open http://localhost:8000
~~~

Opening <code>index.html</code> directly also works, although a local server more closely matches deployment behavior.

### Deploy to Vercel

~~~bash
npm install -g vercel
vercel

# Publish later updates to production
vercel --prod
~~~

The README also documents importing the GitHub repository into Vercel with framework preset “Other,” or uploading a zip. There is no build command or output-directory transformation.

### Suggested facilitation flow

1. Open the [live demo](https://project-9ogsa.vercel.app) and ask which URL is fake before discussing Unicode.
2. Compare the final characters and reveal <code>U+0061</code> versus <code>U+0430</code>.
3. Show <code>xn--fpcus-8ve.com</code> and explain that Punycode is an encoding, not itself evidence that a domain is malicious.
4. Walk through the seven-stage chain to show why correct reservation details can increase trust.
5. Finish with one individual behavior and one organization-level control the audience can apply.

For a live corporate session, avoid asking attendees to visit an uncontrolled lookalike domain. The visual comparison and local click handler are enough to teach the point safely.

## Current scope, risks, and license

| Area | Current status and consequence |
|---|---|
| Product scope | A single awareness lesson, not a learning management system, quiz engine, phishing campaign tool, or telemetry service. |
| Live status | The documented Vercel URL was live and served the audited <code>index.html</code> on 2026-07-11. Third-party assets and outbound domains remain independently changeable. |
| Search visibility | <code>noindex</code> asks compliant crawlers not to index the page; it is not authentication or confidentiality. The footer calls the material “Internal Security Awareness,” while the repository and deployment are public. |
| Demonstration safety | The fake-looking domain is a real outbound link with no ownership statement or interstitial. Its future behavior cannot be guaranteed by this code. |
| Content accuracy | The FPCUSA example correctly maps to <code>xn--fpcus-8ve.com</code>. A separate Booking.com illustration says a Cyrillic “о” maps to <code>xn--bking-qmb.com</code>; encoding the described <code>bоoking.com</code> yields <code>xn--boking-wqf.com</code>, so that example should be corrected. |
| Evidence | The page links one BBC article, but high-impact numerical and comparative claims are not individually footnoted. Treat them as lesson copy pending source-by-source verification. |
| Branding and assets | The page names Formosa USA / Inteplast and hotlinks a third-party-hosted logo. Public reuse should confirm trademark, brand, and image permission. |
| Quality controls | No automated HTML validation, browser test, accessibility test, dependency audit, release, or CI workflow is present. |
| License | **No LICENSE file or license declaration exists in the repository, and GitHub detects no license.** Public visibility is not permission to copy, modify, or redistribute. Add an explicit license—and separately clear third-party branding/assets—before calling the project unambiguously open source. |

## Source and documentation links

- [GitHub repository](https://github.com/teddashh/idn-homograph-example)
- [Live Vercel demonstration](https://project-9ogsa.vercel.app)
- [Repository README and presenter notes](https://github.com/teddashh/idn-homograph-example#readme)
- [Single-page implementation](https://github.com/teddashh/idn-homograph-example/blob/main/index.html)
- [Vercel response-header configuration](https://github.com/teddashh/idn-homograph-example/blob/main/vercel.json)
- [BBC source linked by the lesson](https://www.bbc.com/news/articles/cly00jnnxypo)

---

[← Previous: MCP Memory Server](./mcp-memory-server.md) · [Public GitHub portfolio](./README.md) · [Next: AI Beyblade X Field Guide →](./zhan-dou-tuo-luo.md)

---

<a id="traditional-chinese"></a>

[← GitHub 公開作品集](./README.md#traditional-chinese) · [Ted 個人頁](../README.md#traditional-chinese) · [GitHub 原始碼](https://github.com/teddashh/idn-homograph-example) · [線上示範](https://project-9ogsa.vercel.app) · [Repo README](https://github.com/teddashh/idn-homograph-example#readme) · [English](#english) · **繁體中文**

# IDN Homograph Attack Awareness Demo

> 用一個中英雙語單頁，把肉眼幾乎看不見的 Unicode 網域替換，變成能親眼理解的資安風險。

## 作品定位與快照

這是一個刻意保持小而直接的 security-awareness 體驗，服務同事、onboarding 團體與非資安背景受眾。它不先丟出定義，而是先放兩個幾乎一樣的網址，請學員判斷哪個是假的，再解釋不同 Unicode code point 為什麼能組成欺騙性網域。

最準確的定位是**互動式教學素材**，而不是 phishing simulator 或訓練管理平台。整個體驗只有一份 static HTML，內嵌 CSS 與一小段語言切換 JavaScript；不需要 application server、帳號、database、analytics 或 build pipeline。

| 快照 | 實際稽核結果 |
|---|---|
| 目標受眾 | 一般員工、SOC 同事、資安講師，以及中英雙語團隊 |
| 交付方式 | 一個 responsive static page，可部署 Vercel 或本機開啟 |
| 技術 | HTML5、embedded CSS、vanilla JavaScript、Vercel config、Google Fonts |
| 內容 | 互動網址對照、七步攻擊鏈、五組拉丁／西里爾字元、攻擊模式與防禦方式 |
| Repo 規模 | 3 個 tracked files；<code>index.html</code> 1,052 行／37,149 bytes |
| 線上驗證 | 2026-07-11 檢查時 Vercel 回傳 HTTP 200，SHA-256 與 repo HTML 完全一致 |
| 稽核基準 | 預設分支 <code>main</code>、commit <code>8b0443c8848a14c7c7e4498d540430a26f09067b</code> |

## 要解決的問題

傳統 phishing 教育常說「看清楚網址」，但兩個來自不同文字系統的字元若長得幾乎相同，這句話仍不完整。學員可能已經很仔細，卻依然看不出替換。

這個頁面具體比較：

- 使用拉丁字母的正常網域 <code>fpcusa.com</code>；以及
- <code>fpcusа.com</code>，最後一個 “a” 其實是西里爾 <code>U+0430</code>，不是拉丁 <code>U+0061</code>。

後者會編碼成 <code>xn--fpcus-8ve.com</code>。教學把技術事實安排成可記住的流程：先看見模糊性、觀察網址表示、理解 Punycode、接到真實感較高的攻擊鏈，最後帶走防禦行動。

## 使用體驗與能力

### 快速抓住注意力

頁面預設繁體中文，可切換 English。主要實驗把真網址與 lookalike link 並排，並顯示「正牌／假的」答案。講師可先口頭請大家忽略 badge、做出判斷，再用下方提示揭露 Punycode。

### 七階段威脅敘事

內容從飯店合作夥伴被攻擊一路走到實際損失：

1. spear-phish Booking.com partner 並植入 info stealer；
2. 偷走 Extranet credentials、cookies 或 tokens；
3. 交易 initial access；
4. 取得訂房與房客資料；
5. 透過可信 channel 冒充飯店；
6. 在訊息加入 IDN lookalike link；
7. 擷取付款資料或誘導轉帳。

這個敘事很重要：URL 技巧不是孤立魔術，而是 credential theft、可信脈絡濫用與 social engineering 的一環。

### 解釋真正不同的字元

三欄對照表放入五組 confusable characters：

| 拉丁 | 西里爾 | 教學重點 |
|---|---|---|
| <code>a · U+0061</code> | <code>а · U+0430</code> | 不同 code point 可以長得一樣 |
| <code>c · U+0063</code> | <code>с · U+0441</code> | 單靠目視可能失敗 |
| <code>e · U+0065</code> | <code>е · U+0435</code> | Unicode script identity 很重要 |
| <code>o · U+006F</code> | <code>о · U+043E</code> | IDN display policy 也是防線 |
| <code>p · U+0070</code> | <code>р · U+0440</code> | 熟悉的品牌字串可以被模仿 |

### 從 awareness 走到 action

最後兩段整理 phishing／smishing、credential-harvesting page、account takeover 與防禦做法。個人層包含自行開啟熟悉 App／bookmark、不從訊息連結登入、檢查 registrable domain、使用 phishing-resistant MFA、保持 browser 更新；組織層則包含 defensive registration、DMARC/SPF/DKIM、DNS monitoring、threat intelligence、simulation、NRD monitoring 與 DNSTwist。

### 畫面與語言行為

- 語言按鈕會切換 <code>body</code> class、更新 active button，並把 HTML <code>lang</code> 在 <code>zh-TW</code> 與 <code>en</code> 之間切換。
- Embedded CSS 完成深色視覺、640 px 以下 responsive layout、motion、cards、攻擊鏈 timeline 與字元表格。
- 所有內容都在 client side；repo 沒有 form submission、cookie、local storage、fetch request 或 analytics code。

## 架構與資料流

~~~mermaid
flowchart LR
    U["學員／講師"] --> V["Vercel static delivery<br/>或本機 HTTP server"]
    V --> H["index.html"]
    H --> C["Embedded CSS<br/>layout · responsive · motion"]
    H --> J["Vanilla JS<br/>中／英文切換"]
    H --> X["外部 assets<br/>Google Fonts · remote logo"]
    H --> L["外連教學連結<br/>正常網域 · lookalike · BBC source"]
    V --> R["vercel.json headers<br/>nosniff · DENY framing · no-referrer · noindex"]
~~~

這裡沒有 server-side data flow。Vercel 對每位訪客回傳相同 HTML、CSS 與 JavaScript；唯一互動只是決定哪一組已經寫好的語言區塊可見。點擊 source 或示範 URL 會離開本站，在新 tab 開啟外部 origin。

部署設定會對所有 route 加上四個 response headers：

- <code>X-Content-Type-Options: nosniff</code>
- <code>X-Frame-Options: DENY</code>
- <code>Referrer-Policy: no-referrer</code>
- <code>X-Robots-Tag: noindex, nofollow</code>

Document 裡也有相符的 robots meta；目前 Vercel response 另有 HSTS。

## 關鍵設計與安全選擇

### Static page 降低操作門檻

沒有 package manager 或 framework，講師可以 double-click 檔案、執行一行 local server，或丟到任何 static host。沒有 backend 也代表作品本身不收 credentials 或學員資料。

### 雙語內容是預先撰寫，不是 runtime machine translation

兩種語言都在 HTML 裡，切換即時、不依賴翻譯服務，且各自能使用自然的資安術語。代價是後續修改時要同步維護兩份 copy。

### 把 browser 防護變成教學的一部分

README 明確把 404、warning page 或 Punycode display 視為預期觀察。這是很好的教學設計：browser 不願意把可疑 IDN 顯示成友善 Unicode，本身就是 security policy 正在工作。

### 外部連結隔離

外連使用 <code>target="_blank"</code> 與 <code>rel="noopener"</code>，部署也不送 referrer，並禁止 framing。以 static awareness page 來說，這些都是合理防護。

### 線上 fake-domain link 仍需更強 containment

教材會直接連到 lookalike domain，沒有 interstitial，也不會攔住 navigation。Repo 沒有說明該網域由誰持有或能否持續控制。今天無害或無解析的網域，未來仍可能被註冊或改指向。正式訓練版應改用受控 domain、在本機攔截 click，或只把 Punycode 當不可點文字顯示。

### 第三方 asset 與 policy 缺口

字型從 Google 載入，brand image 則 hotlink <code>somochem.com</code>；即使 app 沒有 backend，仍有 availability、privacy 與 content-control dependency。Repo 沒有 Content-Security-Policy、Subresource Integrity、automated accessibility check 或 test suite。內部部署若能 self-host 核准素材並加上窄範圍 CSP，會更容易治理。

## 快速開始與使用

### 本機預覽

~~~bash
git clone https://github.com/teddashh/idn-homograph-example.git
cd idn-homograph-example

python3 -m http.server 8000
# 開啟 http://localhost:8000
~~~

直接開 <code>index.html</code> 也能使用；local server 會更接近正式部署行為。

### 部署到 Vercel

~~~bash
npm install -g vercel
vercel

# 之後更新 production
vercel --prod
~~~

README 也寫了兩種方式：從 GitHub import，Framework Preset 選 “Other”；或直接上傳 zip。這個專案沒有 build command，也沒有 output directory transformation。

### 建議講解流程

1. 開啟[線上示範](https://project-9ogsa.vercel.app)，先問哪個 URL 是假的，不先談 Unicode。
2. 比較最後一個字元，揭露 <code>U+0061</code> 與 <code>U+0430</code>。
3. 顯示 <code>xn--fpcus-8ve.com</code>，同時說明 Punycode 只是一種 encoding，本身不等於惡意。
4. 走完七階段攻擊鏈，解釋為什麼正確訂房資料會增加可信度。
5. 讓每個人帶走一項個人行為與一項組織控制。

正式企業課程不要要求學員造訪未受控 lookalike domain；視覺比較與 local click handler 已足以安全示範。

## 目前範圍、風險與授權

| 面向 | 目前狀態與影響 |
|---|---|
| 產品範圍 | 一堂單頁 awareness lesson，不是 LMS、quiz engine、phishing campaign tool 或 telemetry service。 |
| 線上狀態 | 2026-07-11 檢查時，文件中的 Vercel URL 正在服務這份 audited <code>index.html</code>；第三方 assets 與外連 domains 仍可獨立變動。 |
| 搜尋能見度 | <code>noindex</code> 只是在請守規則的 crawler 不收錄，不是 authentication 或 confidentiality。Footer 寫 “Internal Security Awareness”，但 repo 與 deployment 都是 public。 |
| 示範安全 | 假網址是一個沒有 ownership statement 或 interstitial 的真外連，未來行為不受這份 code 保證。 |
| 內容正確性 | FPCUSA example 正確對應 <code>xn--fpcus-8ve.com</code>。另一個 Booking.com illustration 說西里爾 “о” 對應 <code>xn--bking-qmb.com</code>；依敘述編碼 <code>bоoking.com</code> 會得到 <code>xn--boking-wqf.com</code>，這一例應修正。 |
| 證據 | 頁面連到一篇 BBC article，但高影響數字與比較性主張沒有逐項 footnote，應先逐條驗證再當成正式教材結論。 |
| 品牌與 assets | 頁面使用 Formosa USA／Inteplast 名稱，並 hotlink 第三方 hosted logo；公開重用前應確認 trademark、brand 與 image permission。 |
| 品質控制 | Repo 沒有 automated HTML validation、browser test、accessibility test、dependency audit、release 或 CI workflow。 |
| 授權 | **Repo 沒有 LICENSE 檔或任何 license declaration，GitHub 也偵測不到 license。** 公開可讀不等於可複製、修改、散布；在明確稱為開源前，應補上授權，並另外處理第三方品牌／素材權利。 |

## 原始碼與文件連結

- [GitHub repository](https://github.com/teddashh/idn-homograph-example)
- [Vercel 線上示範](https://project-9ogsa.vercel.app)
- [README 與講師提示](https://github.com/teddashh/idn-homograph-example#readme)
- [單頁實作](https://github.com/teddashh/idn-homograph-example/blob/main/index.html)
- [Vercel security headers](https://github.com/teddashh/idn-homograph-example/blob/main/vercel.json)
- [教材引用的 BBC 來源](https://www.bbc.com/news/articles/cly00jnnxypo)

---

[← 上一篇：MCP Memory Server](./mcp-memory-server.md#traditional-chinese) · [GitHub 公開作品集](./README.md#traditional-chinese) · [下一篇：AI 教你打陀螺 →](./zhan-dou-tuo-luo.md#traditional-chinese)
