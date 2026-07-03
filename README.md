# Personal Codex Skills

這個 repository 用來集中管理個人日常開發會用到的 Codex Skills。內容以 ArcGIS、.NET、MVC/Razor、Console 批次、MSSQL、技術文件與除錯流程為主，讓不同專案可以重複套用同一套工作方式。

## Skills

| Skill | 用途 |
|---|---|
| `tech-decision-tree` | 協助依任務需求選擇技術組合，例如 `C# Console`、`MVC/Razor`、`React`、`ArcGIS Pro SDK`、`ArcGIS REST API`、`ArcGIS Experience`、Enterprise Geodatabase/SDE、`Python`、`MSSQL`。 |
| `wiki-doc-writer` | 依程式特徵撰寫技術 wiki 文件，支援 ArcGIS Pro SDK、Console/批次程式、MVC/Razor Web 系統、Python 腳本與資料處理專案。 |
| `debug-tree` | 依錯誤類型進行除錯與根因分析，涵蓋 build/runtime/logic/performance、MVC/Razor、ArcGIS、MSSQL、Console 排程等情境。 |
| `dev-review-checklist` | 依 ArcGIS、.NET、MVC/Razor、Console、Python、MSSQL 專案慣例進行 code review 與風險檢查。 |
| `pr-description-writer` | 根據 diff 或修改內容產生 GitHub Pull Request 描述、驗證方式與風險說明。 |
| `adr-writer` | 將技術選型、架構取捨與方案比較整理成 ADR 技術決策紀錄。 |
| `environment-setup-writer` | 整理專案必要軟體、設定檔、啟動方式、外部相依與環境驗證清單。 |
| `test-generator` | 依程式碼與風險產生單元測試、整合測試、資料處理與排程測試建議。 |

## Repository Structure

```text
personal-codex-skills/
|-- AGENTS.md
|-- README.md
|-- skills/
|   |-- adr-writer/
|   |-- debug-tree/
|   |-- dev-review-checklist/
|   |-- environment-setup-writer/
|   |-- pr-description-writer/
|   |-- tech-decision-tree/
|   |-- test-generator/
|   `-- wiki-doc-writer/
`-- .gitignore
```

每個 skill 目錄通常包含：

```text
SKILL.md
agents/openai.yaml
references/
```

- `SKILL.md`：Skill 的主要說明、觸發條件與執行規則。
- `agents/openai.yaml`：在 Codex 介面中顯示的名稱、描述與預設提示。
- `references/`：較長的模板、決策框架、除錯流程或範例內容。

## Skill Locations

本 repository 的 skill 開發位置：

```text
skills/<skill-name>/
```

Codex 實際載入的個人 skill 位置：

```text
$CODEX_HOME/skills/<skill-name>/
```

建議先在本 repository 的 `skills/` 目錄維護與版本控管，確認 validator 通過後，再同步到 Codex 個人 skills 目錄。

## Usage

在 Codex 對話中可以直接指定 skill 名稱，例如：

```text
請使用 $tech-decision-tree 幫我規劃這個 GIS Web 系統的技術選型。
```

```text
請使用 $wiki-doc-writer 幫我整理這個 MVC Controller 的 wiki 文件。
```

```text
請使用 $debug-tree 協助追查這個 ArcGIS REST API 回傳異常。
```

Codex 也會依 `AGENTS.md` 與 skill metadata，在符合條件時自動選用對應 skill。

## Validation

修改 skill 後可使用 Codex skill validator 檢查格式：

```powershell
$env:PYTHONUTF8='1'
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/tech-decision-tree
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/wiki-doc-writer
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/debug-tree
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/dev-review-checklist
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/pr-description-writer
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/adr-writer
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/environment-setup-writer
python "$env:CODEX_HOME/skills/.system/skill-creator/scripts/quick_validate.py" skills/test-generator
```

預期輸出：

```text
Skill is valid!
```

## Review Gate

本專案的 `AGENTS.md` 規定：修改程式、設定、技術文件或流程時，需要執行 Gemini Review Gate。`.codex/review/` 是本機 review 狀態資料夾，已在 `.gitignore` 中排除，不會放入 repository。

若某次任務需要跳過 review，必須在同一則任務訊息中明確加入：

```text
[SKIP_GEMINI_REVIEW]
```

## Maintenance Notes

- 新增 skill 時，請先放在本 repository 的 `skills/<skill-name>/`。
- 需要讓 Codex 立即使用時，再同步到 `$CODEX_HOME/skills/<skill-name>/`。
- Skill 名稱建議使用 hyphen-case，例如 `debug-tree`。
- 長篇模板與參考資料請放在 `references/`，避免 `SKILL.md` 過度膨脹。
- 更新 skill 後，請至少執行對應 skill 的 validator。
- 若要同步到 GitHub，建議先確認 `git status` 乾淨，再設定 remote 與 push。
