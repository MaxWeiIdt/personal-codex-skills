# GitHub Actions CI 與 Release 自動化技術指南

本文件提供跨專案共用的 GitHub Actions CI 與 Release 自動化做法，涵蓋一般 .NET MVC／Razor／Console、Python，以及依賴 ArcGIS Pro 的 .NET 專案。目標是讓開發人員能依專案環境選擇合適的 Runner，完成自動建置、測試、打包與 GitHub Release 發布。

> 本文件的 Release 流程會將成品發布到 GitHub Releases，但不會直接部署至正式伺服器。若要自動部署 IIS、排程主機或其他正式環境，應另外設計 CD、權限控管、人工核准與回復機制。

## 1. 名詞與責任邊界

| 名詞 | 說明 | 本文件涵蓋 |
|---|---|---|
| CI（Continuous Integration） | 自動還原依賴、建置、檢查與執行測試 | 是 |
| Package | 將通過 CI 的程式整理為 ZIP、Wheel 或其他交付格式 | 是 |
| GitHub Release | 將 Tag、版本說明與成品集中保存於 GitHub | 是 |
| CD（Continuous Delivery／Deployment） | 將成品送至測試或正式環境 | 僅說明原則，不直接實作 |
| Runner | 實際執行 workflow 工作的機器 | 是 |

只有在 Tag 發布時執行 build/test，較接近「發版驗證」。若希望每次修改都及早發現問題，建議另外建立 PR／push CI。

## 2. 標準流程架構

### 2.1 建議的完整流程

```text
開發者修改程式
    |
    v
Push / Pull Request
    |
    v
CI：Restore -> Build -> Test
    |
    v
程式碼合併至主要分支
    |
    v
建立並 Push 新 Tag
    |
    v
Release：Build -> Test -> Package
    |
    v
建立 GitHub Release 並上傳成品
    |
    v
人工或獨立 CD 流程部署至目標環境
```

### 2.2 最小導入流程

若專案目前尚未建立 CI，可先使用單一 Tag workflow：

```text
Push Tag
-> Restore
-> Build
-> Test
-> Package
-> GitHub Release
```

這個做法較容易導入，但正式建立 Tag 前，仍需由開發人員在本機確認基本品質。

## 3. Runner 選擇方式

| 專案類型 | 建議 Runner | 適用原因 |
|---|---|---|
| 跨平台 .NET MVC／Web API／Console | `ubuntu-latest` | 啟動快速，適合一般 .NET 專案 |
| 依賴 Windows 的 .NET 專案 | `windows-latest` | 支援 `net8.0-windows`、Windows API 等需求 |
| 一般 Python／ETL | `ubuntu-latest` | Python、pip 與常用工具整合容易 |
| Python 依賴 Windows 元件 | `windows-latest` 或 self-hosted | 依賴 COM、Office Driver 或內部程式時使用 |
| ArcGIS Pro CoreHost／Add-in | self-hosted Windows | 需要 ArcGIS Pro、SDK 或本機 DLL |
| 需連內網 MSSQL／檔案伺服器 | self-hosted | GitHub-hosted runner 通常無法直接存取內網 |

選擇原則：

1. 一般專案優先使用 GitHub-hosted runner。
2. 只有特殊軟體、內網或授權依賴才使用 self-hosted runner。
3. 不要將 self-hosted runner 安裝在正式環境主機。
4. self-hosted runner 應限制只執行受信任 Repository 的 workflow。

## 4. Repository 基本結構

GitHub Actions workflow 必須放在 Repository 的以下路徑：

```text
專案根目錄
|-- .github/
|   `-- workflows/
|       |-- ci.yml
|       `-- release.yml
|-- src/
|-- tests/
`-- README.md
```

建議職責：

| 檔案 | 觸發方式 | 責任 |
|---|---|---|
| `ci.yml` | push、Pull Request | build、test、靜態檢查 |
| `release.yml` | push Tag | build、test、package、GitHub Release |

## 5. Tag 與版本規範

建議使用 Semantic Versioning 格式：

```text
v1.0.0
v1.1.0
v1.1.1
```

版本意義：

| 位置 | 意義 | 範例 |
|---|---|---|
| Major | 不相容變更 | `v1.0.0` -> `v2.0.0` |
| Minor | 向下相容的新功能 | `v1.0.0` -> `v1.1.0` |
| Patch | 向下相容的錯誤修正 | `v1.0.0` -> `v1.0.1` |

建立與推送 Tag：

```powershell
git tag v1.0.0
git push origin v1.0.0
```

注意事項：

- Workflow 必須先 commit 並 push，再建立新 Tag。
- Re-run 舊 Tag 會使用該 Tag 建立當下的 workflow，不會使用主要分支上的新版 workflow。
- 已發布的 Tag 原則上不應移動或覆寫，修正時應建立新版本。

## 6. 一般 .NET MVC／Razor／Console

### 6.1 事前需求

- Repository 內包含 `.sln` 或 `.csproj`。
- 專案使用 GitHub-hosted runner 可取得的 .NET SDK。
- 測試專案已加入 Solution，例如 `MyProject.Tests`。
- 專案機密不得寫入 `appsettings.json` 並提交至 Git。

### 6.2 PR／Push CI 範本

建立 `.github/workflows/ci.yml`：

```yaml
name: .NET CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v6

      - name: Set up .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "8.0.x"

      - name: Restore
        run: dotnet restore MyProject.sln

      - name: Build
        run: dotnet build MyProject.sln --configuration Release --no-restore

      - name: Test
        run: dotnet test MyProject.sln --configuration Release --no-build
```

需要替換：

- `MyProject.sln`：實際 Solution 名稱。
- `8.0.x`：專案實際使用的 SDK 版本。
- 若專案為 `net8.0-windows` 或使用 Windows API，將 `ubuntu-latest` 改為 `windows-latest`。

### 6.3 Tag Release 範本

建立 `.github/workflows/release.yml`：

```yaml
name: .NET Release

on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

jobs:
  build-test-release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout release tag
        uses: actions/checkout@v6
        with:
          ref: ${{ github.ref }}

      - name: Set up .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "8.0.x"

      - name: Restore
        run: dotnet restore MyProject.sln

      - name: Build
        run: dotnet build MyProject.sln --configuration Release --no-restore

      - name: Test
        run: dotnet test MyProject.sln --configuration Release --no-build

      - name: Publish
        run: dotnet publish src/MyProject/MyProject.csproj --configuration Release --output publish

      - name: Package
        run: |
          cd publish
          zip -r "../MyProject_${{ github.ref_name }}.zip" .

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v3
        with:
          files: MyProject_${{ github.ref_name }}.zip
          generate_release_notes: true
```

此範本使用第三方 Release Action。正式導入前應依團隊供應鏈政策評估，必要時將版本標籤改為經審核的完整 commit SHA。

### 6.4 .NET 測試判讀

`dotnet test` 只會執行 Solution 中可識別的測試專案。如果專案沒有 xUnit、NUnit 或 MSTest 測試專案，即使步驟成功，也不代表業務邏輯已經被測試。

在 GitHub Actions log 中確認：

```text
Total tests: 10
Passed: 10
Failed: 0
```

若沒有 `Total tests` 或測試數為 0，應補上測試專案。

## 7. Python／ETL／資料處理專案

### 7.1 事前需求

- 使用 `requirements.txt`、`pyproject.toml` 或其他明確的依賴檔。
- 測試放在 `tests/`，並可透過 `pytest` 執行。
- API token、資料庫密碼使用 GitHub Secrets。
- 測試資料應為可重建的小型資料，不應直接使用正式資料。

### 7.2 PR／Push CI 範本

建立 `.github/workflows/ci.yml`：

```yaml
name: Python CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v6

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: pip

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest

      - name: Run tests
        run: pytest
```

若 `pytest` 已列在開發依賴中，不需在 workflow 重複安裝。

### 7.3 Python Script／ETL Tag Release 範本

```yaml
name: Python Release

on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

jobs:
  test-and-release:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout release tag
        uses: actions/checkout@v6
        with:
          ref: ${{ github.ref }}

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: pip

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest

      - name: Run tests
        run: pytest

      - name: Package scripts
        run: |
          zip -r "MyPythonProject_${{ github.ref_name }}.zip" src scripts requirements.txt README.md

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v3
        with:
          files: MyPythonProject_${{ github.ref_name }}.zip
          generate_release_notes: true
```

依實際目錄調整 `src`、`scripts`、`requirements.txt` 與 `README.md`。若專案是可安裝的 Python Package，建議使用 `python -m build` 產生 Wheel 與 source distribution，而不是壓縮原始碼。

### 7.4 Python 特殊環境

下列情況可能需要 self-hosted runner：

- `arcpy` 或 ArcGIS Pro Python 環境。
- Windows COM、Office Driver、ODBC 或內部執行檔。
- 只能由公司內網存取的 API、MSSQL 或檔案伺服器。
- 大型資料或特殊硬體資源。

此時不要在 workflow 中任意更新共用 Python 環境。建議使用專用 venv／conda 環境，並在 workflow 先輸出版本資訊。

## 8. ArcGIS Pro CoreHost／Add-in 特殊流程

### 8.1 為什麼使用 self-hosted runner

GitHub-hosted Windows runner 通常沒有：

- ArcGIS Pro。
- `ArcGIS.Core.dll`、`ArcGIS.CoreHost.dll`。
- ArcGIS Pro SDK 專案所需的 Targets 或工具。
- 公司內網的 SDE、MSSQL、Portal 或檔案路徑。

若 `.csproj` 使用以下本機參照，GitHub-hosted runner 便無法直接建置：

```xml
<Reference Include="ArcGIS.Core">
  <HintPath>C:\Program Files\ArcGIS\Pro\bin\ArcGIS.Core.dll</HintPath>
</Reference>
```

### 8.2 self-hosted runner 需求

| 項目 | 要求 |
|---|---|
| 作業系統 | Windows x64 |
| ArcGIS Pro | 已安裝，且版本與專案相容 |
| .NET SDK | 已安裝專案所需版本 |
| Git | 可取得 Repository |
| Runner | 已註冊、執行中並顯示 `Idle` |
| 網路 | 可透過 HTTPS 443 連線 GitHub |
| 安全 | 只允許受信任 Repository 使用 |

建議將 Runner 安裝為 Windows Service。若只執行 `run.cmd`，關閉視窗或登出後 Runner 會離線。

### 8.3 建立與註冊 self-hosted runner

以下步驟在要作為建置機的 Windows 電腦執行；建議使用獨立 VM 或建置主機，而不是正式環境主機。

1. 進入 GitHub Repository 的 **Settings** -> **Actions** -> **Runners**。
2. 選擇 **New self-hosted runner**，再選擇 **Windows** 與 **x64**。
3. 在建置主機開啟 PowerShell，逐行執行 GitHub 畫面即時產生的下載、驗證與解壓指令。
4. 依 GitHub 畫面提供的指令執行 `config.cmd`，將這台主機註冊到指定 Repository。
5. 設定過程中指定可辨識的 Runner 名稱；若畫面詢問是否以服務執行，正式用途建議選擇 `Y`。
6. 回到 GitHub 的 Runners 頁面，確認這台主機顯示為 `Idle`。

Windows x64 Runner 預設會帶有 `self-hosted`、`Windows` 與 `X64` labels，因此可對應本文件 workflow 的設定：

```yaml
runs-on: [self-hosted, windows, x64]
```

若團隊新增自訂 label，例如 `arcgis-pro` 或 `intranet`，workflow 與 Runner 必須使用相同 label，否則工作會停在 `Waiting for a runner to pick up this job`。

建議建立目錄：

```powershell
mkdir C:\actions-runner
cd C:\actions-runner
```

GitHub 頁面會提供類似以下指令；請使用頁面當下產生的完整指令，不要直接複製其他文件中的下載版本或 token：

```powershell
.\config.cmd --url https://github.com/<owner>/<repository> --token <temporary-registration-token>
```

註冊 token 為短效敏感資訊，禁止提交至 Git、貼入 workflow、寫入 Wiki 範例或分享至聊天室。

若只需臨時測試，可以執行：

```powershell
.\run.cmd
```

看到 `Listening for Jobs` 代表 Runner 已開始接收工作。這種方式關閉視窗後便會停止；正式用途應依 GitHub 註冊流程所顯示的指令安裝並啟動 Runner 服務，讓它在重新開機後維持可用。

### 8.4 ArcGIS Pro self-hosted Release 範本

以下範本適合已安裝 ArcGIS Pro 與 .NET SDK 的 Windows Runner：

```yaml
name: ArcGIS Pro Build and Release

on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

defaults:
  run:
    shell: cmd

jobs:
  build-test-release:
    runs-on: [self-hosted, windows, x64]

    steps:
      - name: Checkout release tag
        uses: actions/checkout@v6
        with:
          ref: ${{ github.ref }}

      - name: Verify installed .NET SDK
        run: dotnet --info

      - name: Restore
        run: dotnet restore MyArcGISProject.sln

      - name: Build
        run: dotnet build MyArcGISProject.sln --configuration Release --no-restore

      - name: Test
        run: dotnet test MyArcGISProject.sln --configuration Release --no-build

      - name: Publish
        run: dotnet publish MyArcGISProject.csproj --configuration Release --output publish

      - name: Package
        run: tar.exe -a -c -f "MyArcGISProject_${{ github.ref_name }}.zip" -C publish .

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v3
        with:
          files: MyArcGISProject_${{ github.ref_name }}.zip
          generate_release_notes: true
```

`shell: cmd` 不是所有 self-hosted runner 的必要設定。若 Runner 主機採用 `AllSigned` PowerShell Execution Policy，GitHub Actions 產生的暫存 `.ps1` 可能因未簽署而被封鎖，此時可使用 CMD 避開 `run` 步驟的 PowerShell 腳本。

### 8.5 ArcGIS Pro 成品差異

| 專案類型 | 建議成品 |
|---|---|
| CoreHost Console | `dotnet publish` 輸出 ZIP |
| ArcGIS Pro Add-in | `.esriAddInX` |
| Managed Configuration | `.proConfigX` |
| Plugin Datasource | `.esriPlugin` |

Add-in 不應直接套用 Console 的 `dotnet publish` 打包方式，應以上述 ArcGIS 專案實際產物作為 Release asset。

## 9. GitHub Secrets 與權限

### 9.1 最小權限

只進行 build/test：

```yaml
permissions:
  contents: read
```

需要建立 GitHub Release：

```yaml
permissions:
  contents: write
```

不要為方便而開啟不需要的 Repository 權限。

### 9.2 機密管理

Repository → Settings → Secrets and variables → Actions。

Workflow 使用方式：

```yaml
env:
  API_TOKEN: ${{ secrets.API_TOKEN }}
```

注意事項：

- 不要將 token、密碼、Connection String 寫入 YAML。
- 不要在 log 輸出完整 Secrets。
- 測試使用的資料庫帳號應與正式帳號分離。
- Release 建置原則上不應直接連正式資料庫。

## 10. 日常操作

### 10.1 一般開發

```powershell
git add .
git commit -m "feat: add new feature"
git push
```

若 Repository 有 `ci.yml`，push 或 Pull Request 後會自動執行 CI。

### 10.2 建立 Release

先確認：

- 主要分支已包含最新版 workflow。
- CI 已成功。
- Runner 狀態正常。
- 版本號尚未使用。

執行：

```powershell
git tag v1.0.0
git push origin v1.0.0
```

完成後到 GitHub：

```text
Actions -> 確認 workflow 成功
Releases -> 下載 Assets 中的 ZIP／套件
```

## 11. 常見問題與除錯

### 11.1 Waiting for a runner to pick up this job

可能原因：

- self-hosted runner 尚未執行。
- Runner 頁面顯示 `Offline`。
- Workflow 要求的 labels 與 Runner 不一致。
- Runner 版本過舊。
- Runner 無法連線 GitHub。

檢查：

```text
Repository
-> Settings
-> Actions
-> Runners
-> 確認狀態為 Idle
```

### 11.2 找不到 `ArcGIS` namespace 或 DLL

原因：

- 使用 GitHub-hosted runner，但專案引用 ArcGIS Pro 本機 DLL。
- self-hosted runner 沒有安裝相容的 ArcGIS Pro。
- `.csproj` 的 `HintPath` 與實際安裝路徑不同。

處理：

- 改用已安裝 ArcGIS Pro 的 self-hosted Windows runner。
- 核對 ArcGIS Pro、SDK、.NET 與專案版本。
- 若要建立無 ArcGIS Pro 的 Build Server，依 Esri 官方建置伺服器方式管理 SDK assemblies。

### 11.3 `setup-dotnet` 沒有權限寫入 `C:\Program Files\dotnet`

原因：

- self-hosted runner 帳號沒有安裝 SDK 的權限。
- 主機已安裝 SDK，但 workflow 又要求 `setup-dotnet` 安裝其他版本。

處理：

- 專用 self-hosted runner 可預先安裝固定 SDK，workflow 使用 `dotnet --info` 驗證。
- 若需要 `setup-dotnet`，應指定合適的安裝策略與 Runner 權限，不應直接提高整個 Runner 的系統權限。

### 11.4 `PSSecurityException`／PowerShell 腳本未簽署

原因：

- 主機的 PowerShell Execution Policy 為 `AllSigned`。
- GitHub Actions 的 `run` 步驟會產生暫存 `.ps1`，但該檔案沒有簽章。

處理方式之一：

```yaml
defaults:
  run:
    shell: cmd
```

若 workflow 原本使用 `Compress-Archive`，可在 Windows 改用：

```cmd
tar.exe -a -c -f "Application_v1.0.0.zip" -C publish .
```

修改系統 PowerShell Policy 前，應先確認公司資安政策。

### 11.5 修改 workflow 後 Re-run，仍執行舊內容

原因：

- Re-run Tag workflow 會讀取該 Tag 中保存的 workflow。
- 主要分支後續修改不會回寫舊 Tag。

處理：

1. Commit 並 push 新版 workflow。
2. 建立新的版本 Tag。
3. Push 新 Tag 觸發新版 workflow。

### 11.6 `dotnet test` 成功，但沒有測試結果

原因：

- Solution 沒有測試專案。
- 測試專案未加入 Solution。
- 測試 Adapter 或 SDK 設定不完整。

處理：

- 建立 xUnit、NUnit 或 MSTest 專案。
- 將測試專案加入 `.sln`。
- 在 log 確認 `Total tests` 大於 0。

### 11.7 Tag 已建立，但沒有自動產生 Release

檢查：

- Workflow 是否使用 `on.push.tags`。
- Tag 名稱是否符合 `"v*"`。
- Workflow 是否已存在於該 Tag。
- build、test 或 package 是否失敗。
- `permissions.contents` 是否為 `write`。

## 12. 導入檢查清單

### 12.1 共通

- [ ] Workflow 位於 `.github/workflows/`。
- [ ] `.sln`、`.csproj` 或 Python 路徑已換成實際名稱。
- [ ] Runner 作業系統符合專案需求。
- [ ] SDK／Python 版本與專案一致。
- [ ] CI 至少包含依賴安裝、build 與 test。
- [ ] 測試數量不是 0。
- [ ] Tag 規則與團隊版本規範一致。
- [ ] Release workflow 僅取得必要權限。
- [ ] Secrets 未寫入 Repository。

### 12.2 self-hosted runner

- [ ] Runner 使用獨立建置機或 VM。
- [ ] GitHub 顯示 Runner 為 `Idle`。
- [ ] labels 與 workflow 的 `runs-on` 一致。
- [ ] Runner 可連 GitHub HTTPS 443。
- [ ] Runner 已設定為服務或有明確啟動方式。
- [ ] ArcGIS Pro／內部工具與 SDK 已安裝。
- [ ] 僅受信任 Repository 可使用 Runner。
- [ ] 定期更新 Runner 與作業系統。

## 13. 維護原則

- 專案升級 .NET 或 Python 時，同步更新 workflow。
- GitHub Action 升級主要版本前，先閱讀 Release Notes。
- 定期檢查 self-hosted runner 是否在線、是否需要更新。
- Release 失敗時不要覆寫既有 Tag，修正後建立新版本。
- 將跨專案共通流程抽成 reusable workflow 前，先確認各專案差異已穩定。
- 若新增正式環境部署，應加入 GitHub Environment、人工核准、部署帳號最小權限與 rollback 流程。

## 14. 官方參考資料

- [GitHub Actions：Building and testing .NET](https://docs.github.com/en/actions/tutorials/build-and-test-code/net)
- [GitHub Actions：Building and testing Python](https://docs.github.com/en/actions/tutorials/build-and-test-code/python)
- [GitHub Actions：Self-hosted runners reference](https://docs.github.com/en/actions/reference/runners/self-hosted-runners)
- [GitHub Actions：Adding self-hosted runners](https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/add-runners)
- [GitHub Actions：Managing access to self-hosted runners](https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/manage-access)
- [Esri：Configure build server to build add-ins and configurations](https://pro.arcgis.com/en/pro-app/3.6/sdk/api-reference/conceptdocs/docs/ProConcepts-Advanced-Topics.html)
- [softprops/action-gh-release](https://github.com/softprops/action-gh-release)
