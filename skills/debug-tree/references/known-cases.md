# Known Debug Cases

這份文件收錄已確認根因、可重複利用的除錯案例。使用方式是「先走 `SKILL.md` 的通用分類，再用本文件比對是否命中已知案例」，不要在尚未收集證據前直接套用舊修法。

## 收錄規則

| 規則 | 說明 |
|---|---|
| 必須有已確認根因 | 只收錄最後有驗證結果的案例，不收錄猜測。 |
| 必須有可辨識訊號 | 例如錯誤訊息、狀態碼、log 關鍵字、固定現象。 |
| 必須能重用 | 太專案限定、只能用一次的細節不要放進來。 |
| 不貼大量原始 log | 只保留可搜尋的關鍵片段與判斷線索。 |

## 新案例格式

```md
## 案例：{簡短名稱}

| 項目 | 說明 |
|---|---|
| 錯誤訊號 | `{錯誤訊息 / log 關鍵字 / 狀態碼}` |
| 發生情境 | {在哪種專案、哪個流程、什麼操作下發生} |
| 根因分類標籤 | `{環境問題/程式碼邏輯/資料問題/第三方 API/設定問題/相依版本/權限問題/效能問題}` |
| 已確認根因 | {最後確認的真正原因} |
| 優先檢查 | {下次遇到時先查什麼} |
| 修復方向 | {修法方向，不一定放完整程式碼} |
| 避免誤判 | {哪些看起來像但不是根因的方向} |
| 相關 reference | `{references/...}` |
```

## 案例：ArcGIS Pro SDK 存取圖層發生 Thread 錯誤

| 項目 | 說明 |
|---|---|
| 錯誤訊號 | `CalledOnWrongThreadException` |
| 發生情境 | Add-in、DockPane 或 ViewModel 中直接存取 `MapView.Active`、`FeatureLayer`、`Geodatabase`、`FeatureClass`。 |
| 根因分類標籤 | `程式碼邏輯` |
| 已確認根因 | 在非 ArcGIS Pro MCT thread 直接操作 Pro 領域物件。 |
| 優先檢查 | 出錯行是否直接讀寫 Map、Layer、Feature、GDB 物件。 |
| 修復方向 | 將 Pro SDK 領域物件操作移入 `QueuedTask.Run`。 |
| 避免誤判 | 不要把所有邏輯都塞進 `QueuedTask.Run`；長時間 IO/API 應移出 MCT。 |
| 相關 reference | `references/arcgis-pro-sdk-debug.md` |

## 案例：Console 手動可執行但排程失敗

| 項目 | 說明 |
|---|---|
| 錯誤訊號 | 手動執行成功，Task Scheduler 執行失敗、找不到設定檔、找不到輸出路徑或 log 未產生。 |
| 發生情境 | `C# Console` 批次、排程同步、資料轉檔、API 查詢流程。 |
| 根因分類標籤 | `環境問題`, `設定問題`, `權限問題` |
| 已確認根因 | 排程執行帳號、工作目錄、相對路徑、環境變數或權限與手動執行時不同。 |
| 優先檢查 | Task Scheduler 的 Start in、執行帳號、`appsettings.json` 實際位置、NLog 輸出路徑。 |
| 修復方向 | 改用明確工作目錄/絕對路徑，確認排程帳號權限，啟動時記錄目前目錄與設定檔路徑。 |
| 避免誤判 | 不要只看程式邏輯；排程失敗常是環境差異，不一定是程式碼壞掉。 |
| 相關 reference | `references/console-schedule-debug.md` |

## 案例：ArcGIS REST API 回傳 200 但實際失敗

| 項目 | 說明 |
|---|---|
| 錯誤訊號 | HTTP status 為 `200`，但 JSON 內含 `error`、`code`、`message`。 |
| 發生情境 | 查詢 `FeatureServer`、`MapServer`、Portal/Server REST API。 |
| 根因分類標籤 | `第三方 API`, `設定問題` |
| 已確認根因 | ArcGIS REST 有時以 HTTP 200 包裝服務層錯誤，真正錯誤在 response body。 |
| 優先檢查 | response JSON 是否包含 `error`，以及 `error.code`、`error.message`、`details`。 |
| 修復方向 | API wrapper 應同時檢查 HTTP status 與 ArcGIS JSON error。 |
| 避免誤判 | 不要只用 HTTP 200 判定成功。 |
| 相關 reference | `references/arcgis-rest-debug.md` |

## 案例：MVC/Razor POST 後下拉選單或清單消失

| 項目 | 說明 |
|---|---|
| 錯誤訊號 | 表單送出後 validation fail，View 重新顯示但 dropdown、checkbox list 或查詢清單變空。 |
| 發生情境 | `MVC` Controller POST action 回傳同一個 Razor View。 |
| 根因分類標籤 | `程式碼邏輯` |
| 已確認根因 | POST 驗證失敗回傳 View 前，沒有重新建立 ViewModel 內供畫面使用的選單/清單資料。 |
| 優先檢查 | `ModelState.IsValid == false` 分支是否有重新載入 dropdown/list。 |
| 修復方向 | 將建置選單資料抽成 helper，在 GET 與 POST validation fail 時都呼叫。 |
| 避免誤判 | 這通常不是 Razor rendering 問題，而是 ViewModel 資料未補齊。 |
| 相關 reference | `references/mvc-razor-debug.md` |

## 案例：MSSQL 查詢 Timeout

| 項目 | 說明 |
|---|---|
| 錯誤訊號 | `Timeout expired`、API 或批次卡在資料查詢階段。 |
| 發生情境 | MVC 查詢、Console 批次統計、API 查詢 MSSQL。 |
| 根因分類標籤 | `效能問題`, `資料問題` |
| 已確認根因 | 查詢條件、索引、資料量、lock 或 N+1 查詢造成資料庫耗時過長。 |
| 優先檢查 | SSMS execution plan、是否 table scan、where 條件是否可用索引、是否有 lock/deadlock。 |
| 修復方向 | 補索引、調整查詢條件、縮小資料範圍、批次化、避免 N+1。 |
| 避免誤判 | 不要只加大 `CommandTimeout`；那只能延後失敗，不等於修復根因。 |
| 相關 reference | `references/mssql-debug.md` |
