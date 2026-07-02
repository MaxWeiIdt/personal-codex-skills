# 執行期錯誤

使用於 crash、exception、Stack Trace、權限錯誤、API/SQL/GIS runtime failure。

## 判斷流程

1. 取得完整 Stack Trace 與 `InnerException`。
2. 找 Stack Trace 中第一個自有程式碼位置。
3. 確認例外類型與觸發輸入。
4. 找出該行依賴的資料、設定、外部服務或物件生命週期。

## 常見例外

| 例外 | 優先檢查 |
|---|---|
| `NullReferenceException` | 哪個物件為 null、是否初始化、查詢是否無資料。 |
| `InvalidOperationException` | 物件狀態、生命週期、集合修改時機、thread 限制。 |
| `SqlException` | 讀取 `mssql-debug.md`。 |
| `HttpRequestException` | 網路、DNS、憑證、HTTP 狀態碼、API response body。 |
| `UnauthorizedAccessException` | 檔案/資料夾權限、IIS AppPool、排程帳號、路徑是否存在。 |
| ArcGIS 相關例外 | 讀取 `arcgis-pro-sdk-debug.md` 或 `arcgis-rest-debug.md`。 |
| Python 例外 | Python path、stderr、exit code、套件版本、資料鎖定。 |

## 沒有 Stack Trace 時

- 在入口點加入最小範圍 try/catch。
- 輸出完整 exception type、message、stack trace、inner exception。
- 對外部程序記錄 stdout、stderr 與 exit code。
