# MVC / Razor 網頁系統 Wiki 文件格式

當文件對象是 MVC、Razor、後台管理系統、查詢/審核/報表頁、Controller + ViewModel + Service/Repository 架構時，使用此格式。

## 文件開頭

```md
# {系統功能或 Controller 名稱}
```

開頭用 1 到 2 段說明此 MVC/Razor 功能在系統中的定位、主要責任、使用者或外部系統輸入來源、輸出結果、資料保存位置，以及不負責的範圍。

## 建議章節

### 1. 功能概要

用表格說明此功能提供哪些後台或網頁流程。

| 功能 | 說明 | 維護注意 |
|---|---|---|

### 2. 使用情境

說明此頁面或功能在什麼情境被使用、由誰操作、每次操作會完成哪些資料查詢或狀態更新。

可用流程樹：

```text
使用者進入頁面
|-- Controller 接收 request
|-- 讀取查詢條件或 route parameter
|-- 呼叫 Service / Repository
|-- 查詢 MSSQL 或外部 API
|-- 組成 ViewModel
|-- 回傳 Razor View
```

### 3. 主要 Controller 與 Action

列出 Controller、Action、HTTP method、route、責任與回傳結果。

| Controller/Action | HTTP method | Route/入口 | 責任 | 回傳 |
|---|---|---|---|---|

維護提醒：若同一個 Action 同時處理查詢、寫入與匯出，文件要明確拆開責任，避免後續維護者誤改共用邏輯。

### 4. ViewModel / Model 結構

說明 ViewModel、Form model、Entity model 的角色差異。

| 類別 | 類型 | 主要欄位 | 用途 | 維護注意 |
|---|---|---|---|---|

必須說明：

- 查詢條件欄位。
- 畫面顯示欄位。
- 表單送出欄位。
- 下拉選單、checkbox、radio button 清單來源。
- 欄位驗證規則，例如 required、長度、格式、日期區間。

### 5. Razor View 與前端互動

說明 `.cshtml`、partial view、layout、script、form、table 或 modal 的責任。

| View/Partial | 用途 | 使用的 Model | 維護注意 |
|---|---|---|---|

若有 JavaScript、AJAX、React 元件或地圖嵌入，需說明前端與後端資料交換方式。

### 6. 輸入與輸出

#### 6.1 輸入來源

| 輸入 | 來源 | 格式/欄位 | 維護注意 |
|---|---|---|---|
| Route parameter | URL | 例如 `id` | 確認缺值或非法值處理。 |
| Query string | 查詢條件 | 例如 `keyword`、`startDate` | 確認空值與日期格式。 |
| Form post | Razor form | ViewModel 欄位 | 確認 `ModelState` 與防偽 token。 |
| API/AJAX | 前端呼叫 | JSON/form data | 確認錯誤回傳格式。 |

#### 6.2 輸出結果

| 輸出 | 目標 | 格式 | 維護注意 |
|---|---|---|---|
| View | Razor 頁面 | HTML | 確認 ViewModel 資料完整。 |
| JSON | AJAX/API | JSON | 確認狀態碼與錯誤訊息一致。 |
| 檔案 | 匯出下載 | Excel/CSV/PDF 等 | 確認權限、檔名與資料量。 |
| DB 更新 | MSSQL | Entity/狀態欄位 | 確認交易與重複送出。 |

### 7. Service / Repository / 資料存取

說明業務邏輯與資料存取的分工。

| 類別/方法 | 責任 | 輸入 | 輸出 | 維護注意 |
|---|---|---|---|---|

若使用 `DbContext`、Repository、stored procedure、raw SQL，必須說明查詢條件、資料表與關聯。

```sql
-- 重要 SQL 條件或查詢邏輯
```

### 8. 主要處理流程

用編號列出完整流程，包含進入頁面、查詢、表單驗證、寫入、錯誤回傳與成功導頁。

POST/Redirect/Get 流程需明確說明：

```text
GET Action
|-- 建立初始 ViewModel
|-- 載入下拉選單
|-- 回傳 View

POST Action
|-- 接收 Form Model
|-- 驗證 ModelState
|-- 驗證失敗：重建下拉選單並回傳 View
|-- 驗證成功：呼叫 Service 寫入
|-- RedirectToAction
```

### 9. 驗證規則與商業邏輯

將表單驗證、狀態轉換、權限檢查、日期區間、資料重複檢查獨立說明。

| 規則 | 判斷條件 | 結果 | 維護注意 |
|---|---|---|---|

若有狀態碼或流程狀態，必須用表格列出：

| 狀態碼/狀態 | 意義 | 使用位置 | 維護注意 |
|---|---|---|---|

### 10. 權限與安全性

說明登入、角色、授權、CSRF、防重複送出與輸入清理。

| 項目 | 說明 | 維護注意 |
|---|---|---|
| 認證 | 是否需要登入 | 未登入導向或錯誤處理。 |
| 授權 | 角色/群組/權限碼 | 權限變更需同步測試。 |
| CSRF | `ValidateAntiForgeryToken` | 表單 POST 需確認 token。 |
| 輸入驗證 | ModelState/自訂驗證 | 避免 SQL injection、XSS。 |

### 11. 外部相依與資料來源

列出 MSSQL、ArcGIS REST API、內部 API、檔案、NLog、第三方套件等。

| 相依項目 | 類型 | 用途 | 維護注意 |
|---|---|---|---|

若有 ArcGIS、GDB、FeatureClass、REST API、NLog、檔案輸出，必須明確列出資料流與失敗時要看哪裡。

### 12. 錯誤處理與例外情境

用表格列出錯誤情境與處理方式。

| 錯誤情境 | 處理方式 | 使用者/維護者可見結果 | 維護注意 |
|---|---|---|---|
| `ModelState` 驗證失敗 | 回傳原 View | 顯示驗證訊息 | 記得重建下拉選單。 |
| 查無資料 | 回傳空清單或提示 | 頁面顯示無資料 | 不應視為系統錯誤。 |
| DB 寫入失敗 | 捕捉例外並記錄 log | 顯示錯誤訊息 | 避免部分寫入。 |
| API 失敗 | 記錄 response | 顯示查詢失敗 | 保留 request 條件方便追查。 |

### 13. Log 與追蹤

說明 NLog 或既有 log 機制記錄哪些內容、位置與追查方式。

| Log 項目 | 內容 | 維護注意 |
|---|---|---|
| 進入 Action | 使用者、查詢條件、route parameter | 避免記錄敏感資料。 |
| 寫入結果 | 成功/失敗、資料筆數、主要鍵值 | 方便追查狀態異常。 |
| 例外 | exception、inner exception、stack trace | 保留足夠上下文。 |

### 14. 完整流程摘要

用 `text` 區塊列出從 Controller 入口到輸出結果的流程樹。

```text
Controller Action
|-- 驗證輸入
|-- 呼叫 Service
|-- 查詢/更新 MSSQL
|-- 組成 ViewModel 或回傳 JSON
|-- 寫入 log
|-- 回傳 View/Redirect/Json/File
```

### 15. 路徑與檔案

用表格列出 Controller、ViewModel、View、Service、Repository、設定檔、log、script 等用途。

| 路徑/檔案 | 用途 | 維護注意 |
|---|---|---|
| `Controllers/{Name}Controller.cs` | Controller 入口 | Action route 與權限需確認。 |
| `Views/{Name}/Index.cshtml` | Razor View | Model 欄位異動需同步。 |
| `Models/{Name}ViewModel.cs` | ViewModel | 驗證規則與顯示欄位需同步。 |
| `appsettings.json` | 設定 | 連線字串與外部服務 URL。 |

### 16. 維護風險與建議

條列容易壞掉的地方，例如：

- ViewModel 欄位改名但 `.cshtml` 的 `asp-for` 未同步。
- POST 驗證失敗回 View 時忘記重建下拉選單。
- Controller Action 同時承擔太多查詢、寫入與匯出責任。
- `DbContext` lifetime 設定錯誤。
- 查詢條件未限制資料量造成效能問題。
- 使用者重複送出造成重複寫入。
- 權限規則變更但 Action 或 View 未同步。
- 外部 API 或 ArcGIS service 欄位/schema 變動。
