---
name: adr-writer
description: "撰寫 Architecture Decision Record（ADR）與技術決策紀錄。只在使用者明確要求記錄架構決策、比較方案、保存技術取捨或產出 ADR 時使用；不要因為需求提到 ArcGIS、MSSQL、API 或資料流就自動啟動。單純選型請使用 tech-decision-tree，單純開發拆解請使用 development-plan-writer。"
---

# ADR Writer

## 目的

使用此 Skill 將技術選型與架構決策整理成可追溯文件。ADR 要記錄「當時為什麼這樣選」，而不是只描述最後結果。

## 使用流程

1. 先確認決策主題、背景、限制與候選方案。
2. 若使用者已用 `tech-decision-tree` 產生選型，將其結論轉成 ADR。
3. 使用 `references/adr-template.md`。
4. 若資訊不足，列出 assumptions；不要虛構不存在的限制或數據。
5. 明確寫出 consequences：好處、代價、後續維護責任。

## 撰寫規則

- 使用繁體中文。
- 標題用 `ADR-YYYYMMDD-{topic}` 或使用者指定格式。
- 狀態使用 `Proposed`、`Accepted`、`Deprecated`、`Superseded`。
- 方案比較要包含不採用原因。
- 對 ArcGIS/MSSQL/排程/API 類決策，要明確列出資料流、部署與維護風險。

## 交接資訊

文件結尾固定加入以下區塊；這只是手動交接摘要，不代表自動啟動下一個 Skill：

- 目前階段：`Proposed`、`Accepted` 或其他狀態。
- 已完成／已確認：決策內容、採用方案與不採用方案。
- 尚未確認／待決：需要業務、PM 或環境確認的事項。
- 重要假設：本 ADR 建立時採用的前提。
- 主要風險：技術、資料、部署與維護風險。
- 建議下一步：例如使用 `development-plan-writer` 拆解實作。
- 相關產物：ADR 檔案、比較表、驗證紀錄或參考文件。
