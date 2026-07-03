# Test Strategy Reference

## 測試類型選擇

| 情境 | 建議測試 |
|---|---|
| Pure function / 資料轉換 | Unit test，輸入/輸出表格化。 |
| MVC Controller | Controller test 或 service test，確認 validation 與 redirect/view result。 |
| Service / Repository | 使用 fake repository、test DB 或 transaction rollback。 |
| Console / 排程 | 測 CLI args、設定讀取、exit code、log 與重跑。 |
| Python ETL | pytest，測 dataframe/schema、空值、欄位缺漏與輸出檔。 |
| ArcGIS REST API client | mock HTTP response，測 token 失效、分頁、body error。 |
| MSSQL | 小型 test data，測 upsert、transaction、索引相關查詢。 |

## 測試案例模板

| Case | Given | When | Then | Type |
|---|---|---|---|---|

## Mock Data 原則

- 保留最少欄位，但要包含所有判斷欄位。
- 至少包含 happy path、空資料、缺欄位、格式錯誤、重複資料。
- GIS 資料要標明座標系與 geometry 條件。
- 日期測試要包含邊界時間與時區假設。

## 常用命令提示

```powershell
dotnet test
pytest
npm test
```

## Coverage 缺口判斷

優先補：

- 最近修改的條件分支。
- 曾經發生 bug 的流程。
- 寫入資料庫或輸出檔案的流程。
- 外部 API 失敗與 timeout。
- 排程重跑與部分成功。
