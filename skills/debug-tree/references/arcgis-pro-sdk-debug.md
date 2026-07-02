# ArcGIS Pro SDK 除錯

使用於 `ArcGIS Pro SDK`、Add-in、DockPane、ViewModel、`Console CoreHost`、Python Bridge、GP Tool、GDB/FeatureClass 操作問題。

## Add-in / DockPane

| 錯誤 | 優先檢查 |
|---|---|
| `CalledOnWrongThreadException` | 存取 Map、Layer、Feature、GDB 是否包在 `QueuedTask.Run`。 |
| DockPane/Button 不顯示 | `Config.daml` 的 id、className、category、assembly 是否對應。 |
| Add-in 載入失敗 | Pro 版本、SDK 版本、`.esriAddinX`、well-known folder。 |
| UI 卡住 | 是否在 UI thread 做長時間工作；是否缺少 async/await 或 progress。 |

## CoreHost

| 錯誤 | 優先檢查 |
|---|---|
| 初始化失敗 | 是否有 `[STAThread]` 與 `Host.Initialize()`。 |
| COMException | ArcGIS Pro 是否安裝、授權是否有效、執行平台是否 x64。 |
| DLL 載入失敗 | `AnyCPU` vs `x64`、Pro 安裝路徑、runtime 相依。 |

## GDB / FeatureClass

- `Geodatabase`、`FeatureClass`、`Table` 是否在正確 thread 使用。
- 是否用 `using` 或明確 Dispose 釋放資源。
- schema lock 是否來自未釋放 cursor、row、table 或外部程式。
- 編輯資料是否使用 `EditOperation`。

## Python / GP Tool

| 錯誤 | 優先檢查 |
|---|---|
| `arcpy` 匯入失敗 | 是否使用 ArcGIS Pro Python 環境。 |
| `ExecuteError` | GP Tool 參數、座標系統、輸入資料、schema lock。 |
| Python exit code 非 0 | 收集 stdout、stderr、exit code、執行路徑。 |
