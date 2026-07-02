# 編譯與建置錯誤

使用於 build、restore、compile、package、前端 bundling 或專案參照失敗。

## 判斷流程

1. 讀完整錯誤訊息，不只看最後一行。
2. 找第一個真正錯誤，忽略後續連鎖錯誤。
3. 判斷專案類型：`C#`、`Python`、`React/HTML`。
4. 檢查最近變更：套件版本、專案參照、目標框架、檔案名稱大小寫。

## C# 常見錯誤

| 錯誤 | 優先檢查 |
|---|---|
| `CS0246` 找不到型別 | NuGet 是否安裝、project reference、`using`、namespace。 |
| `CS0103` 名稱不存在 | 變數宣告範圍、拼字、partial class、Razor generated code。 |
| `CS1061` 沒有包含定義 | API/SDK 版本、extension method namespace、型別是否錯用。 |
| `MSB` 系列錯誤 | `.csproj`、TargetFramework、SDK 版本、build action。 |

## Python 常見錯誤

| 錯誤 | 優先檢查 |
|---|---|
| `ModuleNotFoundError` | venv/conda 是否啟用、套件是否安裝、執行 Python 路徑。 |
| `SyntaxError` | Python 版本、縮排、括號/引號配對。 |
| `ImportError` | module path、circular import、`__init__.py`。 |

## React / HTML 常見錯誤

| 錯誤 | 優先檢查 |
|---|---|
| Module not found | `node_modules`、`package.json`、路徑大小寫、alias 設定。 |
| JSX/TSX 語法錯誤 | import/export、Babel/TypeScript 設定、元件名稱。 |
| CSS/asset 載入失敗 | 相對路徑、public path、檔案大小寫。 |

## C# 特別檢查清單

- `.csproj` 的 `TargetFramework` 是否正確，例如 `net48`、`net6.0`、`net8.0`。
- ArcGIS Pro SDK 專案的 `ProVersion` 是否與本機 ArcGIS Pro 版本相容。
- 是否需要先執行 restore。
- 多專案 solution 的 project reference 是否指向正確專案。
