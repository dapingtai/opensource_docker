# ITUBE_QA_SERVER

這是一個開發用的伺服器，用於反向代理提供前後端服務。

## 目錄結構
```
project/
├── conf.d/      # 處放各站點或服務的 Nginx 設定檔
├── includes/    # 共用 Nginx 設定片段（如 header、proxy 等）
├── log/         # 存放 Nginx 存取與錯誤日誌
├── modules/     # Nginx 動態模組
└── ssl/         # SSL 憑證與私鑰

```

## 伺服器資訊
- IP 地址: `10.62.172.119`
- 預設端口: `80` (HTTP) 或 `443` (HTTPS)

## 功能
1. **反向代理**：將請求代理至後端服務（如 Node.js 或 API 伺服器）。
2. **靜態檔案服務**：直接提供前端靜態檔案。
3. **負載均衡**（可選）：支援多後端實例的負載均衡。

## 安裝與執行
1. 安裝 Nginx：
   ```bash
   sudo apt update && sudo apt install nginx
   ```
2. 啟動 Nginx：
   ```bash
   sudo systemctl start nginx
   ```
3. 設定反向代理：
   - 編輯 `conf.d/` 內的設定檔，或引用 `includes/` 片段。
   - 重載設定：
     ```bash
     sudo nginx -s reload
     ```

## 環境變數
Nginx 設定通常直接寫在配置文件中，但可透過環境變數動態調整：
- 在主設定檔中使用 `env` 指令載入變數。

## 開發指南
1. **修改設定**：
   - 更新 `conf.d/` 或 `includes/` 內的設定檔。
   - 測試設定語法：
     ```bash
     sudo nginx -t
     ```
2. **日誌檢查**：
   - 查看即時日誌：
     ```bash
     tail -f log/access.log
     ```

## 貢獻
歡迎提交 Pull Request 或回報問題。

## 授權
MIT License