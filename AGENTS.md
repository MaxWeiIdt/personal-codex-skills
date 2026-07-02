# 專案協作指引

## Wiki 文件撰寫

若需要撰寫或整理 Pro SDK / Console wiki 技術文件，請使用 `$wiki-doc-writer` Skill。

此 Skill 會依關鍵字與程式特徵判斷文件格式：

| 情境 | 使用格式 |
|---|---|
| ArcGIS Pro SDK、ViewModel、DockPane、Add-in、MapView、QueuedWorker、FeatureLayer | ArcGIS Pro SDK 的 ViewModel / DockPane / Add-in 類別文件格式 |
| Console、批次程式、排程程式、`Program.Main`、`appsettings.json`、NLog、API 查詢 | 一般 Console / 批次執行程式文件格式 |

若兩種特徵同時存在且無法判斷主文件對象，請先詢問使用者要使用哪一種格式。

詳細的觸發關鍵字、章節架構與寫作規則，請以 `skills/wiki-doc-writer/` 內的 Skill 定義為準。
