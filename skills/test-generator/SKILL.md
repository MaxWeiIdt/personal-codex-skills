---
name: test-generator
description: "根據既有程式碼、diff 或功能描述規劃並產生測試案例。只在使用者明確要求測試案例、補測試、單元／整合測試、coverage 缺口、mock data 或測試命令時使用；不要因為程式有修改、資料流程或 API 就自動啟動。實際錯誤分析請使用 debug-tree，程式碼審查請使用 dev-review-checklist。"
---

# Test Generator

## 目的

使用此 Skill 為既有程式或修改規劃測試。重點是補上最有風險的路徑，而不是機械式追求覆蓋率。

## 使用流程

1. 先判斷測試目標：新功能、bug fix、重構、資料處理流程、API、UI controller、排程。
2. 找出可測邊界：pure function、service、repository、controller、CLI entry、資料轉換、API client。
3. 使用 `references/test-strategy.md` 選擇測試類型。
4. 若要直接新增測試，優先沿用 repo 既有測試框架與命名慣例。
5. 若無法直接寫測試，輸出測試計畫、mock data 與手動驗證清單。

## 測試設計原則

- 先測高風險分支：錯誤輸入、空資料、schema 變更、API 失敗、重跑、權限、交易失敗。
- 不要為 private implementation detail 寫脆弱測試；優先測 public behavior。
- 測試資料要小而能表達情境。
- 對排程與資料寫入流程，要測重跑是否 idempotent。
- 對 MVC/Razor，要測 model validation 與 service 呼叫結果。
- 對 Python ETL，要測欄位缺漏、型別轉換、空值、輸出 schema。

## 輸出格式

若未直接改檔，輸出：

1. Suggested Test Scope
2. Test Cases
3. Mock Data
4. Commands
5. Remaining Risk

## 交接資訊

測試規劃結尾固定加入以下區塊；這只是手動交接摘要，不代表自動啟動下一個 Skill：

- 目前階段：測試規劃、測試撰寫、執行中或已完成。
- 已完成／已確認：測試範圍、案例、Mock Data 與執行命令。
- 尚未確認／待決：未覆蓋的分支、測試資料、外部服務或環境需求。
- 重要假設：輸入資料、資料庫、API、ArcGIS 與排程環境前提。
- 主要風險：未覆蓋的資料一致性、重跑、權限、效能或整合風險。
- 建議下一步：實作測試、執行測試，或交給 `dev-review-checklist` 檢查。
- 相關產物：測試檔案、Mock Data、命令、Coverage 與測試結果。
