# 範例與檢查情境

需要契約範例或檢查邊界時使用。以下為虛構的自有 API 設計提案，不是既有系統現況，也不是 ArcGIS REST 的標準格式；不要直接套用到使用者的服務。

## 部分更新：省略、null 與空字串

提案：`PATCH /api/tasks/{taskId}` 更新任務備註。

- `taskId`：必填字串，範例為 `task-demo-001`。
- `note`：可省略、允許 null；字串長度為 1–200，空字串及只有空白的字串不接受。
- 省略 `note` 表示不修改；明確傳 null 表示清空。
- 成功使用 HTTP 200，body 必定包含 `taskId` 與 `note`；`note` 可為 null。
- 認證方式、資料存取範圍與其他錯誤規則仍待介接雙方確認。

清空備註的 request body：

```json
{"note": null}
```

成功 response body，HTTP 200：

```json
{"taskId": "task-demo-001", "note": null}
```

不合法的 request body：

```json
{"note": ""}
```

驗證失敗 response body，HTTP 400；此情境不修改原資料：

```json
{
  "error": {
    "code": "INVALID_NOTE",
    "field": "note",
    "message": "note 不接受空字串或只有空白的字串；清空請傳 null。"
  }
}
```

這份片段刻意保留未確認項目，不能當作可直接交付的完整 endpoint 契約。未確認的認證、查無資料與併發更新規則應持續列為待決事項。

## 新增 response enum 值

若既有呼叫端只接受 `status = Pending | Completed`，新增 `PartialFailed` 可能造成反序列化或分支處理失敗。應先盤點呼叫端對未知值的處理，列出驗證或過渡方案；沒有證據前標示「相容性待確認」。

## Skill 使用情境檢查

這些是人工檢查情境與預期行為，不代表已執行行為測試；新增或修改 skill 後可用它們檢視是否偏離目的。

| 使用者要求／輸入 | 預期行為 |
|---|---|
| 明確指定 `$api-contract-designer`，要求替前端定義查詢 API，但沒有程式碼 | 產出提案，列出欄位、回應與待確認項目；不宣稱現有服務已支援。 |
| 明確指定本 skill，提供 Controller 與 DTO，要求整理現有契約 | 引用實際來源，保留既有格式；程式未顯示的限制列為待確認。 |
| 明確指定本 skill，要求把既有 response 包成新 envelope | 列出變更前後與呼叫端相容性影響，不把新格式描述成現況。 |
| 明確指定本 skill，要求設計部分更新欄位 | 分開描述省略、null、空字串，範例與規則一致。 |
| 明確指定本 skill，提供 HTTP 200 且 body 包含 error 的既有服務 | 記錄既有的失敗判讀方式，不自行改成自有 API 格式。 |
| 明確指定本 skill，要求規劃 POST 逾時後重試 | 區分結果未知與明確失敗，確認去重或結果查詢能力，不保證直接重試安全。 |
| 未指定本 skill，只說「API 回傳 500，幫我找原因」 | 遵守手動指定政策，不自動載入本 skill；工作焦點是除錯。 |
| 未指定本 skill，只要求排程 ETL 重跑規劃或 Controller Wiki | 不因 API 關鍵字載入本 skill；工作焦點分別是資料流程或文件。 |
