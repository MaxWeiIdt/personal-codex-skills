# MSSQL 除錯

使用於 `SqlException`、EF Core、連線、timeout、deadlock、資料寫入錯誤。

## SqlException 對應

| 錯誤 | 優先檢查 |
|---|---|
| Timeout expired | 執行計畫、索引、資料量、lock、`CommandTimeout`。 |
| Login failed | 連線字串、帳密、SQL Server 驗證模式、防火牆。 |
| Cannot insert duplicate key | PK/Unique 約束、重跑策略、identity seed。 |
| Deadlock victim | transaction 範圍、資料表存取順序、隔離層級。 |
| String or binary data truncated | 欄位長度與輸入資料長度。 |
| 連線池耗盡 | `DbContext`/`SqlConnection` 是否正確釋放。 |

## EF Core / MVC 檢查

- `DbContext` 在 MVC 中應為 `Scoped`，不要註冊成 `Singleton`。
- 避免在 `foreach` 內觸發 N+1 查詢。
- 大量寫入評估 `SqlBulkCopy` 或批次寫入。
- 查詢結果與預期不符時，先把 SQL 印出或在 SSMS 直接驗證。

## 資料一致性

- 確認 transaction 是否有 commit。
- 確認排程重跑是否會重複 insert 或覆蓋資料。
- 若有狀態欄位，確認狀態轉移是否不可逆或需補償機制。
