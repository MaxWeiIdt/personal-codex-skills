---
name: wiki-doc-writer
description: "依指定觸發關鍵字或程式特徵，為 ArcGIS Pro SDK ViewModel/DockPane/Add-in 類別、一般 Console/批次執行程式、MVC/Razor 網頁系統，或 Python 腳本/資料處理專案撰寫繁體中文 wiki 技術文件。使用於使用者輸入 Pro SDK wiki、ProSDK文件、ViewModel wiki、DockPane文件、ArcGIS Pro ViewModel、Pro SDK ViewModel、Console wiki、Console文件、批次程式文件、排程程式文件、一般Console、Console開法、MVC wiki、MVC文件、Razor wiki、Razor文件、Web系統文件、後台系統文件、Python wiki、Python文件、Python專案文件、腳本文件、資料處理文件、ETL文件、排程Python，或要求幫我寫 wiki、幫我整理文件時。"
---

# Wiki 技術文件撰寫

## 目的

使用此 Skill 將程式碼、專案片段或使用者提供的功能描述整理成維護導向 wiki 文件。文件目標是讓後續維護者快速理解「這支程式負責什麼、資料從哪裡來、流程怎麼跑、哪些規則不能改、發生錯誤要看哪裡」。

輸出一律使用繁體中文。程式碼中的類別名稱、方法名稱、設定鍵、欄位名稱、狀態碼、路徑與技術名稱保留原名，不要翻譯。

## 觸發與格式選擇

| 使用者輸入或程式特徵 | 使用格式 |
|---|---|
| `Pro SDK wiki`、`ProSDK文件`、`ViewModel wiki`、`DockPane文件`、`ArcGIS Pro ViewModel`、`Pro SDK ViewModel` | ArcGIS Pro SDK 的 ViewModel / DockPane / Add-in 類別文件格式 |
| `Console wiki`、`Console文件`、`批次程式文件`、`排程程式文件`、`一般Console`、`Console開法` | 一般 Console / 批次執行程式文件格式 |
| `MVC wiki`、`MVC文件`、`Razor wiki`、`Razor文件`、`Web系統文件`、`後台系統文件`、`網站文件` | MVC / Razor 網頁系統文件格式 |
| `Python wiki`、`Python文件`、`Python專案文件`、`腳本文件`、`資料處理文件`、`ETL文件`、`排程Python` | Python / 腳本 / 資料處理專案文件格式 |
| 使用者只說「幫我寫 wiki」或「幫我整理文件」，且程式包含 `DockPane`、`ViewModel`、`ArcGIS Pro UI`、`MapView`、`MapSelectionChangedEvent`、`QueuedWorker`、`FeatureLayer` | ArcGIS Pro SDK 的 ViewModel / DockPane / Add-in 類別文件格式 |
| 使用者只說「幫我寫 wiki」或「幫我整理文件」，且程式包含 `Program.Main`、`appsettings.json`、`NLog`、排程、批次流程、API 查詢、Console 執行 | 一般 Console / 批次執行程式文件格式 |
| 使用者只說「幫我寫 wiki」或「幫我整理文件」，且程式包含 `Controller`、`ActionResult`、`IActionResult`、`View`、`Razor`、`cshtml`、`ViewModel`、`ModelState`、`Html.BeginForm`、`asp-for`、`DbContext`、`Services`、`Repositories` | MVC / Razor 網頁系統文件格式 |
| 使用者只說「幫我寫 wiki」或「幫我整理文件」，且程式包含 `.py`、`requirements.txt`、`pyproject.toml`、`conda`、`venv`、`argparse`、`pandas`、`requests`、`arcpy`、`logging`、`schedule`、`cron` | Python / 腳本 / 資料處理專案文件格式 |
| 多種特徵同時存在且無法判斷主文件對象 | 先詢問使用者要使用哪一種格式 |

## 使用流程

1. 先判斷文件對象是 Pro SDK 類別、Console/批次程式、MVC/Razor 網頁系統、Python 腳本/資料處理專案，或多種特徵皆有。
2. 若判斷為 Pro SDK 文件，讀取 `references/pro-sdk-wiki-template.md`。
3. 若判斷為 Console 文件，讀取 `references/console-wiki-template.md`。
4. 若判斷為 MVC/Razor 文件，讀取 `references/mvc-razor-wiki-template.md`。
5. 若判斷為 Python 文件，讀取 `references/python-wiki-template.md`。
6. 若資訊不足但可合理推斷，直接撰寫並在文件中標示「程式碼中未明確顯示」或「需依實際設定確認」。
7. 若關鍵格式會受影響，例如同時有 DockPane、`Program.Main`、MVC Controller 與 Python pipeline，先詢問使用者要用哪一種文件格式。
8. 撰寫時優先說明維護者需要知道的責任、資料流、狀態規則、錯誤情境與風險。

## 通用撰寫規則

- 文件開頭使用 `# 類別或程式名稱`。
- 第一段用 1 到 2 段說明程式定位、主要責任、輸入來源、輸出結果，以及不負責的範圍。
- 章節使用編號格式，例如 `## 1. 功能概要`、`## 2. 使用情境`。
- 優先使用表格整理「功能/說明」、「方法/責任」、「設定鍵/用途」、「狀態碼/意義」、「錯誤情境/處理方式」。
- 涉及程式碼、路徑、設定鍵、欄位名稱、方法名稱、狀態碼時，使用 Markdown inline code，例如 `Program.Main`、`appsettings.json`、`Status_StereoPair`。
- 涉及 SQL 條件、路徑、命令、程式碼片段時，使用 fenced code block，並標明語言，例如 `csharp`、`python`、`sql`、`powershell`、`text`。
- 文件重點放在維護理解，不要寫成使用者操作手冊，也不要只描述表面功能。
- 若程式有 ArcGIS、GDB、FeatureClass、GP Tool、Python、API、NLog、檔案輸出，必須明確列出資料流與相依關係。
- 若有狀態碼、商業規則、空間查詢規則、欄位回寫規則，必須獨立成章，避免埋在流程描述裡。
- 最後必須有「路徑與檔案」以及「維護風險/維護建議」章節。

## 寫作語氣

- 使用清楚、直接、維護導向的繁體中文。
- 不要過度口語，也不要寫成教學文。
- 每一節都要回答「這段程式為什麼存在」與「維護時要注意什麼」。
- 若程式碼中有明確方法名稱、欄位名稱、路徑或狀態碼，保留原名，不要翻譯掉。

## 品質檢查

輸出前檢查：

- 文件是否明確說明程式責任與不負責範圍。
- 是否交代資料來源、輸出結果、外部相依與資料流。
- 是否將狀態碼、商業規則、空間查詢規則或欄位回寫規則獨立說明。
- 是否包含錯誤處理或例外情境。
- 是否包含「路徑與檔案」與「維護風險/維護建議」。
- 是否避免推薦與文件對象無關的操作手冊內容。
