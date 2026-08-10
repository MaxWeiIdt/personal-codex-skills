# GIS／ETL 資料流程設計參考

## 1. 流程階段檢查表

| 階段 | 必須說明 |
|---|---|
| 取得來源 | 來源系統、查詢條件、時間範圍、分頁／批次方式、認證與逾時 |
| 暫存 | 暫存位置、批次編號、原始欄位保存、重跑是否覆蓋 |
| 驗證 | 必填欄位、型別、唯一鍵、筆數、空間範圍、座標系統、幾何有效性 |
| 轉換 | 欄位對應、空間轉換、去重規則、狀態轉換與商業規則 |
| 寫回 | 寫入目標、交易邊界、更新／新增／刪除規則、鎖定與權限 |
| 結案 | 成功條件、批次摘要、錯誤明細、產物保留期限與告警 |

## 2. 批次與狀態設計

建議每次執行有不可變的 `BatchId`，並至少記錄：

- `StartedAt`、`CompletedAt`、執行主機與執行版本。
- 來源條件或水位，例如最後更新時間、OBJECTID 範圍或檔案版本。
- `ReadCount`、`ValidCount`、`WrittenCount`、`SkippedCount`、`ErrorCount`。
- `Status`：`Started`、`Validated`、`Writing`、`Completed`、`PartialFailed`、`Failed`、`RolledBack`。
- 錯誤明細的資料鍵、階段、錯誤類型與可否重試。

避免只用一個 `IsProcessed` 欄位判斷整批成功；批次狀態與資料列狀態應分開。

## 3. 重跑策略

| 情境 | 建議策略 |
|---|---|
| 來源取得失敗 | 保留 BatchId，確認是否可安全重試；不要產生無法追蹤的新批次 |
| 驗證失敗 | 不進入正式寫回，保留原始資料與錯誤報告 |
| 寫回中斷 | 依交易邊界回滾，或使用 staging／checkpoint 從明確階段續跑 |
| 部分資料失敗 | 將成功與失敗資料分開記錄，允許依資料鍵重跑 |
| 重複執行 | 以來源唯一鍵、批次鍵或版本欄位保護冪等性 |
| 正式資料誤寫 | 先停止後續批次，保存批次範圍，再依核准的回滾或補償方案處理 |

任何「直接 truncate 後重匯」或「先刪除再寫入」方案，都必須說明備份、鎖定、失敗窗口與回復方式。

## 4. GIS 特有檢查

- ArcGIS REST：檢查 HTTP 狀態與 response 內的 `error`，處理分頁、Token、逾時、附件與服務限制。
- FeatureClass／SDE：確認 workspace、schema、版本／編輯模式、Schema Lock、欄位別名與 geometry type。
- 空間資料：確認座標系統、幾何有效性、空間範圍、重疊／相交規則與 XY tolerance。
- MSSQL：確認 transaction scope、索引、deadlock／timeout、批次大小與寫入順序。
- Python／arcpy：釋放 cursor、layer 與 workspace 參考，避免暫存檔與 schema lock 殘留。

## 5. 驗收與告警

至少定義：

1. 來源與目標筆數差異的允許範圍。
2. 必填欄位、唯一鍵、幾何無效與座標系統錯誤的處置。
3. 成功、部分成功與失敗的 exit code／批次狀態。
4. Log 中可用 `BatchId` 找回完整流程。
5. 逾時、錯誤率、筆數異常與連續失敗的告警條件。
6. 成功後的抽樣查詢或 REST／SQL／地圖驗證。

