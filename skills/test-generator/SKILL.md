---
name: test-generator
description: "根據既有程式碼、diff 或功能描述規劃並產生測試案例。使用於使用者要求產生測試、補單元測試、整合測試、測試案例、coverage 缺口、MVC Controller 測試、Console service 測試、Python 資料處理測試、MSSQL 測試資料、ArcGIS REST API 測試、排程流程測試或 mock data 時。"
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
