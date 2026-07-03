---
name: environment-setup-writer
description: "為專案產生開發環境、執行環境與設定檔說明。使用於使用者要求環境建置、開發環境說明、setup guide、環境設定、.env 範本、appsettings 設定、Python venv、requirements、NLog、MSSQL connection、ArcGIS Pro SDK、CoreHost、MVC/Razor、Console 排程、部署前檢查或新電腦如何跑專案時。"
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
