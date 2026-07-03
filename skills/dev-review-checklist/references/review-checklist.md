# Review Checklist Reference

## 通用審查

| 檢查項 | 風險 |
|---|---|
| 變更是否符合需求邊界 | 無關重構容易引入額外 regression。 |
| 錯誤處理是否保留原始例外或關鍵上下文 | 失敗後無法追查排程或 API 問題。 |
| log 是否足以判斷成功、失敗、無資料、部分成功 | 排程容易誤判成功。 |
| 設定值是否硬編碼 | 部署環境差異會失敗。 |
| 是否有重跑/重試策略 | 資料可能重複寫入或半套更新。 |
| 是否有測試或可驗證步驟 | 後續修改無保護。 |

## ArcGIS Pro SDK / CoreHost

| 檢查項 | 風險 |
|---|---|
| Pro SDK API 是否在正確 thread 使用，例如 `QueuedTask.Run` | UI crash、deadlock、狀態不同步。 |
| FeatureClass、RowCursor、Geodatabase 是否正確釋放 | GDB lock、檔案無法刪除或覆寫。 |
| MapView、Layer、Selection 是否處理 null 與不存在 | 使用者切換專案/圖層後失敗。 |
| 事件訂閱是否有取消 | DockPane 重開後重複觸發或 memory leak。 |
| GP Tool / Python 參數與工作目錄是否明確 | 開發機可跑，部署機失敗。 |

## ArcGIS REST API

| 檢查項 | 風險 |
|---|---|
| Token 過期、HTTP 200 但 body 有 error 是否處理 | 誤判查詢成功。 |
| 分頁、`resultOffset`、`exceededTransferLimit` 是否處理 | 大量資料漏抓。 |
| geometry、spatialReference、where 條件是否明確 | 空間查詢結果錯誤。 |
| timeout 與 retry 是否合理 | 排程卡住或 API 短暫失敗即中止。 |

## MVC / Razor

| 檢查項 | 風險 |
|---|---|
| ViewModel 與 Razor 欄位是否一致 | post 回來資料遺失。 |
| `ModelState` 與 validation 是否處理 | 錯誤資料寫入。 |
| 是否有權限檢查與 CSRF 防護 | 未授權操作或表單攻擊。 |
| 查詢條件是否參數化 | SQL injection 或查詢錯誤。 |
| Controller 是否混入過多資料處理邏輯 | 難測試、難維護。 |

## Console / Python / 排程

| 檢查項 | 風險 |
|---|---|
| exit code 是否反映成功/失敗 | 排程平台無法偵測失敗。 |
| log 是否包含輸入參數、筆數、輸出位置 | 事後無法查問題。 |
| 重跑是否 idempotent | 重複寫入、覆蓋錯誤、狀態混亂。 |
| Python executable、venv/conda、requirements 是否明確 | 排程環境與開發環境不同。 |
| 輸入檔缺漏、schema 改變、空資料是否分開處理 | 無資料與失敗混淆。 |

## MSSQL / 資料處理

| 檢查項 | 風險 |
|---|---|
| 寫入是否使用 transaction 或補償策略 | 部分成功導致資料不一致。 |
| 查詢是否有索引與合理條件 | 效能退化或 timeout。 |
| schema 變更是否同步程式、文件與測試資料 | 部署後 runtime fail。 |
| 日期、時區、編碼、座標系是否明確 | 比對錯誤或資料偏移。 |

## Findings 範例

```md
## Findings

| Severity | Location | Issue | Impact | Suggested Fix |
|---|---|---|---|---|
| High | `Program.cs:42` | 排程重跑時直接 insert，沒有去重或 transaction。 | 失敗重跑會產生重複資料。 | 以批次識別碼或 unique key 做 upsert，並將寫入包在 transaction。 |
```
