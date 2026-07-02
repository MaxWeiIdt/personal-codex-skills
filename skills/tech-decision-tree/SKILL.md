---
name: tech-decision-tree
description: "建立技術選型決策樹與架構建議，且僅使用使用者允許的技術堆疊：C# Console、MVC 全端、Python、ArcGIS Pro SDK for C#（包含 Add-in、Python、Console CoreHost）、ArcGIS Experience、ArcGIS REST API、React、HTML、Razor 與 MSSQL。當需要評估新專案需求、選擇 Web 系統、批次程式、ArcGIS Pro Add-in、Web GIS、REST 整合，或需要說明架構判斷原因時使用。"
---

# 技術選型決策樹

## 目的

使用此 Skill 評估新需求，產出維護導向的技術選型建議。回覆必須限制在允許的技術堆疊內，並同時說明「推薦結果」與「如何判斷」。

不要推薦使用者未列出的技術。若需求看起來需要其他技術，請改用最接近的允許組合，並明確說明限制。

## 允許技術堆疊

| 類別 | 可用技術 |
|---|---|
| 後端與框架 | `C# Console`、`MVC`、`Python` |
| GIS | `ArcGIS Pro SDK for C#`、`ArcGIS Pro Add-in`、`ArcGIS Pro Python`、`Console CoreHost`、`ArcGIS Experience`、`ArcGIS REST API` |
| 前端 | `React`、`HTML`、`Razor` |
| 資料庫 | `MSSQL` |

## 執行流程

1. 先閱讀需求，判斷是否需要 UI、背景執行、GIS 整合、外部 API、資料持久化，以及部署或執行環境限制。
2. 只有在缺少資訊會改變架構選擇時，才追問最多 3 個釐清問題；否則直接建立假設並清楚標示。
3. 依下列維度分類需求：
   - `UI 型態`：無 UI、一般網頁、ArcGIS Pro 桌面端、Web GIS、ArcGIS Experience。
   - `執行模式`：互動式操作、排程批次、一次性批次、桌面端輔助流程。
   - `GIS 深度`：無 GIS、僅 REST 查詢、Web GIS 互動、ArcGIS Pro/GDB/FeatureClass/GP 層級。
   - `前端複雜度`：簡單 CRUD/報表、複雜互動/狀態管理、地圖優先互動。
   - `資料核心`：MSSQL 業務資料、GIS service 資料、GDB/FeatureClass、檔案或腳本輸出。
4. 選擇能滿足需求的最小可維護架構。
5. 說明接近但未採用的替代方案，以及不選的原因。
6. 使用繁體中文輸出，保留技術名稱原文。

## 輸出格式

除非使用者指定其他格式，請使用以下結構：

````md
# 技術選型建議：{需求名稱}

## 1. 推薦組合

| 層面 | 建議技術 | 責任 |
|---|---|---|

## 2. 判斷路徑

```text
新需求
|-- ...
```

## 3. 關鍵判斷依據

| 問題 | 判斷 | 對選型的影響 |
|---|---|---|

## 4. 不建議路徑

| 替代方案 | 不選原因 |
|---|---|

## 5. 維護注意事項

- ...
````

若使用者要求草案或 Outline，請使用較短結構：

```md
# 技術選型決策樹草案

## 1. 關鍵決策節點
## 2. 決策樹框架
## 3. 技術組合推薦路徑範例
## 4. 後續細化方向
```

## 判斷規則

當使用者要求完整決策樹、路徑範例、比較矩陣，或需要較完整的選型說明時，讀取 `references/decision-framework.md`。

快速判斷原則：

| 需求特徵 | 優先選擇 |
|---|---|
| 無 UI，需排程或可重複批次 | `C# Console + MSSQL` |
| 腳本式資料清洗或 GIS 前處理 | `Python`，必要時由 `C# Console` 協調 |
| 一般內部網頁系統 | `MVC + Razor/HTML + MSSQL` |
| 複雜網頁互動或狀態管理 | `React + MVC + MSSQL` |
| 客製化 Web 地圖互動 | `React + ArcGIS REST API + MVC + MSSQL` |
| 使用既有服務快速組 Web GIS | `ArcGIS Experience + ArcGIS REST API` |
| ArcGIS Pro 桌面 UI 或地圖/圖層選取整合 | `ArcGIS Pro SDK Add-in + DockPane/ViewModel` |
| 無 UI 但需 ArcGIS Pro/GDB/FeatureClass/GP 自動化 | `Console CoreHost + MSSQL`，必要時搭配 `Python` |

## 說明標準

- 先給最終推薦組合，再說明判斷細節。
- 技術名稱、框架名稱、產品名稱保留原文並使用 inline code。
- 用問題路徑說明判斷，不要只寫「偏好某技術」。
- 需求資訊不足時，列出使用的假設。
- 視需求補上維護風險，例如 GIS schema 變更、REST service 可用性、排程可觀測性、MSSQL schema 耦合、ArcGIS Pro runtime 限制。
- 除非使用者明確改成要實作，否則不要寫程式碼。
