# Enterprise Geodatabase / SDE 選型補充

當需求涉及 Enterprise Geodatabase、SDE 直連、MSSQL 中的 GDB/FeatureClass、ArcGIS Server 發布、dbo/sde schema 或 Pro SDK 讀寫資料表時，使用此補充判斷。

## 1. 選型核心

| 情境 | 優先技術 | 判斷原因 |
|---|---|---|
| 只需要查詢或編輯已發布的 FeatureServer/MapServer | `ArcGIS REST API` | 避免客製程式直接耦合 GDB schema 與 ArcGIS runtime。 |
| 需要在 ArcGIS Pro UI 中選圖層、操作 FeatureClass、配合 DockPane 流程 | `ArcGIS Pro SDK Add-in + MSSQL/GDB/SDE` | 工作流程依賴 Pro 專案狀態、MapView、Layer、Selection 或 Pro SDK API。 |
| 無 UI 但需批次讀寫 GDB、FeatureClass、GP Tool | `Console CoreHost + MSSQL/SDE`，必要時搭配 `Python` | 保留 ArcGIS runtime 能力，同時適合排程與批次。 |
| 只讀寫一般業務資料表，不需要 geodatabase 行為 | `C# Console + MSSQL` 或 `MVC + MSSQL` | 可使用一般 MSSQL schema 與資料存取，不必引入 Pro SDK/CoreHost。 |
| 需要組態式 Web GIS 展示 | `ArcGIS Experience + ArcGIS REST API` | 若流程能由已發布服務與 widget 完成，維護成本較低。 |

## 2. dbo-schema 與 sde-schema 判斷

Enterprise Geodatabase 在第一次 `Create Enterprise Geodatabase` 或 `Enable Enterprise Geodatabase` 時就決定系統核心表位置。

| 架構 | 系統核心表 | 適用情境 | 注意事項 |
|---|---|---|---|
| dbo-schema | `dbo.GDB_ITEMS`、`dbo.GDB_ITEMTYPES` | 小型專案、DBA/GIS 管理同一團隊、權限集中 | GIS 管理者通常需要較高 DB 權限。 |
| sde-schema | `sde.GDB_ITEMS`、`sde.GDB_ITEMTYPES` | 正式環境、政府/企業、DBA 與 GIS 管理權責分離 | `sde` 不可授予 `sysadmin`，避免物件落到 dbo。 |

重要原則：

- 新增 `sde` 使用者不會把既有 dbo-schema geodatabase 轉成 sde-schema。
- 判斷現況時要看 `GDB_ITEMS` 等系統表實際 schema，不只看是否存在 `sde` 使用者。
- 程式碼與 SQL 應使用完整限定名稱，例如 `sde.LayerName` 或 `dbo.LayerName`。

## 3. 開發判斷

| 問題 | 對架構的影響 |
|---|---|
| 程式是否需要新增/編輯 FeatureClass？ | 需要確認 ArcGIS 權限、OBJECTID 配號、`i..._get_ids` 類型預存程序權限。 |
| 程式是否只需要查詢資料？ | 可優先評估 REST API 或一般 MSSQL 查詢，避免不必要的 GDB 直連。 |
| 是否需要版本化、多人編輯、服務發布？ | 優先清楚設計 sde-schema 與帳號權限。 |
| Web 系統是否直接連 SDE？ | 通常應由後端服務封裝；若只需服務資料，優先 REST API。 |
| 排程是否要直連 FeatureClass？ | 優先 `Console CoreHost + MSSQL/SDE`，並補足 log、重跑與權限檢查。 |

## 4. 維護風險

- dbo/sde schema 混用會造成 ArcGIS 系統檢查、升級或程式查表失敗。
- `sde` 帳號若加入 `sysadmin`，SQL Server 可能以 dbo 身分建立物件。
- 一般帳號具備 `db_datawriter` 不代表一定能新增 feature，可能仍缺少 ArcGIS 內部預存程序執行權限。
- Pro SDK 或 CoreHost 讀不到資料表時，先查實際 schema，再查權限。
- 若後續需要 Traditional Versioning、Branch Versioning、Utility Network、Parcel Fabric 或多人維護，初期就要重視 sde-schema 與帳號設計。

## 5. 建議釐清問題

1. 目前 Enterprise Geodatabase 是 dbo-schema 還是 sde-schema？
2. 程式需要直連 FeatureClass/GDB，還是只要查詢已發布 REST service？
3. 是否需要新增或編輯 feature？使用哪個帳號執行？
4. 是否涉及 ArcGIS Server 發布、多人編輯或版本化？
5. 程式要在 ArcGIS Pro UI、背景排程，還是 Web 後端中執行？
