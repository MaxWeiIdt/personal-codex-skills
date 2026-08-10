---
name: pr-description-writer
description: "根據 git diff、commit 或使用者描述撰寫 Pull Request 描述。只在使用者明確要求 PR description、Pull Request 說明、變更摘要、reviewer checklist 或合併請求內容時使用；不要因為工作區存在 diff 或剛完成修改就自動啟動。程式碼風險審查請使用 dev-review-checklist。"
---

# PR Description Writer

## 目的

使用此 Skill 將程式變更整理成 reviewer 容易閱讀的 PR 描述。輸出要能回答：為什麼改、改了什麼、怎麼驗證、有哪些風險、部署或資料是否受影響。

## 使用流程

1. 優先讀取目前 git diff、 staged diff 或使用者提供的 diff。
2. 若沒有 diff，根據使用者描述與檔案清單撰寫，並標示「未檢視 diff」。
3. 判斷變更類型：bug fix、feature、refactor、docs、config、test、data flow、deployment。
4. 使用 `references/pr-template.md` 的格式輸出。
5. 不要誇大沒有做的測試；未執行就寫「未執行」並說明原因。

## 撰寫規則

- 使用繁體中文，技術名詞與檔名保留原文。
- 摘要要短，細節放在 bullets。
- 驗證步驟要列實際命令、手動檢查或 validator 結果。
- 若涉及 ArcGIS、MSSQL、排程、API、Python 環境或部署設定，必須列入風險與注意事項。
- 若 PR 只有文件變更，也要說明是否影響 runtime。

## 交接資訊

PR 描述結尾固定加入以下區塊；這只是手動交接摘要，不代表自動啟動下一個 Skill：

- 目前階段：草稿、待 Review、待合併或待部署。
- 已完成／已確認：變更目的、修改範圍與驗證結果。
- 尚未確認／待決：Reviewer、部署環境或業務尚未確認事項。
- 重要假設：版本、設定、資料與執行環境前提。
- 主要風險：Runtime、資料、相容性、部署與回滾風險。
- 建議下一步：Reviewer checklist、部署驗證或回滾準備。
- 相關產物：Commit、測試命令、Build／Release 成品與設定變更。
