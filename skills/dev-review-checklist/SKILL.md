---
name: dev-review-checklist
description: "依專案技術棧進行開發審查與 review checklist。使用於使用者要求 code review、程式碼審查、review checklist、幫我檢查修改、檢查風險、上 PR 前檢查，或需要檢查 ArcGIS Pro SDK、ArcGIS REST API、MVC/Razor、C# Console、Python、MSSQL、NLog、排程、API 流程、資料處理流程的 bug、維護風險、安全性、重跑風險與缺漏測試時。"
---

# 開發審查 Checklist

## 目的

使用此 Skill 對程式修改或既有專案做維護導向 review。審查重點不是格式潔癖，而是找出會造成錯誤資料、排程失敗、GIS 行為異常、部署問題、安全風險、效能退化或後續難維護的問題。

## 使用流程

1. 先確認審查範圍：使用者指定的檔案、diff、PR、功能流程或整個專案。
2. 若有 git diff，優先審查實際變更；若沒有 diff，審查使用者提供的檔案與流程。
3. 判斷技術棧並讀取對應 reference：
   - 通用與輸出格式：`references/review-checklist.md`
   - ArcGIS / MVC / Console / Python / MSSQL 等專項風險也在同一份 reference 中分節。
4. 先列 findings，依嚴重度排序；每一項都要有檔案/行號或明確程式位置。
5. 若找不到問題，明確說「未發現阻擋性問題」，並列出仍未驗證的風險或測試缺口。

## 審查輸出格式

使用以下順序：

1. Findings
2. Open Questions / Assumptions
3. Verification
4. Summary

Findings 必須包含：

| 欄位 | 說明 |
|---|---|
| Severity | `Blocking`、`High`、`Medium`、`Low` |
| Location | 檔案與行號，或可定位的方法/流程 |
| Issue | 問題說明 |
| Impact | 實際可能造成的錯誤 |
| Suggested Fix | 修正方向 |

## 判斷原則

- 優先找正確性、資料一致性、安全、排程重跑、部署與測試缺口。
- 不要只列個人偏好的命名或排版問題。
- 不要要求大重構，除非目前結構已經導致明確 bug 或高風險。
- 若需要執行測試、build 或查詢檔案，先依現有 repo 工具與慣例操作。
- 對 ArcGIS Pro SDK，要特別注意 `QueuedTask`、UI thread、GDB lock、圖層狀態與事件取消。
- 對 MVC/Razor，要特別注意 model binding、validation、權限、CSRF、SQL/查詢條件與前後端欄位同步。
- 對 Console/Python 排程，要特別注意重跑 idempotency、log、exit code、環境路徑、設定與憑證。
