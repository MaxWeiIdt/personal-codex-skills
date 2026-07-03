# PR Description Template

```md
## Summary

- 

## Changes

- 

## Validation

- 

## Risk / Impact

- Runtime impact:
- Data impact:
- Deployment impact:
- Rollback notes:

## Reviewer Notes

- 
```

## 變更類型提示

| 類型 | Summary 應回答 |
|---|---|
| Feature | 新增了什麼能力，使用者或系統得到什麼結果。 |
| Bug fix | 修掉什麼錯誤，錯誤原因與影響範圍。 |
| Refactor | 行為是否不變，結構如何調整，測試如何證明。 |
| Docs | 文件涵蓋什麼，是否影響程式執行。 |
| Config | 哪些環境或部署設定改變。 |
| Data flow | 輸入、轉換、輸出或資料庫寫入是否改變。 |

## Validation 常用項目

- `dotnet build`
- `dotnet test`
- Python unit tests
- Codex skill validator
- 手動操作 MVC/Razor 頁面
- ArcGIS Pro Add-in 手動流程
- API 查詢結果比對
- MSSQL 查詢或 migration 檢查
