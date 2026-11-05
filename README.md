# CitadelDoc 堡壘文檔 🛡️

CitadelDoc 是一個基於 **Go 語言** 與 **Grit 框架** 構建的企業級**安全文檔框架 (Secure Documentation Framework)**。與傳統的靜態網站生成器（如 Docusaurus）不同，CitadelDoc 採用**伺服器端存取控制**與**縱深防禦 (Defense in Depth)** 架構，確保您的核心架構設計、內部 API 規格書與機密技術資產在網路上得到全方位的安全防護。

---

## ✨ 核心特性

- **🛡️ 伺服器端縱深防禦**：文檔絕不在前端裸奔。所有請求必須通過 Grit 中間件的身份驗證（JWT / Session），未授權使用者無法獲取任何文檔位元組。
- **⚡ 強悍效能與輕量化**：依賴 Go 語言的極致效能與高效的 Markdown 動態解析，徹底擺脫 Node.js 臃腫的 `node_modules` 與複雜的運行時開銷。
- **🔒 安全響應防護 (Secure Headers)**：原生內置嚴格的 Content-Security-Policy (CSP)、X-Frame-Options 與 X-Content-Type-Options，從根本上杜絕 XSS 與點擊劫持（Clickjacking）。
- **🧭 靈活的路由安全檢查**：內建路徑淨化機制，強制防範路徑穿越攻擊 (Path Traversal Attacks, 如 `../../`)。
- **📦 單一二進制部署**：編譯後生成無狀態的單一靜態二進制檔案（Static Binary），完美契合 Docker 與 Kubernetes 容器化基礎設施，部署極致便利。

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
