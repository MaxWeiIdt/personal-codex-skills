---
name: debug-tree
description: "程式碼除錯與根因分析（RCA）決策樹。當程式碼編譯失敗、執行崩潰、邏輯錯誤、效能低落、測試不通過、排程失敗、MVC/Razor 畫面異常、ArcGIS Pro SDK/ArcGIS REST API/ArcGIS Experience/MSSQL/Python/React 發生問題，或使用者要求協助追查 bug、錯誤訊息、不能用、跑不起來時使用。"
---

# Debug 決策樹

## 目的

使用此 Skill 進行結構化除錯與根因分析。目標不是快速猜答案，而是依錯誤證據分類、縮小範圍、定位根因、最小修復，並清楚回報驗證結果。

適用技術棧包含：`C# Console`、`MVC`、`Razor`、`ArcGIS Pro SDK`、`Console CoreHost`、`ArcGIS REST API`、`ArcGIS Experience`、`React`、`HTML`、`Python`、`MSSQL`。

## 使用時機

遇到以下情境時啟動此 Skill：

- 使用者回報程式有 bug、異常行為、不能用、跑不起來。
- 執行命令、建置、測試、排程或部署時出現錯誤訊息。
- Runtime crash、exception、HTTP/API 錯誤、SQL 錯誤、ArcGIS 錯誤。
- 邏輯結果不符合預期，但程式沒有明確報錯。
- 查詢過慢、UI 卡頓、Feature Service 回應慢或批次執行時間異常。

## 核心原則

- 不要猜測根因；先收集錯誤訊息、Stack Trace、log、輸入資料、重現步驟與最近變更。
- 不要一開始就大改；先定位最小可疑範圍。
- 修改前先確認工作區是否已有使用者變更，避免覆蓋不屬於本次除錯的內容。
- 只修改與根因直接相關的檔案與邏輯。
- 修復後必須執行合理驗證；若不能驗證，明確說明原因與剩餘風險。

## 第一階段：錯誤分類

先依錯誤表現選擇分支，再讀取對應 reference：

| 分支 | 錯誤表現 | 讀取 reference |
|---|---|---|
| A | 編譯、建置、套件、專案參照失敗 | `references/build-errors.md` |
| B | Runtime crash、exception、Stack Trace、權限、API/SQL/GIS 例外 | `references/runtime-errors.md` |
| C | 程式不報錯，但結果與預期不符 | `references/logic-errors.md` |
| D | 查詢慢、API 慢、UI 卡頓、GIS 服務慢 | `references/performance-errors.md` |
| E | IIS、部署、環境、連線、Python/ArcGIS Pro 環境問題 | `references/environment-deploy.md` |
| F | MVC/Razor 表單、Model Binding、View、Controller、Layout 問題 | `references/mvc-razor-debug.md` |
| G | Console 排程、批次流程、設定、NLog、重跑與資料狀態問題 | `references/console-schedule-debug.md` |
| H | ArcGIS Experience widget、資料來源、權限、Portal/REST 整合問題 | `references/arcgis-experience-debug.md` |

## 技術專用參考

當錯誤明確落在特定技術時，優先讀取：

| 技術 | Reference |
|---|---|
| ArcGIS Pro SDK / CoreHost / Python Bridge | `references/arcgis-pro-sdk-debug.md` |
| MSSQL / EF Core / SQL Timeout / Deadlock | `references/mssql-debug.md` |
| ArcGIS REST API / Token / FeatureServer / MapServer | `references/arcgis-rest-debug.md` |

## 已知案例庫

當錯誤訊號、log 關鍵字、狀態碼或發生情境與過去案例相近時，讀取 `references/known-cases.md`。

使用規則：

- 先完成第一階段錯誤分類，再比對已知案例。
- 已知案例只能作為優先檢查方向，不可取代證據收集與根因驗證。
- 若案例命中，回報時說明「比對到的已知案例」與「本次實際確認的根因」。
- 若修復後確認是可重用的新案例，可建議補進 `known-cases.md`。

## 修復 SOP

1. 記錄問題
   - 錯誤訊息全文。
   - Stack Trace 或 log 片段。
   - 重現步驟。
   - 影響範圍與最近變更。
2. 分析根因
   - 依錯誤分類讀取對應 reference。
   - 找到第一個落在自有程式碼或自有設定的錯誤點。
   - 用資料、log、查詢結果或最小重現驗證推論。
3. 實施修復
   - 只改與根因直接相關的程式碼、設定或文件。
   - 避免順手重構無關區塊。
   - 若發現使用者未提交變更，保留並順著現況處理。
4. 驗證修復
   - 重新執行原本失敗的命令、測試、操作或排程片段。
   - 視情境補跑單元測試、建置、API 查詢、SQL 查詢或手動驗證。
   - 檢查是否產生副作用。
5. 回報結果
   - 說明問題分類、根因分類標籤、根因、修改內容、驗證方式與剩餘風險。

## 回報格式

根因分類標籤用於日後回顧與統計。優先使用下列表籤；若問題跨多類，可用逗號列出 2 到 3 個。

| 標籤 | 使用時機 |
|---|---|
| `環境問題` | 本機/伺服器/排程帳號/IIS/Python/ArcGIS Pro 安裝或權限差異。 |
| `程式碼邏輯` | 條件判斷、流程順序、狀態轉移、演算法或資料處理邏輯錯誤。 |
| `資料問題` | DB 資料、GDB/FeatureClass、輸入檔、缺值、格式或 schema 異常。 |
| `第三方 API` | 外部 API、ArcGIS REST、Portal/Server、Token 或遠端服務回應異常。 |
| `設定問題` | `appsettings.json`、`web.config`、排程參數、連線字串或部署設定錯誤。 |
| `相依版本` | NuGet、SDK、ArcGIS Pro、Python package、前端套件版本不相容。 |
| `權限問題` | 檔案、資料夾、DB、Portal、IIS AppPool 或排程帳號權限不足。 |
| `效能問題` | 查詢慢、API 慢、UI 卡頓、批次耗時或 GIS 服務效能瓶頸。 |

```md
## 除錯報告

**問題分類**：{分支 A/B/C/D/E/F/G/H} - {子類別}
**根因分類標籤**：{環境問題/程式碼邏輯/資料問題/第三方 API/設定問題/相依版本/權限問題/效能問題}
**根因**：{一句話描述根本原因}
**影響範圍**：{受影響功能、資料或流程}

**修復內容**：
- `{檔案路徑}`：{修改摘要}

**驗證結果**：
- {執行的測試、命令或手動驗證}
- {是否修復原始問題}
- {是否確認沒有明顯副作用}

**未驗證/剩餘風險**：
- {無法驗證的原因，或仍需使用者確認的外部條件}
```

## 快速參考

| 技術 | 常見錯誤 | 快速檢查 |
|---|---|---|
| ArcGIS Pro SDK | `CalledOnWrongThreadException` | 存取 Map/Layer/Feature 是否包在 `QueuedTask.Run`。 |
| ArcGIS Pro SDK | CoreHost 初始化失敗 | 是否有 `[STAThread]` 與 `Host.Initialize()`。 |
| MVC/Razor | Model Binding 失敗 | 表單欄位名稱、ViewModel 屬性、`name` attribute 是否對應。 |
| Console 排程 | 手動可跑、排程失敗 | 工作目錄、帳號權限、環境變數、設定檔路徑是否不同。 |
| ArcGIS Experience | Widget 無資料 | 資料來源、圖層權限、Portal 登入、REST response 是否正常。 |
| MSSQL | Timeout | 執行計畫、索引、鎖定、查詢條件與資料量。 |
| ArcGIS REST | Token 過期或 `498` | Token 更新、referer、Portal/Server 權限。 |
| React | State 更新後 UI 未刷新 | 是否直接 mutate state，是否正確使用 setState。 |
| Python/arcpy | Schema Lock | 是否有其他程式開啟資料，物件是否釋放。 |
