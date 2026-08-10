---
name: environment-setup-writer
description: "為專案產生開發環境、執行環境與設定檔說明。只在使用者明確要求 setup guide、環境建置、新電腦如何執行、設定檔說明或環境前置檢查時使用；不要因為程式出現 appsettings、Python、ArcGIS 或 MSSQL 就自動啟動。正式部署與回滾應使用部署流程 skill。"
---

# Environment Setup Writer

## 目的

使用此 Skill 產生維護導向的環境建置文件與設定清單，讓開發者能知道需要安裝什麼、設定什麼、如何驗證環境是否可執行。

## 使用流程

1. 先判斷專案型態：MVC/Razor、C# Console、ArcGIS Pro SDK、Python、MSSQL、React 或混合型。
2. 掃描專案中的 `.sln`、`.csproj`、`appsettings.json`、`NLog.config`、`package.json`、`requirements.txt`、`pyproject.toml`、`.env.example`、README。
3. 使用 `references/setup-template.md` 產出文件。
4. 不要寫入真實密碼、token、connection string；只提供 placeholder。
5. 若資訊不足，明確標示「需依實際環境確認」。

## 輸出重點

- 必要軟體與版本。
- 設定檔與環境變數。
- 資料庫與 ArcGIS 相依。
- 本機啟動與排程啟動方式。
- 驗證命令與常見環境問題。
- 不要把 setup guide 寫成操作手冊；重點放在專案可維護的環境知識。

## 交接資訊

環境文件結尾固定加入以下區塊；這只是手動交接摘要，不代表自動啟動下一個 Skill：

- 目前階段：開發環境、測試環境、排程環境或部署前檢查。
- 已完成／已確認：必要版本、設定檔、相依套件與驗證命令。
- 尚未確認／待決：內網、ArcGIS、MSSQL、帳號權限或機密設定。
- 重要假設：作業系統、SDK、執行帳號與外部服務前提。
- 主要風險：版本差異、路徑、權限、Token、Connection String 與環境漂移。
- 建議下一步：例如使用部署流程或 `debug-tree` 處理環境問題。
- 相關產物：setup guide、設定範本、安裝命令與驗證紀錄。
