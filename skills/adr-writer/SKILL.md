---
name: adr-writer
description: "撰寫 Architecture Decision Record（ADR）與技術決策紀錄。使用於使用者要求 ADR、架構決策紀錄、技術決策紀錄、記錄為什麼選這個方案、比較方案、決策背景、技術選型結論，或需要記錄 ArcGIS、.NET、MVC/Razor、Console、Python、MSSQL、API、部署、資料流與維護取捨時。"
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
