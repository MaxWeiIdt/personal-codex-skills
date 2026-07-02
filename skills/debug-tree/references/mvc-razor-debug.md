# MVC / Razor 除錯

使用於 `MVC`、`Razor`、Controller、ViewModel、Model Binding、表單送出、View 顯示或路由問題。

## 分類流程

| 表現 | 優先檢查 |
|---|---|
| Action 沒被打到 | route、HTTP method、form action、area、controller/action 名稱。 |
| Model Binding 失敗 | input `name`、ViewModel 屬性、nullable、collection index、anti-forgery。 |
| View 顯示空值 | Controller 是否填 model、View 使用的 model type、partial view 傳值。 |
| Submit 後資料消失 | validation fail 後是否重建下拉選單/清單資料。 |
| 404/500 | route table、view path、exception log、IIS rewrite。 |

## Razor 常見檢查

- `@model` 型別是否與 Controller 傳入一致。
- `asp-for` 是否對應正確屬性。
- partial view 是否需要自己的 model。
- `ViewBag`/`ViewData` key 是否拼字一致。
- 表單是否缺少 `method="post"` 或 anti-forgery token。

## MVC + MSSQL

- `DbContext` 生命週期是否為 `Scoped`。
- Lazy loading 或 navigation property 是否造成 N+1。
- 查詢條件是否因空字串/null 導致查不到資料。
