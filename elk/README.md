# ELK Stack 整合 MySQL 專案

## 專案簡介
本專案利用 ELK Stack（Elasticsearch、Logstash、Kibana）將 MySQL 資料庫中的資料同步至 Elasticsearch，並可透過 Kibana 進行資料查詢與視覺化。

## 架構說明
- **Elasticsearch**：儲存與索引資料。
- **Logstash**：從 MySQL 資料庫擷取資料，並寫入 Elasticsearch。
- **Kibana**：提供網頁介面查詢與視覺化 Elasticsearch 資料。

## 安裝與啟動步驟
1. **請先確認已安裝 [Docker](https://www.docker.com/) 及 [Docker Compose](https://docs.docker.com/compose/)。**
2. **修改 MySQL 連線資訊**：
   - 請編輯 `logstash.conf` 檔案中的 `jdbc_connection_string`、`jdbc_user`、`jdbc_password` 等欄位，填入您自己的 MySQL 伺服器資訊。
3. **啟動服務**：
   ```bash
   docker compose up -d
   ```
4. 啟動後，服務會自動依照 `logstash.conf` 設定，將 MySQL 資料同步至 Elasticsearch。

## 服務連接埠
- **Elasticsearch**：
  - 9200（HTTP API）
  - 9300（內部通訊）
- **Logstash**：
  - 5044（Beats 輸入，預設未啟用）
  - 9600（監控 API）
- **Kibana**：
  - 5601（Web UI）

## 注意事項
- 預設 Logstash 會連線至 `logstash.conf` 中設定的 MySQL 伺服器，請務必確認連線資訊正確。
- 若需更改同步的資料表或欄位，請修改 `logstash.conf` 內的 SQL 語句。
- 預設 Elasticsearch 及 Kibana 未啟用帳號密碼驗證，僅供開發測試用途，請勿用於生產環境。

## 參考檔案
- `docker-compose.yaml`：定義 ELK Stack 服務。
- `logstash.conf`：Logstash 資料擷取與同步設定。
- `mysql-connector-j-8.0.33.jar`：MySQL JDBC 驅動程式。

---