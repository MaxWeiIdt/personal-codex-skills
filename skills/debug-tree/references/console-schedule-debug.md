# Console 排程 / 批次除錯

使用於 `C# Console`、Windows 工作排程器、批次流程、API 同步、資料轉檔、NLog、排程重跑問題。

## 手動可跑、排程失敗

| 檢查項目 | 說明 |
|---|---|
| 工作目錄 | Task Scheduler 的 Start in 是否正確。 |
| 執行帳號 | 是否有 DB、檔案、網路磁碟、API 權限。 |
| 設定檔路徑 | `appsettings.json` 是否在執行目錄，是否用相對路徑。 |
| 環境變數 | PATH、Python、ArcGIS Pro、proxy 是否和手動執行不同。 |
| 32/64 位元 | CoreHost、ODBC、ArcGIS runtime 是否需要 x64。 |

## 批次流程問題

- API 空回傳要區分「正常無資料」與「查詢失敗」。
- Token 過期要記錄可辨識錯誤，不要只顯示泛用 exception。
- 寫入 MSSQL 前確認防重複策略。
- 若可重跑，確認重跑不會重複累計或覆蓋錯誤資料。
- NLog 應記錄開始、結束、筆數、錯誤、耗時與關鍵參數。

## Python / 外部程序

- 記錄 executable path、arguments、working directory。
- 收集 stdout、stderr、exit code。
- 確認排程帳號可讀取 Python 腳本、輸入檔、輸出資料夾。
