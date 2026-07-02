# 環境與部署問題

使用於本機可跑但部署失敗、排程失敗、IIS 問題、Python/ArcGIS 環境差異、網路連線問題。

## 判斷流程

1. 比對成功環境與失敗環境。
2. 確認執行帳號、工作目錄、環境變數、權限。
3. 確認設定檔是否部署到正確位置。
4. 確認外部服務、DB、Portal、檔案路徑是否可達。

## 常見情境

| 情境 | 優先檢查 |
|---|---|
| IIS 部署失敗 | AppPool .NET 版本、Hosting Bundle、`web.config`、資料夾權限。 |
| ArcGIS Pro Add-in 部署 | `.esriAddinX`、well-known folder、Pro/SDK 版本。 |
| Python 環境 | conda/venv、ArcGIS Pro Python、套件版本、執行 Python 路徑。 |
| 連線問題 | 防火牆、DNS、VPN、Proxy、TLS/憑證。 |
