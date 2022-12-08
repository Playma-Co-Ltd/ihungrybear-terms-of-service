# RDS

以專案 udb-crm 為例

1. 創建資料庫
2. 資料庫 engine 選擇 PostgresSQL
3. 根據專案創建時選擇的 PostgresSQL 版本選擇對應版本
4. 依據測試區或是正式區決定是否選用多可用區數據庫實例
5. 修改数据库实例标识符

> 命名規則：{project-name}-{type}-rds
udb-crm 的測試區密鑰名稱：udb-crm-dev-rds

6. 產生一組 16 位數密碼並輸入主密碼
7. 實例配置選擇 t 類中的 db.t3.micro
8. 存儲類型選擇 通用型 SSD(gp2)
9. 儲存空間 20 GiB
10. 建立新的安全群組給 rds 使用

> {project-name}-{service}-security-group
udb-crm 的 RDS 安全群組：udb-crm-rds-security-group

11. 新增入站規則，類型選擇 PostgresSQL
12. 配置新建立的安全群組給 RDS
13. 創建資料庫
