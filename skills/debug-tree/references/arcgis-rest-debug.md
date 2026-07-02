# ArcGIS REST API 除錯

使用於 `FeatureServer`、`MapServer`、Portal token、query、geometry、CORS、HTTP status 或 ArcGIS JSON error。

## HTTP 與 ArcGIS Error

| 表現 | 優先檢查 |
|---|---|
| HTTP 200 但 JSON 有 `error` | 讀 ArcGIS error code/message，不要只看 HTTP status。 |
| `400 Bad Request` | 參數格式、`where`、geometry JSON、`spatialRel`。 |
| `401/403` | Token、Portal/Server 權限、服務分享設定。 |
| `498 Invalid Token` | Token 是否過期、referer、產生 token 的 Portal/Server 是否一致。 |
| `500` | ArcGIS Server log、服務是否啟動、查詢是否過重。 |
| CORS | 前端直連限制，是否需後端 proxy 或 server CORS 設定。 |

## 常見地雷

- 忘記帶 `f=json`，回傳 HTML 而非 JSON。
- `where` 字串未用單引號，例如 `NAME = 'value'`。
- ArcGIS JSON 與 GeoJSON 格式混用。
- `outFields=*` 造成回傳過大，應指定必要欄位。
- 未使用 pagination 或 `resultOffset/resultRecordCount` 導致筆數上限問題。
