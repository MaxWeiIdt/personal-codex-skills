# Environment Setup Template

```md
# {專案名稱} 環境建置說明

## 1. 專案型態

| 項目 | 說明 |
|---|---|
| 技術棧 |  |
| 執行方式 | 本機 / IIS / 排程 / ArcGIS Pro / Python |
| 主要相依 |  |

## 2. 必要軟體

| 軟體 | 建議版本 | 用途 | 備註 |
|---|---|---|---|

## 3. 設定檔與環境變數

| 設定 | 位置 | 範例 | 說明 |
|---|---|---|---|

## 4. 安裝與還原相依

```powershell
# dotnet restore / npm install / pip install 等
```

## 5. 資料庫與外部服務

| 相依 | 用途 | 權限/連線需求 | 驗證方式 |
|---|---|---|---|

## 6. 啟動方式

| 情境 | 命令或入口 | 注意事項 |
|---|---|---|

## 7. 驗證方式

| 驗證項目 | 命令/操作 | 預期結果 |
|---|---|---|

## 8. 常見問題

| 問題 | 可能原因 | 處理方式 |
|---|---|---|

## 9. 維護注意

- 
```

## 技術棧檢查提示

| 技術 | 應檢查 |
|---|---|
| MVC/Razor | .NET SDK、IIS/IIS Express、`appsettings.json`、DB connection、前端資源。 |
| C# Console | .NET runtime、排程帳號、工作目錄、NLog、輸入/輸出路徑。 |
| ArcGIS Pro SDK | ArcGIS Pro 版本、SDK 版本、Add-in 安裝、`QueuedTask` 相關 runtime。 |
| CoreHost | ArcGIS Pro 授權、CoreHost 初始化、GDB/FeatureClass 路徑。 |
| Python | Python 版本、`venv`/`conda`、`requirements.txt`、排程使用的 executable。 |
| MSSQL | connection string、schema、權限、migration、測試資料。 |
