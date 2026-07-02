# 效能問題

使用於查詢慢、API 慢、UI 卡頓、GIS 服務慢或批次耗時異常。

## 先量測

不要直接猜瓶頸。先加入最小計時點：

| 層級 | 量測方式 |
|---|---|
| MSSQL | SSMS execution plan、IO/time、查詢耗時。 |
| 後端 API | controller/service/repository 分段計時。 |
| 前端 | browser devtools、network、render 次數。 |
| GIS service | request duration、回傳筆數、geometry 大小。 |
| Console | 每個批次階段 log 開始/結束時間。 |

## 常見根因

| 類型 | 優先檢查 |
|---|---|
| 資料庫慢 | Table scan、缺索引、查詢條件不可 sargable、lock。 |
| 後端慢 | N+1 查詢、重複 API 呼叫、不必要序列化、大量資料一次載入。 |
| 前端慢 | 不必要 re-render、大量 DOM、圖片/圖資太大、同步阻塞。 |
| GIS 慢 | Feature Service 回傳筆數過多、未指定欄位、未簡化 geometry、無 tile cache。 |
