# 技術選型決策框架

當使用者要求完整決策樹、架構建議、技術比較矩陣，或希望建立可重複使用的新專案評估清單時，使用此參考文件。

## 1. 核心問題

| 問題 | 若是 | 若否 |
|---|---|---|
| 系統是否需要使用者介面 UI？ | 繼續判斷 UI 型態。 | 優先走批次或腳本路徑。 |
| 是否需要排程或無人值守執行？ | 優先考慮 `C# Console + MSSQL`；只有在需要 GIS 能力時才加入 GIS 工具。 | 優先判斷互動式 Web、桌面端或一次性腳本。 |
| 是否必須在 ArcGIS Pro 桌面端內執行？ | 有 UI 時優先 `ArcGIS Pro SDK Add-in`；無 UI 自動化時優先 `Console CoreHost`。 | 繼續判斷 Web GIS 或非 GIS 路徑。 |
| GIS 是否只需要讀取已發布服務？ | 優先 `ArcGIS REST API`；再依 UI 複雜度搭配 `React`、`MVC` 或 `ArcGIS Experience`。 | 若需要 GDB、FeatureClass 或 GP Tool，優先 ArcGIS Pro SDK/CoreHost。 |
| 是否需要直連 Enterprise Geodatabase/SDE 或 FeatureClass？ | 優先考慮 `ArcGIS Pro SDK Add-in`、`Console CoreHost + MSSQL/SDE`，並確認 dbo/sde schema 與權限。 | 若只需查詢已發布服務，避免直連 GDB，改走 `ArcGIS REST API`。 |
| UI 是否主要是 CRUD、查詢、報表或管理後台？ | 優先 `MVC + Razor/HTML + MSSQL`。 | 繼續評估 `React` 或 ArcGIS 專用 UI。 |
| UI 是否以地圖為核心且互動複雜？ | 優先 `React + ArcGIS REST API + MVC + MSSQL`。 | 可考慮 `MVC + Razor/HTML` 或 `ArcGIS Experience`。 |
| Web GIS 是否能以組態方式完成？ | 優先 `ArcGIS Experience + ArcGIS REST API`。 | 優先 `React + ArcGIS REST API`。 |
| `MSSQL` 是否為系統紀錄中心？ | 架構中納入 `MSSQL`，並明確定義同步、查詢與狀態保存責任。 | 將 GIS service、GDB 或檔案視為主要資料來源。 |

## 2. 決策樹

```text
新需求
|
|-- 是否需要 UI？
|   |
|   |-- 否
|   |   |
|   |   |-- 是否需要排程或無人值守執行？
|   |   |   |
|   |   |   |-- 是
|   |   |   |   |
|   |   |   |   |-- 是否需要 ArcGIS Pro/GDB/FeatureClass/GP 能力？
|   |   |   |   |   |-- 是：Console CoreHost + MSSQL/SDE，必要時搭配 Python
|   |   |   |   |   |-- 否：C# Console + MSSQL
|   |   |   |
|   |   |   |-- 否
|   |   |       |
|   |   |       |-- 是否偏向腳本式檔案、資料或 GIS 前處理？
|   |   |           |-- 是：Python，必要時搭配 MSSQL
|   |   |           |-- 否：C# Console + MSSQL
|   |
|   |-- 是
|       |
|       |-- UI 是否在 ArcGIS Pro 裡執行？
|       |   |-- 是：ArcGIS Pro SDK Add-in + DockPane/ViewModel + MSSQL/GDB/SDE
|       |
|       |-- 是否為一般網頁系統？
|       |   |
|       |   |-- 前端是否主要是 CRUD、查詢、報表或管理後台？
|       |   |   |-- 是：MVC + Razor/HTML + MSSQL
|       |   |
|       |   |-- 是否需要複雜互動或前端狀態管理？
|       |       |-- 是：React + MVC + MSSQL
|       |
|       |-- 是否為 Web GIS 或地圖優先系統？
|           |
|           |-- 是否能以既有 widget 或頁面組態完成？
|           |   |-- 是：ArcGIS Experience + ArcGIS REST API
|           |
|           |-- 是否需要客製化地圖行為或流程？
|               |-- 是：React + ArcGIS REST API + MVC + MSSQL
```

## 3. 推薦路徑

| 路徑 | 需求特徵 | 建議組合 | 判斷原因 |
|---|---|---|---|
| 批次資料處理 | 無 UI、可重複執行、輸入來源為 API/檔案/資料庫、需保存狀態 | `C# Console + MSSQL` | 部署單純，排程邊界清楚，適合長期維護批次流程。 |
| 批次 GIS 自動化 | 無 UI，但需要 GDB、FeatureClass、GP 或 ArcGIS Pro 能力 | `Console CoreHost + MSSQL`，必要時搭配 `Python` | 在不建立 DockPane UI 的前提下保留 ArcGIS Pro SDK 操作能力。 |
| Enterprise Geodatabase / SDE 直連 | 需要讀寫 FeatureClass、確認 dbo/sde schema、走 geodatabase 權限與版本化資料 | `Console CoreHost + MSSQL/SDE` 或 `ArcGIS Pro SDK Add-in + MSSQL/GDB/SDE` | 直連 GDB/SDE 時要處理 schema、權限、OBJECTID 配號與 ArcGIS runtime 限制；若只是查詢已發布服務，應優先 REST API。 |
| 腳本式資料/GIS 前處理 | 一次性轉換、檔案處理多、流程偏程序式 | `Python`，必要時搭配 `MSSQL` | 適合快速處理資料或 GIS 前處理，不需要完整 C# 應用程式外殼。 |
| 一般 Web 後台 | CRUD、查詢、審核、報表、頁面結構穩定 | `MVC + Razor/HTML + MSSQL` | 前後端維持在同一架構內，適合一般業務系統維護。 |
| 複雜 Web 系統 | 豐富互動、元件化畫面、前端狀態較多 | `React + MVC + MSSQL` | 將複雜前端行為與後端業務/資料責任分開。 |
| 客製化 Web GIS | 地圖優先 UI、客製圖層互動、Feature 查詢、需串接業務資料 | `React + ArcGIS REST API + MVC + MSSQL` | 支援客製地圖互動，同時由 MSSQL 保存業務資料。 |
| 組態式 Web GIS | 已有 ArcGIS services、偏展示/查詢/儀表板、客製需求有限 | `ArcGIS Experience + ArcGIS REST API` | 當組態即可滿足流程時，這是最快且維護成本較低的路徑。 |
| ArcGIS Pro 桌面工具 | DockPane、MapView、圖層選取、GDB 編輯/檢查/輸出 | `ArcGIS Pro SDK Add-in + MSSQL/GDB`，必要時搭配 `Python` | 工作流程依賴 ArcGIS Pro UI、專案狀態或桌面端圖層互動時必須使用。 |

## 4. 說明檢查表

產出選型建議時，至少包含下列內容：

| 章節 | 必要內容 |
|---|---|
| 推薦組合 | 列出每個被選用技術與責任分工。 |
| 判斷路徑 | 用決策樹或編號流程說明從需求到結果的判斷過程。 |
| 關鍵判斷依據 | UI 需求、執行模式、GIS 深度、前端複雜度、資料來源、持久化需求。 |
| 不建議路徑 | 列出 1 到 3 個接近替代方案，說明為何不選。 |
| 維護注意事項 | Schema、服務可用性、排程、log、ArcGIS runtime 限制、部署責任。 |

## 5. 邊界規則

- 不引入 Vue、Node.js、Java、PostgreSQL、QGIS、雲端服務或 message queue。
- 當 `MVC + Razor/HTML` 已足夠時，不要額外推薦前端框架。
- 純 server-side 或無人值守 Web 流程，不要推薦 `ArcGIS Pro SDK Add-in`。
- 若需求依賴高度客製 UI 狀態或非標準流程，不要推薦 `ArcGIS Experience` 作為主要架構。
- 若流程需要長期排程、結構化狀態追蹤與 MSSQL 中心化操作，不要把 `Python` 當成整體架構；可將它定位為處理輔助工具。
- 當需求需要持久化業務資料、稽核狀態或跨次執行歷史時，預設資料庫為 `MSSQL`。
- 當需求需要直連 Enterprise Geodatabase/SDE 時，不要只用一般 MSSQL 表思維判斷；需確認 geodatabase schema、ArcGIS 權限、FeatureClass 行為與是否需要 Pro SDK/CoreHost。

## 6. 釐清問題

只有在缺少資訊會影響技術選型時，才需要追問：

1. 主要使用者互動是在瀏覽器、ArcGIS Pro，還是完全沒有 UI？
2. 流程是否需要排程或背景自動執行？
3. GIS 處理是否需要 ArcGIS Pro、GDB、FeatureClass、GP Tool 能力，還是只需要查詢已發布的 REST 服務？
4. 地圖介面是否能用 ArcGIS Experience 組態完成，還是需要客製化互動流程？
5. 若使用 Enterprise Geodatabase，資料庫是 dbo-schema 還是 sde-schema？程式是否需要直連 FeatureClass？
6. `MSSQL` 是否為主要資料來源與系統紀錄中心，還是 GIS service/GDB 才是主要資料來源？
