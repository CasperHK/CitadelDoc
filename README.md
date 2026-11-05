# CitadelDoc 堡壘文檔 🛡️

CitadelDoc 是一個基於 **Go 語言** 與 **Grit 框架** 構建的企業級**安全文檔框架 (Secure Documentation Framework)**。與傳統的靜態網站生成器（如 Docusaurus）不同，CitadelDoc 採用**伺服器端存取控制**與**縱深防禦 (Defense in Depth)** 架構，確保您的核心架構設計、內部 API 規格書與機密技術資產在網路上得到全方位的安全防護。

> Login to Review, Secure Exposed to Authorized User.
> 登入查看，安全資訊僅對授權使用者可見。

---

## ✨ 核心特性

- **🛡️ 伺服器端縱深防禦**：文檔絕不在前端裸奔。所有請求必須通過 Grit 中間件的身份驗證（JWT / Session），未授權使用者無法獲取任何文檔位元組。
- **⚡ 強悍效能與輕量化**：依賴 Go 語言的極致效能與高效的 Markdown 動態解析，徹底擺脫 Node.js 臃腫的 `node_modules` 與複雜的運行時開銷。
- **🔒 安全響應防護 (Secure Headers)**：原生內置嚴格的 Content-Security-Policy (CSP)、X-Frame-Options 與 X-Content-Type-Options，從根本上杜絕 XSS 與點擊劫持（Clickjacking）。
- **🧭 靈活的路由安全檢查**：內建路徑淨化機制，強制防範路徑穿越攻擊 (Path Traversal Attacks, 如 `../../`)。
- **📦 單一二進制部署**：編譯後生成無狀態的單一靜態二進制檔案（Static Binary），完美契合 Docker 與 Kubernetes 容器化基礎設施，部署極致便利。

---

### 📊 內建審計日誌 (Built-in Audit Logging)

CitadelDoc 具備嚴格的行為審計能力。系統內建的審計中間件會攔截每一個文檔存取請求，並即時記錄以下維度，以便進行合規性審查與異常行為分析（如：集中的文檔拖取行為）：

- **WHO**：存取使用者的唯一識別碼（如 User ID、Email 或 員工編號）
- **WHEN**：精確到毫秒的存取時間戳（Timestamp）
- **WHAT**：使用者請求的具體文檔路徑（Document URI）
- **WHERE**：請求來源的客戶端真實 IP 位址（Client IP）與 User-Agent

日誌預設輸出至標準輸出（Stdout），並可輕鬆對接企業內部的 **SIEM（安全資訊和事件管理）伺服器**、ELK Stack 或專屬的審計資料庫。


---

## 👥 角色與權限控制 (RBAC)

CitadelDoc 採用「讀寫分離、設計開放」的權限模型，確保前端開發的靈活性，同時嚴格保護文檔的寫入安全：

| 角色 (Role) | 訪問前端 (/docs) | 設計/調整 UI 樣式 | 編輯文檔內容 | 存取後端寫入 API |
| :--- | :---: | :---: | :---: | :---: |
| **所有使用者 / 設計師** (All Users) | ✅ 允許 | ✅ 允許 | ❌ 拒絕 | ❌ 拒絕 (401 Unauthorized) |
| **已登入編輯者** (Authenticated Editor) | ✅ 允許 | ✅ 允許 | ✅ 允許 | ✅ 允許 (通過 JWT / Session) |

### 🛠️ 運作機制
1. **公開前端設計**：所有人皆可自由訪問前端路由並使用 UI 組件（例如基於 Solid.js 的排版沙盒），調整文檔的閱讀視覺與前端配置。
2. **編輯者權限驗證**：文檔頁面內置編輯入口。當使用者觸發「編輯頁面」時，系統會驗證其登入狀態。未登入者將引導至 `/admin/login`。
3. **安全 API 守衛**：所有文檔的修改與保存皆通過後端受保護的 API（如 `POST /api/v1/docs/save`）進行。該 API 強制掛載 `EditorAuthRequired` 中間件，任何未授權的寫入請求皆會被拒絕並觸發安全審計日誌。


---

## 🌐 Active Directory (AD) 系統整合

CitadelDoc 支援原生整合 Windows Active Directory / LDAP 伺服器，實現企業級的統一身份驗證與自動化權限指派：

1. **認證 (Authentication)**：編輯者在 `/admin/login` 輸入 AD 帳號密碼，後端透過安全連接（LDAPS / TLS）向 AD 網域控制器進行 Bind 認證。
2. **授權 (Authorization)**：認證成功後，系統會自動拉取該用戶的 `memberOf`（群組成員身份）與 `department`（部門）屬性。
3. **動態對應**：
   - 只有屬於指定 AD 群組（例如 `CN=CitadelDoc_Editors,OU=Groups,DC=domain,DC=local`）的用戶，系統才會核發代表編輯者權限的 JWT Token。
   - 所有人（包含一般 AD 用戶或訪客）皆可直接訪問前端進行介面設計與文檔閱讀。

---

## 🏗️ 技術棧架構

CitadelDoc 採用現代化且注重安全高效的架構設計：

- **Backend 核心**：Go + Grit Framework (高效路由與安全中間件)
- **Markdown 解析引擎**：`yuin/goldmark` (符合 CommonMark 標準且具備高擴展性)
- **Frontend 互動層**：可搭配 **Solid.js** 實現前後端分離的極速交互，或使用 Go HTML Template 進行一體化安全渲染。

---

## 🚀 快速開始

### 前置需求
- Go 1.20 或更高版本
- Git

### 1. 克隆項目
```bash
git clone https://github.com
cd CitadelDoc
```

### 2. 安裝依賴
```bash
go mod tidy
```

### 3. 配置文檔與運行
將您的 Markdown 文檔放入受保護的內部分錄（例如 `./markdown_files`），然後啟動伺服器：
```bash
go run main.go
```
伺服器預設將在 `http://localhost:8080` 啟動，請訪問 `/docs/` 開始閱讀受保護的文檔。

---

## 📁 項目目錄結構

```text
CitadelDoc/
├── main.go               # 項目入口與 Grit 路由配置
├── middleware/           # 安全與身份驗證中間件 (Auth, CSP, RateLimit)
│   ├── auth.go
│   └── security.go
├── parser/               # Markdown 解析與安全過濾邏輯
│   └── markdown.go
├── markdown_files/       # 受保護的 Markdown 文檔目錄 (不對外公開)
│   ├── index.md
│   └── api/
├── templates/            # 伺服器端渲染模板 (或 Solid.js 前端構建產物)
│   └── doc_template.html
├── go.mod
└── README.md
```

---

## 🔒 安全配置指引

### 啟用嚴格的 CSP (Content Security Policy)
在 `middleware/security.go` 中，您可以根據部署環境調整 CSP 策略，嚴格限制外部腳本的執行：
```go
c.Header("Content-Security-Policy", "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline';")
```

### 路徑防穿越檢查
系統在處理動態文檔路由時，會自動執行 `filepath.Clean()`。請勿移除此層防護，以確保惡意請求無法探索伺服器上的敏感檔案。
```markdown
<FollowUp>
這份 README 範本是否符合你的項目預期？接下來，我們可以開始著手為 CitadelDoc 設計**具體的安全中間件程式碼（如 JWT 驗證與 CSP 設定）**，或是規劃如何將 **Solid.js 前端與 Grit 後端 API 進行整合**？請告訴我你的下一步計劃。
</FollowUp>
