---
name: pr-description-writer
description: "根據 git diff、commit、修改檔案或使用者描述撰寫 Pull Request 描述。使用於使用者要求 PR 描述、PR description、pull request 說明、幫我寫 PR、整理變更摘要、GitHub PR 內容、合併請求描述，或需要產生變更目的、修改內容、驗證方式、風險、部署注意事項與 reviewer checklist 時。"
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
