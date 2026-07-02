# ArcGIS Experience 除錯

使用於 `ArcGIS Experience`、Experience Builder、widget、資料來源、Portal 權限、地圖服務整合問題。

## 常見表現

| 表現 | 優先檢查 |
|---|---|
| Widget 沒資料 | Data source 是否選對、圖層是否有資料、filter/query 是否過濾掉。 |
| 地圖圖層不顯示 | Web Map 圖層、服務權限、比例尺限制、definition query。 |
| 登入後才正常 | Portal/Online sharing、群組權限、token、匿名存取設定。 |
| 點選不同步 | Widget action、selection data view、資料來源 ID 是否一致。 |
| 發布後壞掉 | draft vs published 設定差異、資源路徑、權限未同步。 |

## 與 REST API 串接

- 先直接打 REST endpoint，確認 query 回傳正常。
- 確認 Experience 使用的 layer URL 與測試 URL 相同。
- 檢查欄位名稱大小寫與 alias 混用。
- 若服務需 token，確認 Experience 的登入與服務授權來源一致。

## 維護提醒

- Experience 適合組態式流程；若需要高度客製狀態或複雜商業流程，需判斷是否應轉為客製 Web GIS。
- 圖層 schema 或欄位改名會影響 widget、filter、popup 與 action。
